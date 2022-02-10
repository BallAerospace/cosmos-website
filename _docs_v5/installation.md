---
layout: docs
title: Installation
---

## Installing COSMOS

The following sections describe how to get COSMOS installed on various operating systems. A major difference between COSMOS v4 and COSMOS v5 is the usage of containers. This document should help you setup you host machine to allow you to have a running version of COSMOS in no time.

## Installing COSMOS on Host Machines

### INSTALL

Install [Docker](https://docs.docker.com/get-docker/) and install [Docker Compose](https://docs.docker.com/compose/install/).

* Minimum Resources allocated to Docker: 8GB RAM, 1 CPU, 80GB Disk
* Recommended Resources allocated to Docker: 16GB RAM, 2+ CPUs, 100GB Disk

**Note:** As of December 2021 the COSMOS Docker containers are based on the Alpine Docker image.

### DOWNLOAD

Download the latest COSMOS 5 from the Github [releases](https://github.com/BallAerospace/COSMOS/releases).

### EXTRACT

Extract the archive somewhere on your host computer.

### UPDATE

At this point if your plugin requires an external dependency you will need to update the cosmos gemspec file and rebuild the container.

An example diff of adding external gems to the cosmos/cosmos.gemspec file.
```diff
  s.add_runtime_dependency 'tzinfo-data', '~> 1'
+  s.add_runtime_dependency 'google-protobuf', '~> 3.17'
+  s.add_runtime_dependency 'protobuf'
```

### ENVIRONMENT

The COSMOS 5 containers are designed to work and be built in the presence of an SSL Decryption device. To support this a cacert.pem file can be placed at the base of the COSMOS 5 project that includes any certificates needed by your organization. **Note**: If you set the path to the ssl file in the `SSL_CERT_FILE` environment variables the cosmos-control setup script will copy it and place it for the docker container to load.

<div class="note warning">
  <h5>SSL Issues</h5>
  <p style="margin-bottom:20px;">Increasingly organizations are using some sort of SSL decryptor device which can cause curl and other command line tools like git to have SSL certificate problems. If installation fails with messages that involve "certificate", "SSL", "self-signed", or "secure" this is the problem. IT typically sets up browsers to work correctly but not command line applications. Note that the file extension might not be .pem, it could be .pem, crt, .ca-bundle, .cer, .p7b, .p7s, or  potentially something else.</p>
  <p style="margin-bottom:20px;">The workaround is to get a proper local certificate file from your IT department that can be used by tools like curl (for example mine is at C:\Shared\Ball.pem). Doesn't matter just somewhere with no spaces.</p>
  <p style="margin-bottom:20px;">Then set the following environment variables to that path (ie. C:\Shared\Ball.pem)</p>

<p style="margin-left:20px;margin-bottom:20px;">
SSL_CERT_FILE<br/>
CURL_CA_BUNDLE<br/>
REQUESTS_CA_BUNDLE<br/>
</p>

<p style="margin-bottom:20px;">
Here are some directions on environment variables in Windows:
  <a href="https://www.computerhope.com/issues/ch000549.htm">
    Windows Environment Variables
  </a>
<br/>
You will need to create new ones with the names above and set their value to the full path to the certificate file.
</p>

<p style="margin-bottom:20px;">
  At Ball please contact
  <a href="mailto:cosmos@ballaerospace.com">
    cosmos@ballaerospace.com
  </a> for assistance.
</p>
</div>

<div class="note info">
  <h5>Classified Installation</h5>
  <p style="margin-bottom:20px;">If you're building in a classified environment or want to use a private Rubygems, NPM or APK server (e.g. Nexus), you can update the following environment variables: RUBYGEMS_URL, NPM_URL, APK_URL, and more in the <a href="https://github.com/BallAerospace/COSMOS/blob/master/.env">.env</a> file. Example values:</p>

  <p style="margin-left:20px;margin-bottom:20px;">
    ALPINE_VERSION=3.15<br/>
    ALPINE_BUILD=0<br/>
    RUBYGEMS_URL=https://rubygems.org<br/>
    NPM_URL=https://registry.npmjs.org<br/>
    APK_URL=http://dl-cdn.alpinelinux.org<br/>
  </p>
</div>

### RUN

Run `cosmos-control.bat` start (Windows), or `cosmos-control.sh` start (linux/Mac).

If you see an error indicating docker daemon is not running ensure Docker and Docker compose is installed and running. If it errors please try to run `docker --version` or `docker-compose --version` and try to run the start command again. If the error continues please include the version in your issue if you choose to create one.

`cosmos-control.*` can help solve some of the short falls of docker-compose when building the containers.

`cosmos-control.*` takes multiple arguments. Run with no arguments for help. An example run of cosmos-control.bat with no arguments will show a usage guide.
```
.\cosmos-control.bat
Usage: "cosmos-control.bat" [start, stop, cleanup, build, run, deploy, util]
*  build: build the containers for cosmos
*  start: run the docker containers for cosmos
*  stop: stop the running docker containers for cosmos
*  restart: stop and run the minimal docker containers for cosmos
*  cleanup: cleanup network and volumes for cosmos
...
```

<div class="note info">
  <h5>Docker minio/mc issue</h5>
  <p style="margin-bottom:20px;">On some windows systems we have seen docker error when building the cosmosc2-minio-init container. It could be due to an issue downloading the minio mc container. To solve this you can manually pull the minio container. Open the minio-init <a href="https://github.com/BallAerospace/COSMOS/blob/master/cosmos-minio-init/Dockerfile#L1">Dockerfile</a>, note the FROM line, e.g. <code>FROM minio/mc:RELEASE.2021-11-05T10-05-06Z</code>, and manually pull the image.</p>

  <p style="margin-left:20px;margin-bottom:20px;">
    Note: Image name may be different in current <a href="https://github.com/BallAerospace/COSMOS/blob/master/cosmos-minio-init/Dockerfile#L1">Dockerfile</a><br/>
    <code>docker pull minio/mc:RELEASE.2021-11-05T10-05-06Z</code>
  </p>

  <p>This should download the minio/mc container locally and not have docker-compose pull the image on build. Now re-run cosmos-control start and continue on the cosmos adventure.</p>
</div>

### WAIT

COSMOS 5 will be built and when ready should be running (~15 mins for first run, ~2 for subsequent). Running `docker ps` can help show the running containers.

### CONNECT

Connect a web browser to http://localhost:2900.

### NEXT STEPS

Continue to [Getting Started](/docs/v5/gettingstarted).

---

### Feedback

<div class="note">
  <h5>Let us know what could be better!</h5>
  <p>
    Both using and hacking on COSMOS should be fun, simple, and easy, so if for
    some reason you find it's a pain, please <a
    href="{{ site.repository }}/issues/new/choose">create an issue</a> on
    GitHub describing your experience so we can make it better.
  </p>
</div>


