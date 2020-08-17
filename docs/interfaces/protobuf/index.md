---
title: Protobufkdb | Interfaces | Documentation for kdb+ and q
keywords: protobuf, protocol buffers, api, fusion, interface, q
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
---

# Protobufkdb

:fontawesome-brands-github: 
[KxSystems/mqtt](https://github.com/KxSystems/protobufkdb)

Protobuf/Google Protocol Buffers is a mechanism for serializing sructured data and helps to communicate data among different programming languages.

The message is designed with simple schema file and its backward compatability is underpinned by indices assigned to sub messages rather than versioning, as well as some inspectional attributes like "optional" or "required". The actual code for each programming language are generated automatically with a dedicated compiler without developers' effort to modify the code for maintenance.

You can find more details in [Google developer's page](https://developers.google.com/protocol-buffers/).

## Protobuf/kdb+ Integration

This interface allows to serialize/desrialize Protobuf message from a kdb+ session referring to loaded schema files. This interface supports two ways of loading a schema file:

- Incorporate into a shared library at compilation
- Load a schema file from a kdb+ session dynamically

The first option provides peformance gain about 10% to 20% while the latter option gives you a flexibility to modify schema files without re-compiling a library.

You can also assign serialiszed data to a kdb+ variable or save it to a file. Similarly you can deserialize data from a kdb+ variable or a file.

:fontawesome-regular-hand-point-right:
[Function reference](reference.md), [example implementations](examples.md)

## Install Protobufkdb

For installing Protobufkdb, see the page below.

:fontawesome-brands-github: 
[Install guide](https://github.com/KxSystems/protobufkdb#installation)

