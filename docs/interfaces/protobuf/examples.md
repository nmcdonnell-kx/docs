---
title: Interface example | Protobuf | Interfaces | Documentation for kdb+ and q
<<<<<<< HEAD
keywords: protobuf, protocol buffers, api, fusion, interface, q
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
---
# Protobuf/Protocol Buffers Interface Example
=======
description: Examples of interfacing kdb+ and Protobuf
---
# Protobuf/Protocol Buffers interface example
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

:fontawesome-brands-github:
[KxSystems/protobufkdb](https://github.com/KxSystems/protobufkdb)

<<<<<<< HEAD
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
=======
It is assumed in the example that you are executing logic in the root of the `protobufkdb` repository. The file structure for the required components from this location is:
 
```treeview
./
├── proto/
│   ├── google
│   ├── kdb_type_specifier.proto
│   └── sample.proto
└── q/
    └── protobufkdb.q
```

Here `sample.proto` is defined as follows:

```protobuf
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea
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

<<<<<<< HEAD
## Load Protobufkdb Library
=======

## Load Protobufkdb library

From the root of the repository load the `protobufkdb` library
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q)\l q/protobufkdb.q
```

<<<<<<< HEAD
## Import Schema File

_This procedure is not necessary when you are using a library built from source linking schema files. In using dynamic import functionality you need to specify the path of your proto (schema) files first._

Tell protobufkdb the location of schema files specifying its path (`./proto`) and then import a specific file (`sample.proto`).
=======

## Import schema file

When using dynamic import functionality, specify the path of your Proto (schema) files first. As such we tell `protobufkdb` the location of schema files of interest, specifying its path (`"./proto"`) and importing the relevant file (`"sample.proto"`).

!!! detail "This step is not necessary when using a library built from source, where schema files are linked appropriately."
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q).protobufkdb.addProtoImportPath["proto"]
q).protobufkdb.importProtoFile["sample.proto"]
```

<<<<<<< HEAD
## Checking Schema File
=======

## Check schema file

Ensure the schema file has been imported appropriately. 
Display the schemas in it.
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

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

<<<<<<< HEAD
## Serialize Data

Protobuf message is expressed in a mixed list in q. You can serialize the data with a target schema name.

```q
q)HQ:((`$"Head_Office"; `$"Brian_Conlon_House"); 54.179127; -6.337371; 2020.03.23)
q)encodedHQ:.protobufkdb.serializeArray[`Office; HQ]
q)encodedHQ
"\n\013Head_Office\n\022Brian_Conlon_House\021Qj/\242\355\026K@\031\253y\216\..
q)newry:((`$"Newry_Office"; `$"The_Conlon_Building"); 54.1751772; -6.3378739; 2020.08.14)
=======

## Serialize data

A Protobuf message is expressed in a mixed list in q. This data can be serialized to Protobuf provided the message content matches the format of a named target schema.

```q
// Serialize the details of an office to a Protobuf array
q)HQ:(`Head_Office`Brian_Conlon_House; 54.179127; -6.337371; 2020.03.23)
q)encodedHQ:.protobufkdb.serializeArray[`Office; HQ]
q)encodedHQ
"\n\013Head_Office\n\022Brian_Conlon_House\021Qj/\242\355\026K@\031\253y\216\..

// Serialize the details of all offices in a region to a Protobuf array using Arenas
q)newry:(`Newry_Office`The_Conlon_Building; 54.1751772; -6.3378739; 2020.08.14)
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea
q)belfast:(enlist `Belfast; 54.592595; -5.927475; 2020.08.14)
q)EMEA:`HQ`Newry`Belfast!(HQ; newry; belfast)
q)encodedEMEA:.protobufkdb.serializeArrayArena[`Region; (`EMEA; EMEA)]
q)encodedEMEA
"\n\004EMEA\022<\n\002HQ\0226\n\013Head_Office\n\022Brian_Conlon_House\021Qj/..
```

<<<<<<< HEAD
## Deserialize Data

```q
=======

## Deserialize data

Retrieve the contents of a Protobuf message serialized in the above example

```q
// Retrieve encoded information about an office from a Protobuf message
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea
q).protobufkdb.parseArray[`Office; encodedHQ]
`Head_Office`Brian_Conlon_House
54.17913
-6.337371
2020.03.23
<<<<<<< HEAD
=======

// Retrieve encoded information about a region from a Protobuf message using Arenas
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea
q).protobufkdb.parseArrayArena[`Region; encodedEMEA]
`EMEA
`HQ`Newry`Belfast!((`Head_Office`Brian_Conlon_House;54.17913;-6.337371;2020.0..
```

<<<<<<< HEAD
## Save Serialized Data

You can serialize and save data to a file sepcifying a target schema and the target file name.
=======

## Save serialized data

You can serialize and save data to a file by specifying a target schema and the target file name.
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q).protobufkdb.saveMessage[`Office; "proto/record_HQ"; HQ]
```

<<<<<<< HEAD
## Load Serialized Data
=======

## Load serialized data

Retrieve data serialized and saved to a file based on a specified schema.
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q).protobufkdb.loadMessage[`Office; "proto/record_HQ"]
`Head_Office`Brian_Conlon_House
54.17913
-6.337371
2020.03.23
```

<<<<<<< HEAD
You can find more examples in :fontawesome-brands-github: [Protobufkdb repository](https://github.com/KxSystems/protobufkdb).
=======

More examples:
:fontawesome-brands-github: 
[protobufkdb/examples](https://github.com/KxSystems/protobufkdb/tree/master/examples)


>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea
