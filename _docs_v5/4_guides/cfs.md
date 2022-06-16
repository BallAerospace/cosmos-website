---
layout: docs
title: COSMOS and NASA cFS
toc: true
---

## Working configuration

This tutorial has been tested using the following components:

- Ubuntu 20.04 - minimal (fresh installation) [cFS and COSMOS on same machine,
cFS and COSMOS on different machines]
   - Docker version 20.10.7, build 20.10.7-0ubuntu1~20.04.1
   - docker-compose version 1.25.0, build unknown
- Ubuntu 18.04 - minimal (fresh installation) [cFS and COSMOS on same machine]
   - Docker version 20.10.7, build 20.10.7-0ubuntu1~18.04.1
   - docker-compose version 1.17.1, build unknown
- COSMOS v5 master-branch commit: 94c22fa570
- cFS master-branch commit: fe54f66926

Replace all `<xxxxxx>` with your matching paths and names. Example: `<USERNAME>`.

## Setting up cFS

To install NASA cFS, you can follow the instructions on
[https://github.com/nasa/cFS](https://github.com/nasa/cFS). The instructions in short:

### Installing cFS dependencies

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y \
   cmake \
   git
```

### Cloning and running cFS

```bash
git clone https://github.com/nasa/cFS.git
cd cFS
git submodule init
git submodule update

cp cfe/cmake/Makefile.sample Makefile
cp -r cfe/cmake/sample_defs sample_defs

make SIMULATION=native prep
make
make install
cd build/exe/cpu1/
./core-cpu1
```

## Setting up COSMOS

For the installation, the official [guide](/docs/v5/installation) was used.
Here are the highlights.

### Installing COSMOS dependencies

Prepare system. Install Docker (see the installation
[guide](https://phoenixnap.com/kb/how-to-install-docker-on-ubuntu-18-04)
for more details) and put your user into the group `docker`. After that, logout
and login again for the changes to take effect.

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y \
   curl \
   docker.io \
   docker-compose \
   git
sudo usermod -aG docker <USERNAME>
```

### Installing COSMOS

```bash
export PATH_TO_COSMOS=<path where to clone COSMOS>
git clone https://github.com/BallAerospace/COSMOS.git $PATH_TO_COSMOS
```

### Configuring COSMOS

Change the Docker configuration for the interoperability with NASA cFS. For 
subscribing to the telemetry, you have to append a port binding in the file 
`compose.yaml` under the section `cosmos-operator`. The port number has to 
match with the port number cFS is sending the telemetry on. 

In the COSMOS folder:
```yaml
ports:
  - "1235:1235/udp"
```

Run COSMOS, the first run takes a while (~15 min).

```bash
cd $PATH_TO_COSMOS
. cosmos-control.sh start
```

When started, connect with a browser to [http://localhost:2900](http://localhost:2900).

For shutting down COSMOS:

```bash
. cosmos-control.sh stop
```


## Creating a COSMOS plugin for TM/TC interface with cFS

The detailed instructions how to create a plugin, can be found
[here](/docs/v5/gettingstarted), in the chapter "Interfacing with Your Hardware".

Create a directory where you would like your COSMOS plugin to be stored.

```bash
mkdir cosmos-plugins
cd cosmos-plugins
```

Create a plugin with the name `SAMPLE`. `SAMPLE` is the name of the plugin and
must be in capital letters according to the COSMOS documentation. This command
should create the plugin structure.

```bash
. $PATH_TO_COSMOS/cosmos-control.sh cosmos generate plugin SAMPLE
```

Also, change the rights of this newly created plugin, because the COSMOS script
creates the plugin to be owned by the root user.

```bash
sudo chown -R <USERNAME>:<USERGROUP> cosmos-sample
```

In this newly created plugin, change the `plugin.txt` file, so that the
communication happens over UDP. `port_tm` is the port number on which cFS
sends the telemetry messages. `port_tc` indicates the port on which cFS listens to the
telecommands.

```bash
VARIABLE ip localhost
VARIABLE port_tm 1235
VARIABLE port_tc 1234
VARIABLE sample_target_name SAMPLE

TARGET SAMPLE <%= sample_target_name %>
# hostname   write_dest_port   read_port   write_src_port   interface_address   ttl   write_timeout   read_timeout   bind_address
INTERFACE <%= sample_target_name %>_INT udp_interface.rb <%= ip %> <%= port_tc %> <%= port_tm %> nil nil 128 nil nil
  MAP_TARGET <%= sample_target_name %>
```

Note that the two arguments to the `TARGET` parameter are:

1. the physical target name that should match the name of the plugin, i.e. `SAMPLE`.
This name must match the folder name in the `targets` folder. Example: for the
`SAMPLE` plugin, the target specifications must be under
`cosmos-plugins/cosmos-sample/targets/SAMPLE`. If you don't follow this
convention, the server will refuse to install your plugin at the following steps.

1. the name of your target how it is shown in the user interface.

In this example, we keep both names to be `SAMPLE`.

## Creating TM/TC definitions

Change to the target folder and remove the existing files and create own files.

```bash
cd cosmos-plugins/cosmos-sample/targets/SAMPLE/cmd_tlm
rm *
touch sample_cmds.txt
touch sample_tlm.txt
touch to_lab_cmds.txt
```

Open these newly created files in a text editor and fill them with following
content.

`to_lab_cmds.txt`:
```bash
COMMAND TO_LAB TO_LAB_ENABLE BIG_ENDIAN "TO_Lab enable telemetry"
  #                   NAME      BITS TYPE   min VAL     max VAL    init VAL  DESCRIPTION
  APPEND_ID_PARAMETER STREAM_ID  16  UINT   0x1880      0x1880     0x1880    "Stream ID"
      FORMAT_STRING "0x%04X"
  APPEND_PARAMETER    SEQUENCE   16  UINT   0xC000      MAX_UINT16 0xC000    ""
      FORMAT_STRING "0x%04X"
  APPEND_PARAMETER    PKT_LEN    16  UINT   0x0001      0xFFFF     0x0012    "length of the packet"
  APPEND_PARAMETER    CMD_ID      8  UINT   6           6          6         ""
  APPEND_PARAMETER    CHECKSUM    8  UINT   MIN_UINT8   MAX_UINT8  0x98      ""
      FORMAT_STRING "0x%2X"
  APPEND_PARAMETER    DEST_IP   144  STRING "127.0.0.1"                      "Destination IP, i.e. 172.16.9.112, pc-57"
```

<div class="note info">
  <h5>Enabling Telemetry</h5>
  <p>The command `0x1880` is needed to enable telemetry. When the cFS receives
  this command, it starts sending telemetry to the IP address provided via the
  `DEST_IP` field.</p>
</div>

`sample_cmds.txt`:
```bash
COMMAND SAMPLE SAMPLE_NOOP BIG_ENDIAN "Sample_app NOOP Command"
  # cFS primary header
  APPEND_ID_PARAMETER    STREAM_ID   16   UINT   0x1882      0x1882      0x1882      "SAMPLE Packet Identification"
      FORMAT_STRING "0x%04X"
  APPEND_PARAMETER       SEQUENCE    16   UINT   MIN_UINT16  MAX_UINT16  0xC000      ""
      FORMAT_STRING "0x%04X"
  APPEND_PARAMETER       PKT_LEN     16   UINT   0x0001      0x0001      0x0001      "Packet length"
  # cFS CMD secondary header
  APPEND_PARAMETER       CMD_ID       8   UINT   0           0           0           ""
  APPEND_PARAMETER       CHECKSUM     8   UINT   MIN_UINT8   MAX_UINT8   MIN_UINT8   ""

COMMAND SAMPLE SAMPLE_RESET BIG_ENDIAN "Sample_app Reset Counters Command"
  APPEND_ID_PARAMETER    STREAM_ID   16   UINT   0x1882      0x1882      0x1882      "SAMPLE Packet Identification"
      FORMAT_STRING "0x%04X"
  APPEND_PARAMETER       SEQUENCE    16   UINT   MIN_UINT16  MAX_UINT16  0xC000      ""
      FORMAT_STRING "0x%04X"
  APPEND_PARAMETER       PKT_LEN     16   UINT   0x0001      0x0001      0x0001      "Packet length"
  APPEND_PARAMETER       CMD_ID       8   UINT   1           1           1           ""
  APPEND_PARAMETER       CHECKSUM     8   UINT   MIN_UINT8   MAX_UINT8   MIN_UINT8   ""

COMMAND SAMPLE SAMPLE_PROCESS BIG_ENDIAN "Sample_app Process Command"
  APPEND_ID_PARAMETER    STREAM_ID   16   UINT   0x1882      0x1882      0x1882      "SAMPLE Packet Identification"
      FORMAT_STRING "0x%04X"
  APPEND_PARAMETER       SEQUENCE    16   UINT   MIN_UINT16  MAX_UINT16  0xC000      ""
      FORMAT_STRING "0x%04X"
  APPEND_PARAMETER       PKT_LEN     16   UINT   0x0001      0x0001      0x0001      "Packet length"
  APPEND_PARAMETER       CMD_ID       8   UINT   2           2           2           ""
  APPEND_PARAMETER       CHECKSUM     8   UINT   MIN_UINT8   MAX_UINT8   MIN_UINT8   ""
```

`sample_tlm.txt`:
```bash
TELEMETRY SAMPLE SAMPLE_HK BIG_ENDIAN "Sample_app housekeeping telemetry"
  #                NAME       BITS  TYPE    ID      DESCRIPTION
  APPEND_ID_ITEM   STREAM_ID   16   UINT    0x0883  "Stream ID"
    FORMAT_STRING "0x%04X"
  APPEND_ITEM      SEQUENCE    16   UINT            "Packet Sequence"
    FORMAT_STRING "0x%04X"
  APPEND_ITEM      PKT_LEN     16   UINT            "Length of the packet"
  # telemetry secondary header
  APPEND_ITEM      SECONDS     32   UINT            ""
        UNITS Seconds sec
  APPEND_ITEM      SUBSECS     16   UINT            ""
        UNITS Milliseconds ms
  # some bytes not known for what
  APPEND_ITEM      SPARE2ALIGN 32   UINT            "Spares"
  # payload
  APPEND_ITEM      CMD_ERRS     8   UINT            "Command Error Counter"
  APPEND_ITEM      CMD_CNT      8   UINT            "Command Counter"
  # spare / alignment
  APPEND_ITEM      SPARE       16   UINT            "Spares"
```

Build the plugin from the base of your plugin folder:

```bash
# cd cosmos-plugins/cosmos-sample
. $PATH_TO_COSMOS/cosmos-control.sh cosmos rake build VERSION=1.0.0
```

<div class="note info">
  <h5>Plugin versioning</h5>
  <p>Do not forget to change the version number with every build if you want to
  better distinguish between the versions of the plugin. When the version is
  seen in the plugin's .gem file name, it is easier to visualize the existing
  versions and the newly uploaded versions.</p>
</div>

<div class="note info">
  <h5>Plugin parameters</h5>
  <p>Multiple parameters are available for the plugin configuration. See the
    <a href="/docs/v5/plugins">plugin</a> page.</p>
</div>

## Uploading the plugin

After the plugin has been built, you can import the plugin in the admin area of
the page.

Connect with a browser to
[http://localhost:2900/tools/admin](http://localhost:2900/tools/admin).

Click on the clip icon and navigate to where your plugin is stored and select
the `cosmos-sample-1.0.0.xxx.gem` file. Right of the selection line click on `UPLOAD`.

When using this plugin, make sure to change the `ip` variable during uploading
to fit where cFS is running on. `port_tm` is the port number on which cFS is
sending the telemetry messages. `port_tc` indicates the port on cFS is listening
for telecommands.

Under `sample_target_name` you can change the target name of this plugin. This
step is optional as long as you are fine with your plugin showing up as `SAMPLE`.

![Plugin Variable Settings](/img/v5/guides/plugin-variables.png)

<div class="note warning">
  <h5>Port subscription</h5>
  <p>The last uploaded plugin on COSMOS will subscribe to TM on port 1235.
  Other plugins will not receive any TM anymore.</p>
</div>

<div class="note info">
  <h5>Typo errors</h5>
  <p>Presence of typos in one of the plugin files can cause problems when uploading and installing
the plugin's .gem file. Make sure your configuration is typo-free.</p>
</div>

To enable telemetry, go to the browser and connect to
[http://localhost:2900/tools/cmdsender/SAMPLE/TO_LAB_ENABLE](http://localhost:2900/tools/cmdsender/SAMPLE/TO_LAB_ENABLE)
where you should change the `DEST_IP` to the IP address that COSMOS is running
on and send the command.

Under [http://localhost:2900/tools/cmdtlmserver/tlm-packets](http://localhost:2900/tools/cmdtlmserver/tlm-packets), you should see the incoming packets.
