---
title: Interface example | Protobuf | Interfaces | Documentation for kdb+ and q
keywords: protobuf, protocol buffers, api, fusion, interface, q
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
---
# Protobuf/Protocol Buffers Interface Example

:fontawesome-brands-github:
[KxSystems/protobufkdb](https://github.com/KxSystems/protobufkdb)

In the example below we assume that the file structure of the current working directory looks like this:
 
```bash
.
├── proto
│   ├── google
│   ├── kdb_type_specifier.proto
│   └── sample.proto
└── q
    └── protobufkdb.q
```

Here `sample.proto` is defined like this:

```bash
syntax = "proto3";

option cc_enable_arenas = true;

import "kdb_type_specifier.proto";

message Office {
    repeated string name    = 1;
    double   longtitude     = 2;
    double   latitude       = 3;
    int32    accessed       = 4 [(kdb_type) = DATE];
}

message Region {
    string region              = 1;
    map<string, Office> office = 2; 
}
```

## Load Protobufkdb Library

```q
q)\l q/protobufkdb.q
```

## Import Schema File

_This procedure is not necessary when you are using a library built from source linking schema files. In using dynamic import functionality you need to specify the path of your proto (schema) files first._

Tell protobufkdb the location of schema files specifying its path (`./proto`) and then import a specific file (`sample.proto`).

```q
q).protobufkdb.addProtoImportPath["proto"]
q).protobufkdb.importProtoFile["sample.proto"]
```

## Checking Schema File

```q
q).protobufkdb.displayMessageSchema[`Office]
message Office {
  repeated string name = 1;
  double longtitude = 2;
  double latitude = 3;
  int32 accessed = 4 [(.kdb_type) = DATE];
}

q).protobufkdb.displayMessageSchema[`Region]
message Region {
  string region = 1;
  map<string, .Office> office = 2;
}

```

## Serialize Data

Protobuf message is expressed in a mixed list in q. You can serialize the data with a target schema name.

```q
q)HQ:((`$"Head_Office"; `$"Brian_Conlon_House"); 54.179127; -6.337371; 2020.03.23)
q)encodedHQ:.protobufkdb.serializeArray[`Office; HQ]
q)encodedHQ
"\n\013Head_Office\n\022Brian_Conlon_House\021Qj/\242\355\026K@\031\253y\216\..
q)newry:((`$"Newry_Office"; `$"The_Conlon_Building"); 54.1751772; -6.3378739; 2020.08.14)
q)belfast:(enlist `Belfast; 54.592595; -5.927475; 2020.08.14)
q)EMEA:`HQ`Newry`Belfast!(HQ; newry; belfast)
q)encodedEMEA:.protobufkdb.serializeArrayArena[`Region; (`EMEA; EMEA)]
q)encodedEMEA
"\n\004EMEA\022<\n\002HQ\0226\n\013Head_Office\n\022Brian_Conlon_House\021Qj/..
```

## Deserialize Data

```q
q).protobufkdb.parseArray[`Office; encodedHQ]
`Head_Office`Brian_Conlon_House
54.17913
-6.337371
2020.03.23
q).protobufkdb.parseArrayArena[`Region; encodedEMEA]
`EMEA
`HQ`Newry`Belfast!((`Head_Office`Brian_Conlon_House;54.17913;-6.337371;2020.0..
```

## Save Serialized Data

You can serialize and save data to a file sepcifying a target schema and the target file name.

```q
q).protobufkdb.saveMessage[`Office; "proto/record_HQ"; HQ]
```

## Load Serialized Data

```q
q).protobufkdb.loadMessage[`Office; "proto/record_HQ"]
`Head_Office`Brian_Conlon_House
54.17913
-6.337371
2020.03.23
```

You can find more examples in :fontawesome-brands-github: [Protobufkdb repository](https://github.com/KxSystems/protobufkdb).
