---
layout: docs
title: Calendar
toc: true
---

## Introduction

Calendar allows for the simple execution of commands and scripts. This uses the microservice operator in cosmos to run a multi-threaded application to listen for updates to the timelie schedule and execute on about a seconds accuracy.

![Calendar](/img/v5/calendar/calendar.png)

## Types of Events

- Metadata

- Narrative

- Activity

### Metadata

All Metadata is stored into a single sort set in the backend at millisecond resolution so you can now record any external data outside of the target / packet.  

### Narrative

A simple way to record events on the calendar, for example a test window or anything else...

### Activity

Scheduled on a timeline these can run single commands or kick off scripts to run.

### Adding Timelines

Adding a Timeline to Cosmos.

 - Each timeline consists on several threads so be careful of your compute resources you have as you can overwhelm cosmos with lots of these.
 - Also note can not have over lapping activities on a single calendar.

### Timeline lifecycle

When a user creates a timeline, the cosmos operator see a new microservice has been created. This signals the operator to start a new microservice, the timeline microservice. The timeline microservice is the main thread of execution for the timeline. This starts a scheduler manager thread. The scheduler manger thread contains a thread pool that hosts more then one thread to run the activity. The scheduler manger will evaluate the schedule and based on the start time of the activity it will add the activity to the queue.

The main thread will block on the web socket to listen to request changes to the timeline, these could be adding, removing, or updating acitivities. This will make the changes to the schedule if they are within the hour but not out side of that as we don't want to hold everything in memory. When the web socket gets an update it has an action lookup table that based on the change takes different actions that could include removing the block and updating the schedule from the database to ensure the schedule and the database are always in synk.

The schedule thread is check every second to make sure if a task can be run. If the start time is equal or less then the last 15 seconds it will then check the previously queued jobs list in the schedule. If the activity has not been queued and is not fulfilled. If the activity passes these check then a queued event is added to the activity but the activity is not saved to the database. The activity will be added to the queue to be worked by the worker threads. (TODO) This could be improved upon as a was to wake the thread when a change to the schedule happens or when a job needs to run.

The workers are plain and simple, they block on the queue until an activity is placed on the queue. Once a job is pulled from the queue they check the type and run the activity. The thread will mark the activity fulfillment true and update the database record with the complete. If the worker gets an error while trying to run the task the activity will NOT be fulfilled and record the error in the database.

![Timeline Lifecycle](/img/v5/calendar/timeline_lifecycle.png)