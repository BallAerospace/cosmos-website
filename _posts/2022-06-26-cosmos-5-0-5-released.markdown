---
layout: news_item
title: "Ball Aerospace COSMOS 5.0.5 Released"
date: 2022-06-26 6:00:00 -0700
author: ryanmelt
version: 5.0.5
categories: [release]
---

I'm happy to announce the release of COSMOS 5.0.5!   

This release updates dependencies, has some important bug fixes, and improves overall performance.  It also simplifies the init container design down to a single init container.

## What's Changed

* Allow Script Runner to render UTF-8 chars by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1661
* New github action container_scans in https://github.com/BallAerospace/COSMOS/pull/1656
* Metadata constraint by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1665
* Init and minio by @ryanmelt in https://github.com/BallAerospace/COSMOS/pull/1662
* update_metadata merge existing by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1663
* Change ${PWD} to . in compose.yaml by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1668
* Add milliseconds to temp script name by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1672
* Remove v-text on vuetify components by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1671
* Add cosmos validate to check plugins by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1675
* Graph legend position by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1679
* Config stream by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1682
* Table report by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1683
* Add run_script method for buttons by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1688
* Remove old interface code for inject_tlm by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1689
* Handbooks by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1696
* Xtce support by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1699
* Create hash of screen def as key by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1702
* set needs_dependencies if there is a top level lib folder by @ryanatball in https://github.com/BallAerospace/COSMOS/pull/1703
* Admin updates by @ryanmelt in https://github.com/BallAerospace/COSMOS/pull/1707
* Table Manager upload / download support by @jmthomas in https://github.com/BallAerospace/COSMOS/pull/1704
* Logging Optimizations by @ryanmelt in https://github.com/BallAerospace/COSMOS/pull/1714

### Migration Notes:

If you are upgrading and already have a local compose.yaml file, then you will need to the init container services, like it is defined in the compose.yaml file in the release.

### Prerequisites:

Docker - Running COSMOS 5 requires a working Docker installation.  Typically Docker Desktop on Windows / Mac.  Plain Docker or Podman also works on linux.  We actively develop and run with Docker Desktop on Mac/Windows, and Linux on Raspberry Pi, so if you have any issues on another platform, please let us know by submitting a ticket!

Minimum Resources allocated to Docker: 4GB RAM, 1 CPU, 80GB Disk
Recommended Resources allocated to Docker: 16GB RAM, 2+ CPUs, 100GB Disk

### To Run:

1. Download one of the archives (.zip or .tar.gz from the Github release page) [Download Release Here](https://github.com/BallAerospace/COSMOS/releases/tag/v5.0.5)
2. Extract the archive somewhere on your host computer
3. Run: docker-compose up -d
4. Connect a web browser to http://localhost:2900
5. Have fun running COSMOS 5!

We will be actively updating documentation on https://ballaerospace.github.io/cosmos-website/ to capture all the details on COSMOS 5.  So if it isn't documented yet, we're getting there!  The biggest new documentation is on the new plugin system.

Please try it out and let us know what you think!  Please submit any issues as Github tickets, or any generic feedback to COSMOS@ball.com.  

Thanks!

**Full Changelog**: https://github.com/BallAerospace/COSMOS/compare/v5.0.4...v5.0.5
