---
layout: docs
title: Autonomic
toc: true
---

## Introduction

Autonomic allows for the simple execution of commands and scripts based on stream based input. This uses the microservice operator in cosmos to run a multi-threaded application to listen for updates on and execute on about a seconds accuracy.


### Overview and TriggerGroups

Triggers are organized into groups, these groups are to ensure that we can scale as the number of subscriptions to the data can be a bottleneck.

![Autonomic](/img/v5/autonomic/autonomic.png)

```json
{
  "name": "system42",
}
```

### Triggers

Triggers are are logical components that are evaluated to true or false.

![Triggers](/img/v5/autonomic/triggers.png)

```json
{
  "group": "system42",
  "description": "TBD",
  "left": {
    "type": "item",
    "target": "INST",
    "packet": "ADCS",
    "item": "POSX",
    "raw": true,
  },
  "operation": ">",
  "right": {
    "type": "value",
    "value": 0,
  }
}
```

### Reactions

Reactions wait for triggers to be evaluated and perform actions based on what you tell them.

![Reactions](/img/v5/autonomic/reactions.png)

```json
{
  "description": "INST command",
  "snooze": 300,
  "review": true,
  "triggers": [
    {
      "name": "123456",
      "group": "system42",
    },
  ],
  "actions": [
    {
      "type": "command",
      "command": "INST CLEAR"
    }
  ]
}
```

### Autonomic lifecycle

> TODO

![Autonomic Lifecycle](/img/v5/autonomic/autonomic_lifecycle.png)