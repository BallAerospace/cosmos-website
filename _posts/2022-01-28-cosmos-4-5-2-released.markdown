---
layout: news_item
title: "Ball Aerospace COSMOS 4.5.2 Released"
date: 2022-01-28 12:00:00 -0700
author: jmthomas
version: 4.5.2
categories: [release]
---

COSMOS 4.5.2

This is primarily a bug fix release. All users are recommended to update. Please see the migration notes below for necessary changes when upgrading from 4.4.x.

### Bug Fixes:

- [#1438](https://github.com/BallAerospace/COSMOS/issues/1438) Remove all reserved item names from system cmd meta

### Maintenance:

- [#1440](https://github.com/BallAerospace/COSMOS/issues/1440) Remove ruby23 support

### Migration Notes from COSMOS 4.4.x:

To upgrade to the latest version of COSMOS, run "bundle update cosmos" in your COSMOS project folder.

Modify you system.txt files to:

1. Make sure you have a X_CSRF_TOKEN line with a unique value (anything will do, just change it)
2. Change all LISTEN_HOST settings to 127.0.01 unless you need connections from external hosts
3. If you know only specific external hosts will be connecting, add ALLOW_ACCESS lines for each
4. Only add ALLOW_ROUTER_COMMANDING if you are chaining CmdTlmServers (that need to send commands), or you are receiving commands through routers
5. Only add ALLOW_ORIGIN <Address of webpage>if you expect COSMOS to be accessed from a webpage
6. Add ALLOW_HOST <Your COSMOS IP Address>:7777, etc if you expect the COSMOS APIs to be accessed from external computers. Otherwise it will only accept connections that have a HOST header set to localhost
