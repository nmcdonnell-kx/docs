---
title: Function reference | Protobuf | Interfaces | Documentation for kdb+ and q
<<<<<<< HEAD
keywords: protobuf, protocol buffers, api, fusion, interface, q
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
---
# Protobuf/Protocol Buffers function reference

:fontawesome-brands-github: 
[KxSystems/protobufkdb](https://github.com/KxSystems/protobufkdb)
<br>

The following functions are exposed in the `.protobufkdb` namespace and allow you to interact with a protobuf message.

<pre markdown="1" class="language-txt">
.protobufkdb   **Protobuf/Protocol Buffers interface**

Library Information
  [init](#protobufkdbinit)               Checks that the version of the library that we linked against is compatible with the version of the headers we compiled against
  [version](#protobufkdbversion)         Returns the libprotobuf version as an integer
  [versionStr](#protobufkdbversionstr)   Returns the libprotobuf version as a char array

Import Schema
  [addProtoImportPath](#protobufkdbaddprotoimportpath)  Add a search path from which to import proto/schema files
  [importProtoFile](#protobufkdbimportprotofile)        Import the specified file
  [listImportedMessageTypes](#protobufkdblistimportedmessagetypes)  List all successfully imported message schemas

Inspect Schema
  [displayMessageSchema](#protobufkdbdisplaymessageschema)  Display the schema definistion of the message

Serialize / Deserialize
  [parseArray](#protobufkdbparsearray)                      Deserialize encoded protobuf character array with the specified schema
  [parseArrayArena](#protobufkdbparsearrayarena)            Deserialize encoded protobuf character array with the specified schema creating an intermediate protobuf message on google arena
  [serializeArray](#protobufkdbserializearray)              Serialize kdb+ object with the specified schema
  [serializeArrayArena](#protobufkdbserializearrayarena)    Serialize kdb+ object with the specified schema creating an intermediate protobuf message on google arena

Save / Load
  [loadMessage](#protobufkdbloadmessage)    Load the specified file and deserialize the encoded protobuf character array to kdb+ object
  [saveMessage](#protobufkdbsavemessage)    Serialize kdb+ object with the specified message and save it to the specified file
</pre>

## Library Information

### .protobufkdb.init

_Checks that the version of the library that we linked against is compatible with the version of the headers we compiled against._

Syntax: `.protobufkdb.init[]`

This function is called in `protobufkdb.q` to check the library compatibility. Not used by a user.

### .protobufkdb.version

_Returns the libprotobuf version as an integer._

Syntax: `.protobufkdb.version[]`

```q
q).protobufkdb.version[]
3012003i
```

### .protobufkdb.versionStr

_Returns the libprotobuf version as a char array_

Syntax: `.protobufkdb.versionStr[]`

```q
q).protobufkdb.versionStr[]
"libprotobuf v3.12.3"
```

## Import Schema

### .protobufkdb.addProtoImportPath

_Adds a path to be searched when dynamically importing `.proto` file definitions.  Can be called more than once to specify multiple import locations._

Syntax: `.protobufkdb.addProtoImportPath[import_path]`

Where:

- `import_path` is a string containing the path to be searched for `.proto` file definitions.  Can be absolute or relative.

```q
q).protobufkdb.addProtoImportPath["../proto"]
```

### .protobufkdb.importProtoFile

_Dynamically imports a `.proto` file definition into the interface, allowing the messages types defined in that file to be parsed and serialised by the interface._

Syntax: `.protobufkdb.importProtoFile[filename]`

Where:

- `filename` is a string containing the name of the `.proto` file to be imported.  Must not contain any directory specifiers - directory search locations should be setup up beforehand using `.protobufkdb.addProtoImportPath`

returns an error containing information on the errors and warnings which occurred if the file fails to parse.

```q
q).protobufkdb.importProtoFile["examples_dynamic.proto"]
q).protobufkdb.importProtoFile["exmples_dynamic.proto"]
'Import error: File not found.
File: 'exmples_dynamic.proto', line: -1, column: 0

  [0]  .protobufkdb.importProtoFile["exmples_dynamic.proto"]
       ^
```

### .protobufkdb.listImportedMessageTypes

_Returns a list of the message types which have been successfully imported._

Syntax: `.protobufkdb.listImportedMessageTypes[]`

returns symbol list of the successfully imported message types.
=======
description: Protobuf/Protocol Buffers kdb+ interface function reference
---
# Protobuf/Protocol Buffers function reference

:fontawesome-brands-github:
[KxSystems/protobufkdb](https://github.com/KxSystems/protobufkdb)


Functions exposed in the `.protobufkdb` namespace allow you to generate and parse Protobuf messages.

<div markdown="1" class="typewriter">
.protobufkdb   **Protobuf/Protocol Buffers interface**

Library Information
  [version](#protobufkdbversion)                   Return the libprotobuf version as an integer
  [versionStr](#protobufkdbversionstr)                Return the libprotobuf version as a string

Import Schema
  [addProtoImportPath](#protobufkdbaddprotoimportpath)        Add a path from which to import proto/schema files
  [importProtoFile](#protobufkdbimportprotofile)           Import a file
  [listImportedMessageTypes](#protobufkdblistimportedmessagetypes)  List successfully imported message schemas

Inspect Schema
  [displayMessageSchema](#protobufkdbdisplaymessageschema)      Display the schema definition of the message

Serialize / Deserialize
  [parseArray](#protobufkdbparsearray)                Deserialize encoded Protobuf string
  [parseArrayArena](#protobufkdbparsearrayarena)           Deserialize encoded Protobuf string, creating an
                            intermediate Protobuf message on Google Arena
  [serializeArray](#protobufkdbserializearray)            Serialize kdb+ object
  [serializeArrayArena](#protobufkdbserializearrayarena)       Serialize kdb+ object, creating an
                            intermediate Protobuf message on Google Arena

Save / Load
  [loadMessage](#protobufkdbloadmessage)               Load a file and deserialize it to a kdb+ object
  [saveMessage](#protobufkdbsavemessage)               Serialize kdb+ object with a message, save it to file
</div>


??? detail "Message types"

    Where a function takes a `message_type` parameter to specify the name of the message to be be processed, the interface first looks for that message type in the compiled messages. 

    If that fails it then searches the imported message definitions.  Only if the message type is not found in either is an error returned.


## `addProtoImportPath`

_Add an import path_


```txt
.protobufkdb.addProtoImportPath[import_path]
```

Where `import_path` is a path (absolute or relative) as a string

1.  adds it as a path to be searched when dynamically importing `.proto` file definitions
1.  returns generic null

Can be called more than once to specify multiple import locations.

```q
// successful execution
q).protobufkdb.addProtoImportPath["../proto"]

// incorrect data format provided as input
q).protobufkdb.addProtoImportPath[enlist 1f]
'"Specify import path"
```


## `displayMessageSchema`

_Display the Proto schema of a specified message_

```txt
.protobufkdb.displayMessageSchema[message_type]
```

Where `message_type` is the name of a message type as a string, 

1.  prints schema format to stdout 
1.  returns generic null

`message_type` must match a message name in its `.proto` definition.

??? warning "For use only in debugging"

    The schema is generated by the libprotobuf `DebugString()` functionality and displayed on stdout to preserve formatting and indentation.

```q
q).protobufkdb.displayMessageSchema[`ScalarExampleDynamic]
message ScalarExampleDynamic {
  int32 scalar_int32 = 1;
  double scalar_double = 2;
  string scalar_string = 3;
}
```


## `importProtoFile`

_Import a `.proto` file definition_


```txt
.protobufkdb.importProtoFile[filename]
```

Where `filename` is the name of a `.proto` file as a string

1.  dynamically imports the file definition into the interface
1.  returns generic null

Success allows the message types defined in the file to be parsed and serialized by the interface.

Signalled errors contain errors and warnings which occurred in parsing the file.

!!! warning "The filename may not include a path." 

    Define directory search locations beforehand with `.protobufkdb.addProtoImportPath`.

```q
// successful function invocation
q).protobufkdb.importProtoFile["examples_dynamic.proto"]

// proto file does not exist
q).protobufkdb.importProtoFile["not_a_file.proto"]
'Import error: File not found.
File: 'not_a_file.proto', line: -1, column: 0

  [0]  .protobufkdb.importProtoFile["not_a_file.proto"]
       ^
```


## `listImportedMessageTypes`

_List imported message types_

```txt
.protobufkdb.listImportedMessageTypes[]
```

Returns successfully imported message types as a symbol list.

!!! detail "The list does not contain message types compiled into the interface."
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q).protobufkdb.listImportedMessageTypes[]
`ScalarExampleDynamic`RepeatedExampleDynamic`SubMessageExampleDynamic`MapExam..
```

<<<<<<< HEAD
**Note:** The list does not contain message types which have been compiled into the interface.

## Inspect Schema

### .protobufkdb.displayMessageSchema

_Displays the proto schema of the specified message to console._

Syntax: `.protobufkdb.displayMessageSchema[message_type]`

Where: 

- `message_type` is a string containing the name of the message type.  Must be the same as the message name in its .proto definition.

```q
q).protobufkdb.displayMessageSchema[`ScalarExampleDynamic]
message ScalarExampleDynamic {
  int32 scalar_int32 = 1;
  double scalar_double = 2;
  string scalar_string = 3;
}
```

**Note:** This function is intended for debugging use only. The schema is generated by the libprotobuf DebugString() functionality and displayed on stdout to preserve the formatting and indentation.

## Serialize / Deserialize

### .protobufkdb.parseArray

_Parse a proto-serialized char array into a protobuf message, then converts that into a corresponding kdb object._

Syntax: `.protobufkdb.parseArray[message_type; char_array]`

Where:

- `message_type` is a string containing the name of the message type.  Must be the same as the message name in its .proto definition.
- `char_array` is a kdb char array containing the serialized protobuf message.

returns Kdb object corresponding to the protobuf message.
=======

## `loadMessage`

```txt
.protobufkdb.loadMessage[message_type;file_name]
```

Where:

-   `message_type` is a message type (string or symbol) matching a message name in the `.proto` definition
-   `file_name` is the name of a file (string or symbol)

returns a kdb+ object parsed from `file_name`.

```q
q)data
1  2
10 20
s1 s2
q).protobufkdb.saveMessage[`RepeatedExampleDynamic;`trivial_message_file;data]
q).protobufkdb.loadMessage[`RepeatedExampleDynamic;`trivial_message_file]
1  2
10 20
s1 s2
```


## `parseArray`

_Parse a proto-serialized string to a kdb+ object_

```txt
.protobufkdb.parseArray[message_type;char_array]
```

Where

-   `message_type` is a message type (string or symbol) matching a message name in the `.proto` definition
-   `char_array` is the serialized Protobuf message (string)

returns the message as a kdb+ object.
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q)data:(1 2i;10 20f;`s1`s2)
q)array:.protobufkdb.serializeArray[`RepeatedExampleDynamic;data]
q)array
"\n\002\001\002\022\020\000\000\000\000\000\000$@\000\000\000\000\000\0004@\0..
q).protobufkdb.parseArray[`RepeatedExampleDynamic;array]
<<<<<<< HEAD
1  2 
=======
1  2
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea
10 20
s1 s2
```

<<<<<<< HEAD
### .protobufkdb.parseArrayArena

Identical to `.protobufkdb.parseArray[]` except the intermediate protobuf message is created on a google arena (can help improves memory allocation performance for large messages with deep repeated fields/map - see [here](https://developers.google.com/protocol-buffers/docs/reference/arenas)).

Syntax: `.protobufkdb.parseArrayArena[message_type; char_array]`

Where:

- `message_type` is a string containing the name of the message type.  Must be the same as the message name in its .proto definition.
- `char_array` is a kdb char array containing the serialized protobuf message.

returns Kdb object corresponding to the protobuf message.
=======

## `parseArrayArena`

_Parse a proto-serialized string to a kdb+ object, saving intermediate message_

```txt
.protobufkdb.parseArrayArena[message_type;char_array]
```

Where:

-   `message_type` is a message type (string or symbol) matching a message name in the `.proto` definition
-   `char_array` is the serialized Protobuf message (string)

the function

1.  parses the string as a Protbuf message, which it creates on a Google Arena
2.  returns the message as a kdb+ object

!!! detail "Identical to `parseArray` except the intermediate Protobuf message is created on a Google Arena"

    Can improve memory allocation performance for large messages with deep repeated fields/map.

    :fontawesome-brands-google:
    [Google Arenas](https://developers.google.com/protocol-buffers/docs/reference/arenas)
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q)data:(1 2i;10 20f;`s1`s2)
q)array:.protobufkdb.serializeArray[`RepeatedExampleDynamic;data]
q)array
"\n\002\001\002\022\020\000\000\000\000\000\000$@\000\000\000\000\000\0004@\0..
q).protobufkdb.parseArrayArena[`RepeatedExampleDynamic;array]
<<<<<<< HEAD
1  2 
=======
1  2
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea
10 20
s1 s2
```

<<<<<<< HEAD
### .protobufkdb.serializeArray

_Convert a kdb object to a protobuf message and serialize this into a char array._

Syntax: `.protobufkdb.serializeArray[message_type; msg_in]`

Where:

- `message_type` is a string containing the name of the message type.  Must be the same as the message name in its .proto definition.
- `msg_in` is the kdb object to be converted.

returns Kdb char array containing the serialized message.
=======

## `saveMessage`

_Write a kdb+ object to file as a Protobuf message_

```txt
.protobufkdb.saveMessage[message_type;file_name;msg_in]
```

Where

-   `message_type` is a message type (string or symbol) matching a message name in the `.proto` definition
-   `file_name` is the name of a file (string or symbol)
-   `msg_in` is a kdb+ object 

the function 

1.  converts `msg_in` to a Protobuf message of `message_type`, serializes it, and writes it to `file_name`
2.  returns generic null

```q
q)data
1  2
10 20
s1 s2
q).protobufkdb.saveMessage[`RepeatedExampleDynamic;`trivial_message_file;data]
```


## `serializeArray`

_Serialize a kdb+ object as a Protobuf message_

```txt
.protobufkdb.serializeArray[message_type; msg_in]
```

Where:

-   `message_type` is a message type (string or symbol) matching a message name in the `.proto` definition
-   `msg_in` is a kdb+ object 

returns the serialized Protobuf message as a string.
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q)data:(1 2i;10 20f;`s1`s2)
q).protobufkdb.serializeArray[`RepeatedExampleDynamic;data]
"\n\002\001\002\022\020\000\000\000\000\000\000$@\000\000\000\000\000\0004@\0..
```

<<<<<<< HEAD
### .protobufkdb.serializeArrayArena

_Identical to `.protobufkdb.serializeArray[]` except the intermediate protobuf message is created on a google arena (can help improves memory allocation performance for large messages with deep repeated/map fields - see [here](https://developers.google.com/protocol-buffers/docs/reference/arenas))._

Syntax: `.protobufkdb.serializeArrayArena[message_type; msg_in]`

Where:

- `message_type` is a string containing the name of the message type.  Must be the same as the message name in its .proto definition.
- `msg_in` is the kdb object to be converted.

returns Kdb char array containing the serialized message.
=======

## `serializeArrayArena`

_Serialize a kdb+ object as a Protobuf message, saving it to a Google Arena_

```txt
.protobufkdb.serializeArrayArena[message_type;msg_in]
```

Where:

-   `message_type` is a message type (string or symbol) matching a message name in the `.proto` definition
-   `msg_in` is a kdb+ object 

the function

1.  serializes `msg_in` as a Protobuf message and creates it on a Google Arena
2.  returns the serialized Protobuf message as a string

!!! detail "Identical to `serializeArray` except the Protobuf message is created on a Google Arena"

    Can improve memory allocation performance for large messages with deep repeated fields/map.

    :fontawesome-brands-google:
    [Google Arenas](https://developers.google.com/protocol-buffers/docs/reference/arenas)
>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea

```q
q)data:(1 2i;10 20f;`s1`s2)
q).protobufkdb.serializeArrayArena[`RepeatedExampleDynamic;data]
"\n\002\001\002\022\020\000\000\000\000\000\000$@\000\000\000\000\000\0004@\0..
```

<<<<<<< HEAD
## Save / Load

### .protobufkdb.loadMessage

_Parse a proto-serialized stream from a specified file to a protobuf message then converts this into a corresponding kdb object._

Syntax: `.protobufkdb.loadMessage[message_type; file_name]`

Where:

- `message_type` is a string containing the name of the message type.  Must be the same as the message name in its .proto definition.
- `file_name` is a string containing the name of the file to read from.

returns a kdb object corresponding to the protobuf message.

```q
q)data
1  2 
10 20
s1 s2
q).protobufkdb.saveMessage[`RepeatedExampleDynamic;`trivial_message_file;data]
q).protobufkdb.loadMessage[`RepeatedExampleDynamic;`trivial_message_file]1  2 
10 20
s1 s2
```

### .protobufkdb.saveMessage

_Convert a kdb object to a protobuf message, serializes it, then write the result to the specified file._

Syntax: `.protobufkdb.saveMessage[message_type; file_name; msg_in]`

Where:

- `message_type` is a string containing the name of the message type.  Must be the same as the message name in its .proto definition.
- `file_name` is a string containing the name of the file to write to.
- `msg_in` is the kdb object to be converted.

```q
q)data
1  2 
10 20
s1 s2
q).protobufkdb.saveMessage[`RepeatedExampleDynamic;`trivial_message_file;data]
```
=======



## `version`

_Library version (integer) used by the interface_

```txt
.protobufkdb.version[]
```

returns the version of the `libprotobuf` shared object used by the interface, as an integer.

```q
q).protobufkdb.version[]
3012003i
```


## `versionStr`

_Library version (string) used by the interface_

```txt
.protobufkdb.versionStr[]
```

returns the version of the `libprotobuf` shared object used by the interface, as a string.

```q
q).protobufkdb.versionStr[]
"libprotobuf v3.12.3"
```


>>>>>>> 486378a4ccc66bb987a788e2fd05c953a6470fea
