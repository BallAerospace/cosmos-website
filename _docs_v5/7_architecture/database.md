---
layout: docs
title: Database Architecture
---

Built into COSMOS is a custom time series database designed to meet the unique requirements of the COSMOS C2 system. Every database design in existence is a series of trades, which is why no single database will ever be perfect for every application.

## Trades

Parameters that must be traded include:

- Resource utilization (Disk, CPU, RAM, Network)
- Queryability
- Performance (Reads, Writes, Queries)
- Organization
- Maximum Usable Size
- Data Portability
- Schema Flexability
- Complexity

## Driving Requirements

The following requirements drove this particular design:

1. Log everything, possibly forever

1. Store both raw and decommutated data

1. Optionally store unprocessed byte streams

1. Provide similar performance for retrieving data logged 10 minutes ago, and 10 years ago

1. Optimize for time based queries of individual packets

1. Use simple and easy to understand data formats

1. Support fast queries for data collected over large periods of time (possibly years)

No existing, freely available database that I am aware of can meet all of these requirements. Hence, the COSMOS time series database was born.

## Files and File Names

## Folder Organization

## File Formats

## Reduced Datasets

## Near Realtime Details
