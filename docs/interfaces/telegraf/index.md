---
title: Telegraf Handler | Interfaces | Documentation for kdb+ and q
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
keywords: telegraf, fusion, interface, q
---

# Telegraf Handler for kdb+

:fontawesome-brands-github:
[KxSystems/telegraf_kdb_handler](https://github.com/KxSystems/telegraf_kdb_handler)

Telegraf is an agent to collect and report metrics and events. It was designed as a lightweight, plugin-driven collection that can run on your hosts, collecting data about your systems and applications, or it can operate remotely, scraping data via endpoints exposed by your applications.

The messages of telegraf are not constant for its contents and therefore fixed schema is not feasible to store those information. This interface provides a handler of telegraf line protocol message, a format of messages sent by telegraf and converts them to q dictionaries after parsing inputs to proper type of q object.

For specification of telegraf line protocol message, see [this page](https://docs.influxdata.com/influxdb/v2.0/reference/syntax/line-protocol/).

## Status

This interface is currently available under an Apache 2.0 licence and is supported on a best-efforts basis by the Fusion team. This interface is currently in active development, with additional functionality to be released on a rolling basis.

If you find issues with the interface or have feature requests please 
:fontawesome-brands-github:
[raise an issue](https://github.com/KxSystems/telegraf_kdb_handler/issues). 

To contribute to this project follow the 
:fontawesome-brands-github:
[contributing guide](https://github.com/KxSystems/telegraf_kdb_handler/blob/master/CONTRIBUTING.md).