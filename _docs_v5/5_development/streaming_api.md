---
layout: docs
title: Streaming API
---

<div class="note">
  <h5>This documentation is for COSMOS Developers</h5>
  <p markdown="1">This information is just generally used behind the scenes in COSMOS tools</p>
</div>

The COSMOS 5 Streaming Api is the primary interface to receive a stream of the telemetry packets and/or command packets that have passed through the COSMOS system, both logged and continuously in realtime. Either raw binary packets or decommutated JSON packets can be requested.

This API is implemented over Websockets using the Rails ActionCable framework. Actioncable client libraries are known to exist for at least Javascript, Ruby, and Python. Other languages may exist or could be created. Websockets allow for easy interaction with the new COSMOS 5 Javascript based frontend.

The following interactions are all shown in Javascript, but would be very similar in any language.
Connecting to this API begins by initiating an ActionCable connection.

```
cable = ActionCable.createConsumer('/cosmos-api/cable')
```

This call opens the HTTP connection to the given URL and upgrades it to a websocket connection. This connection can then be shared with multiple “subscriptions”.

A subscription describes a set of data that you want the API to stream to you. Creating a subscription looks like this:

```
subscription = cable.subscriptions.create(
  {
    channel: 'StreamingChannel',
    scope: 'DEFAULT',
    token: token,
  },
  {
    received: (data) => {
      // Handle received data
    },
    connected: () => {
      // First chance to add what you want to stream here
    },
    disconnected: () => {
      // Handle the subscription being disconnected
    },
    rejected: () => {
      // Handle the subscription being rejected
    },
  }
)
```

Subscribing to the StreamingApi requires passing a channel name set to “StreamingChannel”, a scope which is typically “DEFAULT”, and an access token (a password in OpenSource COSMOS). In Javascript you also pass a set of callback functions that run at various lifecycle points in the subscription. The most important of these are connected and received.

connected runs when the subscription is accepted by the StreamApi. This callback is the first opportunity to request specific data that you would like streamed. Data can also be added or removed at any time while the subscription is open.

Data can be added to the stream by requesting individual items from a packet (decommutated data only), or by requesting the entire packet (both raw and decommutated data).

Adding items to stream is done as follows:

```
var items = ["TLM__INST__ADCS__Q1__RAW", "CMD__INST__COLLECT__DURATION__WITH_UNITS"]
var packets = ["TLM__INST__ADCS", "CMD__INST__COLLECT"]
this.subscription.perform('add', {
  scope: 'DEFAULT',
  token: token,
  mode: 'DECOM',
  items: items,
  packets: packets,
  start_time: this.startDateTime,
  end_time: this.endDateTime,
})
```

Mode is either RAW, DECOM, REDUCED_MINUTE, REDUCED_HOUR, or REDUCED_DAY and all items/packets requested in a single add must be in the same mode. However different modes can be used across calls to add.

Items are requested by keys of the form: `<CMD or TLM>__<TARGET NAME>__<PACKET NAME>__<ITEM NAME>__<VALUE TYPE>`
For example TLM\_\_INST\_\_ADCS\_\_Q1\_\_RAW.

Value type should be one of: RAW, CONVERTED, FORMATTED, or WITH_UNITS

Each field is separated by two underscores \_\_.

Entire packets can also be requested by keys of the form: `<CMD or TLM>__<TARGET NAME>__<PACKET NAME>__<VALUE TYPE>`. For Raw mode, VALUE TYPE should be set to RAW or omitted (e.g. TLM\_\_INST\_\_ADCS\_\_RAW or TLM\_\_INST\_\_ADCS).
start_time and end_time are standard COSMOS 64-bit integer timestamps in nanoseconds since the Unix Epoch (midnight January 1st, 1970). If start_time is null, that indicates to start streaming from the current time in realtime, indefinitely until items are removed, or the subscription is unsubscribed. end_time is ignored if start_time is null. If start_time is given and end_time is null, that indicates to playback from the given starttime and then continue indefinitely in realtime. If both start_time and end_time are given, then that indicates a temporary playback of historical data.

Data returned by the streaming API is handled by the received callback in Javascript. Data is returned as a JSON Array, with a JSON object in the array for each packet returned. Results are batched, and the current implementation will return up to 100 packets in each batch (the array will have 100 entries). 100 packets per batch is not guaranteed, and batches may take on varying sizes based on the size of the data returned, or other factors. An empty array indicates that all data has been sent for a purely historical query and can be used as an end of data indicator.

For decommutated items, each packet is represented as a JSON object with a 'time' field holding the COSMOS nanosecond timestamp of the packet, and then each of the requested item keys with their corresponding value from the packet.

```
[
  {
    time: 1234657585858,
    'TLM__INST__ADCS__Q1__RAW': 50.0,
    'TLM__INST__ADCS__Q2__RAW': 100.0
  },
  {
    time: 1234657585859,
    'TLM__INST__ADCS__Q1__RAW': 60.0,
    'TLM__INST__ADCS__Q2__RAW': 110.0
  },
]
```

For raw packets, each packet is represented as a JSON object with a time field holding the COSMOS nanosecond timestamp of the packet, a packet field holding the topic the packet was read from in the form of SCOPE\_\_TELEMETRY\_\_TARGETNAME\_\_PACKETNAME, and a buffer field holding a BASE64 encoded copy of the packet data.

```
[
  {
    time: 1234657585858,
    packet: 'DEFAULT__TELEMETRY__INST__ADCS,
    buffer: 'SkdfjGodkdfjdfoekfsg',
  },
  {
    time: 1234657585859,
    packet: 'DEFAULT__TELEMETRY__INST__ADCS',
    buffer: '3i5n49dmnfg9fl32k3',
  },
]
```
