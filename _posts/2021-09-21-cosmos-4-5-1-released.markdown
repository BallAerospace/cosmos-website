---
layout: news_item
title: "Ball Aerospace COSMOS 4.5.1 Released"
date: 2021-09-21 3:00:00 -0700
author: jmthomas
version: 4.5.1
categories: [release]
---

COSMOS 4.5.1

This is primarily a security and bug fix release. All users are recommended to update. Please see the migration notes below for necessary changes when upgrading from 4.4.x.

### Security Updates:

- [#1357](https://github.com/BallAerospace/COSMOS/issues/1357) Fix fortify issues in C ext
- [#1361](https://github.com/BallAerospace/COSMOS/issues/1361) Incorporate Fortify findings

### Bug Fixes:

- [#1363](https://github.com/BallAerospace/COSMOS/issues/1363) Synchronize packet and limits so that inject_tlm works as expected
- [#1314](https://github.com/BallAerospace/COSMOS/issues/1314) Time#sys breaks with frozen timestamps
- [#1300](https://github.com/BallAerospace/COSMOS/issues/1300) Read only files should not be editable
- [#1336](https://github.com/BallAerospace/COSMOS/issues/1336) Sort Files in CRC File
- [#1330](https://github.com/BallAerospace/COSMOS/issues/1330) xtce_converter: uninitialized constant USERPATH

### New Features:

- [#1299](https://github.com/BallAerospace/COSMOS/issues/1299) Stay on top command line option for tools

### Maintenance:

- [#1242](https://github.com/BallAerospace/COSMOS/issues/1242) Update installer link
- [#1252](https://github.com/BallAerospace/COSMOS/issues/1252) Check for absolute path in installer
- [#1362](https://github.com/BallAerospace/COSMOS/issues/1362) Ahk issues

### Migration Notes from COSMOS 4.4.x:

To upgrade to the latest version of COSMOS, run "bundle update cosmos" in your COSMOS project folder.

Modify you system.txt files to:

1. Make sure you have a X_CSRF_TOKEN line with a unique value (anything will do, just change it)
2. Change all LISTEN_HOST settings to 127.0.01 unless you need connections from external hosts
3. If you know only specific external hosts will be connecting, add ALLOW_ACCESS lines for each
4. Only add ALLOW_ROUTER_COMMANDING if you are chaining CmdTlmServers (that need to send commands), or you are receiving commands through routers
5. Only add ALLOW_ORIGIN <Address of webpage>if you expect COSMOS to be accessed from a webpage
6. Add ALLOW_HOST <Your COSMOS IP Address>:7777, etc if you expect the COSMOS APIs to be accessed from external computers. Otherwise it will only accept connections that have a HOST header set to localhost
