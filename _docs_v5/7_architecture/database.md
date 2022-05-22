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

Packet Logging in COSMOS 5
Packet Log File Format
Packet logs in COSMOS 5 are used to store raw binary packets as received from various targets, as well as decommutated packets stored as JSON structures.  
File Header
COSMOS 5 Packet log files start with the 8-character sequence “COSMOS5_”.  This can be used to identify the type of file independent of filename and differentiate them from newer and older versions.  
File Names
Packet log filenames take the form:
YYYYMMDDHHmmSSNNNNNNNNN__YYYYMMDDHHmmSSNNNNNNNNN__SCOPE__CMDORTLM__TARGETNAME__PACKETNAME__RTORSTORED__TYPE.bin
Where:
YYYY = Year
MM = Month
DD = Day
HH = Hour
mm = Minute
SS = Seconds of Minute
NNNNNNNNN = Nanoseconds of Second
SCOPE = Scope (Typically DEFAULT)
CMDORTLM = CMD or TLM
TARGETNAME = The target name of the file’s contents
PACKETNAME = The packet name of the file’s contents
RTORSTORED = RT (Realtime) or STORED (Stored)
TYPE = RAW, DECOM, MINUTE, HOUR, DAY
Entry Types
Packet log files have 5 different entry types with room for future expansion.   All entry headers are big endian binary data.  
Common Entry Format
This common format is used for all packet log entries as well as the corresponding index file entries.
Field	Data Type	Description
Length	32-bit Unsigned Integer	Total length of the entry in bytes not including the length field.  Max entry size is therefore 4GiB.
Entry Type	4-bit Unsigned Integer	Entry Type:
1 = Target Declaration
2 = Packet Declaration
3 = Raw Packet
4 = JSON Packet
5 = Offset Marker
Cmd/Tlm
Flag	1-bit Unsigned Integer	1 = Command
0 = Telemetry
Stored Flag	1-bit Unsigned Integer	1 = Stored Data
0 = Realtime Data
Id Flag	1-bit Unsigned Integer	1 = ID present
0 = ID not present
Reserved	9-bit Unsigned Integer	Reserved for Future expansion.  Should be set to 0 if unused.
Entry Data	Variable	Unique data based on entry type.  See Entry Types Below
Id
(Optional)	32-byte Binary Hash	If the ID field is set, this is a binary 256-bit SHA-256 hash uniquely identifying a target configuration or packet configuration

Target Declaration Entry
Target declaration entries declare the name of a target before its first packet entry occurs in the file.
Field	Data Type	Description
Target Name	Variable-Length ASCII String	Target Name

Packet Declaration Entry
Packet declaration entries declare the name of a packet and associate it with a target name before the first packet of this type in the file.
Field	Data Type	Description
Target Index	16-bit Unsigned Integer	Index into a dynamically built table of target names, generated from the order of the target declarations in the file.  The first target declaration gets index 0, the second target declaration gets index 1, etc.
Packet Name	Variable-Length ASCII String	Packet Name

I think I removed a redundant length field before packet name here
Raw Packet and JSON Packet Entries
Raw packets contain the raw data that was received by COSMOS on an interface.  JSON packets are used to store decommutated data in JSON text format.
Field	Data Type	Description
Packet Index	16-bit Unsigned Integer	Index into a dynamically built table of cmd_or_tlm/target name/packet name tuples, generated from the order of the packet declarations in the file.  The first packet declaration gets index 0, the second packet declaration gets index 1, etc.  This limits the max number of unique packet types in a single file to 65536.
Timestamp	64-bit Unsigned Integer	Packet timestamp in nanoseconds from the unix epoch (Jan 1st, 1970, midnight).  The packet received time for raw packet entries, and the “packet time” for JSON packet entries (which are used to store decommutated date).  For JSON packet entries, the packet received time can be extracted from the JSON data if needed.
Packet Data	Variable-Length Block Data	The Raw binary packet data for Raw Packet entries, and ASCII JSON data for JSON packet entries.   Note the Common Id field is not supported with either type of packet entry.

Offset Marker Entry
An offset marker entry is written at the end of each packet log file.  This records the Redis stream offset of the last packet logged in the file and allows for a seamless transition from logs files, back into Redis streams to get the most recent data.
Field	Data Type	Description
Offset	Variable-Length ASCII String	Redis offset in the corresponding Redis packet stream that indicates the exact position to continue playback from the log file into Redis recent data in the stream.

JSON Packet Data Format
COSMOS 5 decommutated data is stored in a manner such that the minimum data is stored necessary to generate every value type (RAW, CONVERTED, FORMATTED, and WITH_UNITS).   The overall packet is a JSON object. For each item in a packet, a name/value pair exists with the value being the unaltered RAW value of the item.  If the item has a read_conversion or states defined, a name/value pair with the name being the items name + __C exists containing the CONVERTED value.  If the item has a format string, a name/value pair with the name being the item’s name + __F exists containing the FORMATTED value which is always a string.  If the item has units defined, a name/value pair with the name being the item’s name + __U exists containing the WITH_UNITS value.  If the item has a current limits state, a name/value pair with the name being the item’s name + __L exists containing the value of the limits_state.
Here is the code the generates this data:
      json_hash = {}
      packet.sorted_items.each do |item|
        json_hash[item.name] = packet.read_item(item, :RAW)
        json_hash[item.name + "__C"] = packet.read_item(item, :CONVERTED) if item.read_conversion or item.states
        json_hash[item.name + "__F"] = packet.read_item(item, :FORMATTED) if item.format_string
        json_hash[item.name + "__U"] = packet.read_item(item, :WITH_UNITS) if item.units
        limits_state = item.limits.state
        json_hash[item.name + "__L"] = limits_state if limits_state
      end

