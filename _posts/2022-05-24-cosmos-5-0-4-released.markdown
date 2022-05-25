---
layout: news_item
title: "Ball Aerospace COSMOS 5.0.4 Released"
date: 2022-05-24 6:00:00 -0700
author: ryanmelt
version: 5.0.4
categories: [release]
---

I'm happy to announce the release of COSMOS 5.0.4! This release updates dependencies, has some important bug fixes, and improves overall performance.

The most obvious change from this release is that the Redis process has been broken into two. We now run a cosmos-redis service and a cosmos-redis-ephemeral service. This allows us to configure Redis to only backup configuration data, and not streaming target data, greatly reducing the cost of redis persistence and greatly improving performance.

## What's Changed

### Tickets

- [#1647 Allow setting variables in TlmViewer screens](https://github.com/BallAerospace/COSMOS/issues/1647)
- [#1640 Decom error adding key during iteration](https://github.com/BallAerospace/COSMOS/issues/1640)
- [#1636 Break Redis into Ephemeral and Persistence Services](https://github.com/BallAerospace/COSMOS/issues/1636)
- [#1633 Reducer errors when using loadsim target with default settings](https://github.com/BallAerospace/COSMOS/issues/1633)
- [#1627 Displayed COSMOS version number doesn't update after upgrading](https://github.com/BallAerospace/COSMOS/issues/1627)
- [#1625 Large DataExtractor queries appear to timeout](https://github.com/BallAerospace/COSMOS/issues/1625)

### Pull Requests

- Take microservice name from ENV, fix topics by @jasonatball in https://github.com/BallAerospace/COSMOS/pull/1628
- ERB should support minus sign at end trim mode to suppress blank lines by @ryanatball in https://github.com/BallAerospace/COSMOS/pull/1637
- Split redis by @ryanatball in https://github.com/BallAerospace/COSMOS/pull/1643
- add mutex to metric by @ryanatball in https://github.com/BallAerospace/COSMOS/pull/1644
- Add codecov.yml by @jasonatball in https://github.com/BallAerospace/COSMOS/pull/1651
- Metadata by @jasonatball in https://github.com/BallAerospace/COSMOS/pull/1642
- Initial package audit script. by @ryanatball in https://github.com/BallAerospace/COSMOS/pull/1649
- fix s3 file cache sharing temp folder across processes by @ryanatball in https://github.com/BallAerospace/COSMOS/pull/1650
- Allow button widget vars and fix radio groups by @jasonatball in https://github.com/BallAerospace/COSMOS/pull/1652
- Data extractor disconnects by @ryanatball in https://github.com/BallAerospace/COSMOS/pull/1653
- Bump alpine build, move ruby-dev to cosmos-ruby by @jasonatball in https://github.com/BallAerospace/COSMOS/pull/1655

### Migration Notes:

If you are upgrading and already have a local compose.yaml file, then you will need to manually add the cosmos-redis-ephemeral service, like it is defined in the compose.yaml file in the release.

### Prerequisites:

Docker - Running COSMOS 5 requires a working Docker installation. Typically Docker Desktop on Windows / Mac. Plain Docker or Podman also works on linux. We actively develop and run with Docker Desktop on Mac/Windows, and Linux on Raspberry Pi, so if you have any issues on another platform, please let us know by submitting a ticket!

Minimum Resources allocated to Docker: 4GB RAM, 1 CPU, 80GB Disk
Recommended Resources allocated to Docker: 16GB RAM, 2+ CPUs, 100GB Disk

### To Run:

1. Download one of the archives (.zip or .tar.gz from the Github release page) [Download Release Here](https://github.com/BallAerospace/COSMOS/releases/tag/v5.0.4)
2. Extract the archive somewhere on your host computer
3. Run: docker-compose up -d
4. Connect a web browser to http://localhost:2900
5. Have fun running COSMOS 5!

We will be actively updating documentation on cosmosc2.com to capture all the details on COSMOS 5. So if it isn't documented yet, we're getting there! The biggest new documentation is on the new plugin system.

Please try it out and let us know what you think! Please submit any issues as Github tickets, or any generic feedback to COSMOS@ball.com.

Thanks!

**Full Changelog**: https://github.com/BallAerospace/COSMOS/compare/v5.0.3...v5.0.4
