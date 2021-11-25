---
layout: news_item
title: "Ball Aerospace COSMOS 5.0.0 Released"
date: 2021-08-14 6:00:00 -0700
author: ryanmelt
version: 5.0.0
categories: [release]
---

I am proud to announce the first production release of COSMOS 5.0.0 Open Source Edition!

COSMOS 5 is a highly-scalable, cloud-native, containerized, web-interfaced, command and control software system. This first production release is ready for general use.

### What's New from Coming from COSMOS 4:

- The COSMOS user interface is now a web application instead of a desktop application - That means you can install COSMOS once, and all users just need to open a web browser to interact with the system. It also means COSMOS is a first class citizen when running in cloud environments like AWS and Azure.
- Long term data archiving and trending is now always present and is a core feature of COSMOS. The DART tool has been retired, and equivalent functionality is now built right in.
- COSMOS is now distributed as a collection of Docker containers orchestrated by docker-compose. The only prerequisite for running COSMOS is Docker, making it super easy to get up and running, and providing a consistent experience across host operating systems.
- COSMOS 5 is built around a new plugin system that provides a highly modular way to add targets, interfaces, routers, procedures, microservices, and tools to the COSMOS framework. COSMOS can now easily be the base for your entire system, and should spawn an entire ecosystem of ready to use plugins for various pieces of hardware and software.

### COSMOS 5 Technologies:

- Docker - COSMOS 5 runs across a set of containers managed by Docker
- Redis - Redis is used as a key-value store, and streaming data server
- Minio - Minio provides an S3 compatible file storage server
- Vue.js - Javascript technology used to build the new COSMOS user interface

### Functional versions of the following COSMOS tools are included in this release:

- Command and Telemetry Server
- Command Sender
- Packet Viewer
- Telemetry Viewer
- Telemetry Grapher
- Script Runner (Test Runner now built in)
- Limits Monitor
- Data Extractor (Both Command and Telemetry)
- Data Viewer
- Timeline
- Admin Interface

### Known Things That Aren't Done/Fully Working Yet:

- Automatic data reduction for long term trending is being performed but is not selectable in tools yet

### Prerequisites:

Docker - Running COSMOS 5 requires a working Docker installation. Typically Docker Desktop on Windows / Mac. Plain docker should work on linux. We're currently only developing / running with Docker Desktop on Windows, so if you have any issues on another platform, please let us know by submitting a ticket!

Minimum Resources allocated to Docker: 8GB RAM, 1 CPU, 80GB Disk
Recommended Resources allocated to Docker: 16GB RAM, 2+ CPUs, 100GB Disk

### To Run:

1. Download one of the archives (.zip or .tar.gz from the Github release page) [Download Release Here](https://github.com/BallAerospace/COSMOS/releases/tag/v5.0.0)
2. Extract the archive somewhere on your host computer
3. The COSMOS 5 containers are designed to work and be built in the presence of an SSL Decryption device. To support this a cacert.pem file can be placed at the base of the COSMOS 5 project that includes any certificates needed by your organization. If you don't need this, then please ignore, but if you see any SSL errors, this is probably why.
4. Run cosmos-control.bat start (Windows), or ./cosmos-control.sh start (linux/Mac)
5. COSMOS 5 will be built and when ready should be running (~20 mins for first run, ~3 for subsequent)
6. Connect a web browser to http://localhost:2900
7. Have fun running COSMOS 5!

We will be actively updating documentation on cosmosc2.com to capture all the details on COSMOS 5. So if it isn't documented yet, we're getting there! The biggest new documentation is on the new plugin system.

Please try it out and let us know what you think! Please submit any issues as Github tickets, or any generic feedback to COSMOS@ball.com.

Thanks!
