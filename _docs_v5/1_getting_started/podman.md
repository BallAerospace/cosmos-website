---
layout: docs
title: Podman
---

### COSMOS Using Rootless Podman and Docker-Compose

Podman is an alternative container technology to Docker that is actively promoted by RedHat. The key benefit is that Podman can run without a root-level daemon service, making it significantly more secure by design, over standard Docker. However, it is a little more complicated to use. These directions will get you up and running with Podman. Note that the docker-compose tool version 1.x is compatible with Podman 3.x, so that will still be used in this configuration. The following directions were written against RHEL 8.5, but should be similar on other operating systems.

1. Install Prerequisite Packages

   ```
   sudo yum groupinstall "Development Tools"
   sudo yum install podman-docker podman-plugins
   curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-Linux-x86_64 -o docker-compose
   sudo mv docker-compose /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
   ```

1. Configure Host OS for Redis

   ```
   su
   echo never > /sys/kernel/mm/transparent_hugepage/enabled
   echo never > /sys/kernel/mm/transparent_hugepage/defrag
   sysctl -w vm.max_map_count=262144
   exit
   ```

1. Start rootless podman socket service

   ```
   systemctl enable --now --user podman.socket
   ```

1. Put the following into your .bashrc file (or .bash_profile or whatever)

   ```
   export DOCKER_HOST="unix:$XDG_RUNTIME_DIR/podman/podman.sock"
   ```

1. Source the profile file for your current terminal

   ```
   source .bashrc
   ```

1. Get COSMOS - A release or the current main branch (main branch shown)

   ```
   git clone https://github.com/BallAerospace/COSMOS.git
   ```

1. Optional - Set Default Container Registry

   If you don't want podman to keep querying you for which registry to use, you can create a $HOME/.config/containers/registries.conf and modify to just have the main docker registry (or modify the /etc/containers/registries.conf file directly)

   ```
   mkdir -p $HOME/.config/containers
   cp /etc/containers/registries.conf $HOME/.config/containers/.
   ```

   Then edit the unqualified-search-registries = line to just have the registry you care about (probably docker.io)

1. Run COSMOS

   ```
   cd COSMOS
   ./cosmos-control.sh start
   ```

1. Wait until everything is built and running and then goto http://localhost:2900 in your browser
