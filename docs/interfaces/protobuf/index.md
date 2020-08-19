---
title: Protobufkdb | Interfaces | Documentation for kdb+ and q
keywords: protobuf, protocol buffers, api, fusion, interface, q
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
---

# Protobufkdb

:fontawesome-brands-github: 
[KxSystems/mqtt](https://github.com/KxSystems/protobufkdb)

Protobuf/Google Protocol Buffers is a flexible data interchange mechanism for serializing/deserializing structured data wherever programs or services have to store or exchange data via interfaces while maintaining a high degree of language interoperability.  The binary encoded format used to represent the data is considerable more efficient, both in terms of raw processing speed and compact data size, than other encodings such as JSON or XML.

Protocol buffers is supported across a wide variety of programming languages (including C++, C#, Java and Python), enabling communication between separate applications and platforms though a simple common structured schema.  Using this schema, the protobuf buffer's compiler generates source code in the your language of choice, which serializes and deserializes the binary encoded data.  The substantially reduces the boilerplate code which is otherwise required to convert between the messaging data format and your language's native structures.

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

