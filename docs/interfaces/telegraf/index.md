---
title: Telegraf Handler | Interfaces | Documentation for kdb+ and q
description: Telegraf is an agent to collect report metrics and events 
---

# Telegraf Handler for kdb+

:fontawesome-brands-github:
[KxSystems/telegraf_kdb_handler](https://github.com/KxSystems/telegraf_kdb_handler)

[Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) is an agent used to collect and report both metrics and events. Written in Go, it is a standalone and lightweight compiled binary with no external dependencies. Its primary function is to acts as a plugin-driven collection that can run on your hosts, collecting data about your systems and applications. Alternatively it can operate remotely, scraping data via endpoints exposed by your applications.

A full specification outlining the Telegraf line message protocol can be found [here](https://docs.influxdata.com/influxdb/v2.0/reference/syntax/line-protocol/).

## Use cases

This technology is used commonly for:

- IoT sensors: Collecting critical stateful data (pressure levels, temp levels) from IoT sensors and devices.
- Systems monitoring: Collect metrics from modern stack cloud platforms, containers and orchestrators
- Database monitoring: Collection of metrics from many of the most common database technologies

## Kdb+/Telegraf integration

The Kdb+/Telegraf integration provides users with a handler/parser of Telegraf line protocol messages and an evolving schema map to facilitate the non constant schemas of the messages. Telegraf messages received via HTTP are parsed based on endpoint considerations and formatted into q dictionaries/tables allowing a user to generate appropriate kdb+ structures or analyse the messages as appropriate.

:fontawesome-solid-hand-point-right: [Function reference](reference.md)

## Install the Kdb+/Telegraf handler

:fontawesome-brands-github: 
[Installation guide](https://github.com/KxSystems/telegraf_kdb_handler#installation)

## Status

The interface is currently available under an Apache 2.0 licence as a beta release and is supported on a best-efforts basis by the Fusion team. This interface is currently in active development, with additional functionality to be released on an ongoing basis.

:fontawesome-brands-github: 
[Issues and feature requests](https://github.com/KxSystems/telegraf_kdb_handler/issues) 
<br>
:fontawesome-brands-github: 
[Guide to contributing](https://github.com/KxSystems/telegraf_kdb_handler/blob/master/CONTRIBUTING.md)
