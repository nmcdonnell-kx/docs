---
title: Solace | Interfaces | Documentation for kdb+ and q
description: Interface between kdb+ and Solace 
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
keywords: Solace, publish, subscribe, request, reply, streaming, qos, q
---
# ![Solace](../img/solace.jpeg) Solace interface to kdb+

:fontawesome-brands-github:
[KxSystems/solace](https://github.com/KxSystems/solace)



The [Solace PubSub+ Event Broker](https://solace.com/products/event-broker/software/) can be used to efficiently stream events and information across cloud, on-premises and within IoT environments. The “+” in PubSub+ indicates its support of wide-ranging functionality beyond publish-subscribe. This includes request-reply, streaming and replay, as well as different qualities of service, such as best-effort and guaranteed delivery.


## Use cases

The event broker is used across a number of sectors including

-   airline industry (air-traffic control)
-   financial services (payment processing)
-   retail (supply-chain/warehouse management)

:fontawesome-solid-globe:
[Other sectors](https://solace.com/use-cases/)


## Kdb+/Solace Integration

This interface lets you communicate with a Solace PubSub+ event broker from a kdb+ session. The interface follows closely the [Solace C API](https://docs.solace.com/Solace-PubSub-Messaging-APIs/C-API/c-api-home.htm). Exposed functionality includes

-   subscription to topics on Solace brokers
-   direct/persistent/guaranteed messaging functionality
-   endpoint management

:fontawesome-brands-github:
[Install guide](https://github.com/KxSystems/solace#installation)
