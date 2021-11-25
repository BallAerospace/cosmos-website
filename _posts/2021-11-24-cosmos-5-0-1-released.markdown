---
layout: news_item
title: "Ball Aerospace COSMOS 5.0.1 Released"
date: 2021-11-24 6:00:00 -0700
author: ryanmelt
version: 5.0.1
categories: [release]
---

This is the first patch release of COSMOS 5 Open Source Edition.

### Significant Changes:

- Added new notification system to frontend
- ScriptRunner now supports mnemonic autocomplete for cmd() and tlm() keywords
- Fixed redis streams that were not properly trimmed and leaking memory
- All example telemetry screens from COSMOS 4 now working in COSMOS 5
- Added ability to directly edit screens in TlmViewer
- Added save and restore configuration to TlmViewer
- Updated to use docker compose as much as possible
- Greatly improved error reporting
- Lots and Lots of Minor bug fixes and improvements

### Known Things That Aren't Done/Fully Working Yet:

- Automatic data reduction for long term trending isn't complete yet
- Limits sets are not fully implemented

### Prerequisites:

Docker - Running COSMOS 5 requires a working Docker installation. Typically Docker Desktop on Windows / Mac. Plain docker should work on linux. We're currently only developing / running with Docker Desktop on Windows, so if you have any issues on another platform, please let us know by submitting a ticket!

Minimum Resources allocated to Docker: 8GB RAM, 1 CPU, 80GB Disk
Recommended Resources allocated to Docker: 16GB RAM, 2+ CPUs, 100GB Disk

### To Run:

1. Download one of the archives (.zip or .tar.gz from the Github release page) [Download Release Here](https://github.com/BallAerospace/COSMOS/releases/tag/v5.0.1)
2. Extract the archive somewhere on your host computer
3. The COSMOS 5 containers are designed to work and be built in the presence of an SSL Decryption device. To support this a cacert.pem file can be placed at the base of the COSMOS 5 project that includes any certificates needed by your organization. If you don't need this, then please ignore, but if you see any SSL errors, this is probably why.
4. Run cosmos-control.bat start (Windows), or ./cosmos-control.sh start (linux/Mac)
5. COSMOS 5 will be built and when ready should be running (~10 mins for first run, ~3 for subsequent)
6. Connect a web browser to http://localhost:2900
7. Have fun running COSMOS 5!

We will be actively updating documentation on cosmosc2.com to capture all the details on COSMOS 5. So if it isn't documented yet, we're getting there! The biggest new documentation is on the new plugin system.

Please try it out and let us know what you think! Please submit any issues as Github tickets, or any generic feedback to COSMOS@ball.com.

Thanks!
