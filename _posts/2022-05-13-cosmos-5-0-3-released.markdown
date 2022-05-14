---
layout: news_item
title: "Ball Aerospace COSMOS 5.0.3 Released"
date: 2022-05-13 6:00:00 -0700
author: ryanmelt
version: 5.0.3
categories: [release]
---

I'm happy to announce the release of COSMOS 5.0.3!   This release updates dependencies, has a few bug fixes, and improves UI performance.
Most importantly, the user interface better handles having many thousands of telemetry points.  Everyone is encouraged to upgrade and start running off of the prebuilt containers on Docker Hub.

## What's Changed

* Script file chooser dialogs added
* Cypress Integration Tests Fully Removed (Replaced with Playwright)
* Redis Aof persistence removed (Helps Raspberry Pi a lot)
* TlmGrapher right click Improvements
* Fix get_packet API
* Disallow bracket in target, packet, packet item names
* Fix Redis command tab
* Fix Environment Variables in Test Runner
* Allow util save and load script to take version
* TargetPacketItemChooser performance improvements
* Fix Test Suite results parsing
* Paginate CmdTlmServer and PacketViewer
* inject_tlm enhancements
* Rework cleanup to be configurable and default to off
* Added Ruby block support to wait_check by @andyp13
* Lots of dependency versions updated to latest

### Migration Notes:

If upgrading from an earlier version, you will need to go the Admin -> Microservices tab and manually delete the DEFAULT__CLEANUP__S3 microservice.

### Prerequisites:

Docker - Running COSMOS 5 requires a working Docker installation.  Typically Docker Desktop on Windows / Mac.  Plain Docker or Podman also works on linux.  We actively develop and run with Docker Desktop on Mac/Windows, and Linux on Raspberry Pi, so if you have any issues on another platform, please let us know by submitting a ticket!

Minimum Resources allocated to Docker: 4GB RAM, 1 CPU, 80GB Disk
Recommended Resources allocated to Docker: 16GB RAM, 2+ CPUs, 100GB Disk

### To Run:

1. Download one of the archives (.zip or .tar.gz from the Github release page) [Download Release Here](https://github.com/BallAerospace/COSMOS/releases/tag/v5.0.3)
2. Extract the archive somewhere on your host computer
3. Run: docker-compose up -d
4. Connect a web browser to http://localhost:2900
5. Have fun running COSMOS 5!

We will be actively updating documentation on cosmosc2.com to capture all the details on COSMOS 5.  So if it isn't documented yet, we're getting there!  The biggest new documentation is on the new plugin system.

Please try it out and let us know what you think!  Please submit any issues as Github tickets, or any generic feedback to COSMOS@ball.com.

Thanks!

**Full Changelog**: https://github.com/BallAerospace/COSMOS/compare/v5.0.2...v5.0.3
