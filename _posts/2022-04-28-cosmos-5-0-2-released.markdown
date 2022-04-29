---
layout: news_item
title: "Ball Aerospace COSMOS 5.0.2 Released"
date: 2022-04-28 6:00:00 -0700
author: ryanmelt
version: 5.0.2
categories: [release]
---

I'm happy to announce the release of COSMOS 5.0.2! This release brings new tools, lots of bug fixes, and a focus on stability. Everyone is encouraged to upgrade and start running off of the prebuilt containers on Docker Hub.

## New Tools

TableManager - Edit your binary configuration tables from COSMOS
Autonomic (Beta) - Configurable Automation and Reactions
Calendar (Beta) - Timeline now has a familiar calendar based interface and supports metadata entry

## CI/CD Improvements

Tons of great stuff in this release, and it's been far too long with 647 commits since the 5.0.1 release. Fortunately infrequent releases are over now that our unit test / integration test / and release process have been implemented into the Github Actions CI/CD pipeline.

Additionally we have changed from using Cypress for integration test to Playwright. With this our integration tests are now less flaky, and we are testing both Chrome and Firefox automatically.

We've also been doing a lot of development against an internal Git server, but will now be switching back to fully open development on Github to make maximum use of this CI/CD environment.

## Release Artifacts

A full set of release artifacts is now being pushed for every release.

- Full set of containers to Docker Hub (No more need to build yourself)
- Main ruby gem and all plugin tools pushed to Rubygems.org
- @cosmosc2/tool-common npm package

## Significant Changes

- Initial Version of Automated Data Reduction
- Added IframeWidget
- Limits sets now supported
- Add execute selection to script runner
- Add ImageviewerWidget
- Implement script edit locks
- Add menu for find/replace to ScriptRunner
- Colorblind Mode
- Upgrade ACE Editor
- Add Redis command tab to admin
- Add SR run from cursor functionality
- Add open/save config to limits monitor
- Add spacer widget
- Allow get_tlm_values to return nil if item not yet set
- Update all of our dependency versions to latest
- Add matrix to gemspec to support Ruby 3.1
- Deploy UNKNOWN packet loggers
- Partially sandbox ScriptRunner
- Add completed script history table
- Erb support during plugin install. Asynchronous plugin install. 3rd Party Gem Support.
- Scriptrunner breakpoints
- Easy Install In Offline Environments
- Add Mnemonic Checker to ScriptRunner
- Fix build on M1 macs
- Upgrade vue cli v5
- Add target file methods
- Support Browsers Going Back 2 Years
- Remove privileged host setup commands
- Update Interface Reconnect Logic to Work the Same as COSMOS 4

### Prerequisites:

Docker - Running COSMOS 5 requires a working Docker installation. Typically Docker Desktop on Windows / Mac. Plain Docker or Podman also works on linux. We actively develop and run with Docker Desktop on Mac/Windows, and Linux on Raspberry Pi, so if you have any issues on another platform, please let us know by submitting a ticket!

Minimum Resources allocated to Docker: 4GB RAM, 1 CPU, 80GB Disk
Recommended Resources allocated to Docker: 16GB RAM, 2+ CPUs, 100GB Disk

### To Run:

1. Download one of the archives (.zip or .tar.gz from the Github release page) [Download Release Here](https://github.com/BallAerospace/COSMOS/releases/tag/v5.0.2)
2. Extract the archive somewhere on your host computer
3. Run: docker-compose up -d
4. Connect a web browser to http://localhost:2900
5. Have fun running COSMOS 5!

We will be actively updating documentation on cosmosc2.com to capture all the details on COSMOS 5. So if it isn't documented yet, we're getting there! The biggest new documentation is on the new plugin system.

Please try it out and let us know what you think! Please submit any issues as Github tickets, or any generic feedback to COSMOS@ball.com.

Thanks!

**Full Changelog**: https://github.com/BallAerospace/COSMOS/compare/v5.0.1...v5.0.2