Example data:
{
  ‘TEMP1’: 1234,
  ‘TEMP1__C’: 51.534243143,
  ‘TEMP1__F’ = ’51.534’,
  ‘TEMP1__U’ = ’51.534 C’,
  ‘TEMP1__L’ = ‘RED_HIGH’,
  …
}
Index Files
Alongside each Packet Log File, COSMOS 5 also created a separate index file with the same name but a file extension of “.idx”.  This file is designed to allow quick binary searches on the corresponding packet log by timestamp.
File Header
COSMOS 5 Packet log files start with the 8-character sequence “COSIDX5_”.  This can be used to identify the type of file independent of filename and differentiate them from newer and older versions.  
Index File Entries
Index file entries are the same as the corresponding packet entries, except the packet data field is replaced by 64-bit unsigned integer with the offset into the corresponding packet log file, where the full packet entry exists.
Field	Data Type	Description
Packet Index	16-bit Unsigned Integer	Index into a dynamically built table of cmd_or_tlm/target name/packet name tuples, generated from the order of the packet declarations in the file.  The first packet declaration gets index 0, the second packet declaration gets index 1, etc.  This limits the max number of unique packet types in a single file to 65536.
Timestamp	64-bit Unsigned Integer	Packet timestamp in nanoseconds from the unix epoch (Jan 1st, 1970, midnight).  The packet received time for raw packet entries, and the “packet time” for JSON packet entries (which are used to store decommutated date).  For JSON packet entries, the packet received time can be extracted from the JSON data if needed.
Offset	64-bit Unsigned Integer	File offset in bytes to the beginning of the corresponding entry in the packet log file.

Index File Footer
All of the target declaration entries and packet declaration entries from the corresponding packet log file are written out together at the very end of index files.   This makes the entire index file up to this point a set of fixed size entries that can be very quickly searched (as opposed to the standard packet log file where every entry is variably sized).  The index file footer must be read first to properly process the index file.
Field	Data Type	Description
Count of Target Declaration Entries	16-bit Unsigned Integer	The number of target declaration entries that follow.
Target Declaration Entries	Variable Sized	Array of target declaration entries
Count of Packet Declaration Entries	16-bit Unsigned Integer	The number of packet declaration entries that follow.
Packet Declaration Entries	Variable Sized	Array of packet declaration entries
Index File Footer Length 	32-bit Unsigned Integer	The entire length of the footer (including this length field)

Logging Microservices
COSMOS 5 creates a microservice for each target to perform logging raw packets (SCOPE__PACKETLOG__TARGETNAME), and a separate microservice for logging of decommutated packets (SCOPE__DECOMLOG__TARGETNAME).    Each of these two microservices is configured with the full list of associated Redis stream topics (one for each packet ie. SCOPE__TELEMETRY__TARGETNAME__PACKETNAME, SCOPE__DECOM__TARGETNAME__PACKETNAME) needed to get the packets to log.  Theoretically, if one of these microservices gets backed up, it could be split into multiple with each taking a subset of the packets.
Log File Organization
COSMOS 5 log files are stored in Minio (a local clone of S3).  Telemetry raw packet log files are stored at SCOPE/rawlogs/tlm/TARGETNAME/PACKETNAME/YYYYMMDD and command raw packet log files at SCOPE/rawlogs/cmd/TARGETNAME/PACKETNAME/YYYYMMDD.  The corresponding .idx files are also kept in the same place.  
Telemetry decom packet log files are stored at SCOPE/decomlogs/tlm/TARGETNAME/PACKETNAME/YYYYMMDD and command decom packet log files at SCOPE/decomlogs/cmd/TARGETNAME/PACKETNAME/YYYYMMDD.
Reduced telemetry decom packet log files are stored at SCOPE/reducedlogs/tlm/TARGETNAME/PACKETNAME.
Reduced Data Files
COSMOS 5 creates a microservice for each target to perform telemetry data reduction across the target’s packets.  Command data is not reduced.  This microservice subscribes to each packets decom topic, and calculates a mean, minimum, maximum, and standard deviation calculation over each numeric data point.  These calculations are kept at a minute, hour, and daily granularity, and are stored in corresponding Redis topics for quick access (SCOPE__REDUCEDMINUTE__TARGETNAME__PACKETNAME, SCOPE__REDUCEDHOUR__TARGETNAME__PACKETNAME, SCOPE__REDUCEDDAY__TARGETNAME__PACKETNAME).   Minute and Hourly logs are logged to files stored in S3 daily, daily data is logged to files on the first day of each month.
Archiving
COSMOS 5 keeps archiving microservices that ensure that the file storage buckets on Minio/S3 never overflow.  This process will start deleting the oldest files as soon as a configurable threshold is reached.  Bucket policy should be used to ensure data is continuously backed up to a different S3 location.
Configuration Settings
Bucket – Bucket to Monitor
Prefix – Path in the bucket at which to enforce the size limit
Size limit – The oldest files over this size will be deleted
Delay – Delay between checks



## Reduced Datasets

## Near Realtime Details
