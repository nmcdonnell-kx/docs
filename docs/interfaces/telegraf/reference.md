---
title: Function reference | Telegraf Handler | Interfaces | Documentation for kdb+ and q
keywords: ffi, api, fusion, interface, q
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
---

# Telegraf/kdb+ handler function reference

:fontawesome-brands-github:
[KxSystems/telegraf_kdb_handler](https://github.com/KxSystems/telegraf_kdb_handler)

The following functions are exposed in `.telegraf` namespace.

<div markdown="1" class="typewriter">
.telegraf. **Telegraf Handler**

Functionality
  [init](#telegrafinit)                   initialise either q/C++ functionality.
  [handler](#telegrafhandler)                Converts a batch of Telegraf messages into a kdb+ structure.
  [getChunkSizeThreshold](#telegrafgetchunksizethreshold)  Get the size above which messages are parsed asynchronously.
  [setChunkSizeThreshold](#telegrafsetchunksizethreshold)  Set the size above which messages are parsed asynchronously.
  [saveCurrentSchema](#telegrafsavecurrentschema)      Save current schema for tables as a text file.
</div>

## `.telegraf.init`

_Initialise interface functionality to use C++/q implementations._

```txt
.telegraf.init[use_cxx]
```

Where

- `use_cxx` is a boolean atom; `1b` if using the C++ parser; `0b` if using the q parser.

returns null on successful execution, defining underlying interface functionality

```q
// Initialise interface to use the C++ functionality
q).telegraf.init[1b]
q).telegraf[`getChunkSizeThreshold`setChunkSizeThreshold`parse_map]
code
code
"JFS*"!({"J"$-1 _/: x};$["F"];$["S"];k){x'y}[{$[count x; -1 _ 1 _ x; x]}])

// Initialise interface to use the q functionality
q).telegraf.init[0b]
q).telegraf[`getChunkSizeThreshold`setChunkSizeThreshold`parse_map]
{'"undefined"}
{'"undefined"}
"PJFS*"!($["P"];{"J"$-1 _/: x};$["F"];$["S"];::)
```

## `.telegraf.handler`

_Converts a batch of telegraf line protocol messages into a list containing (table name;table data)._

```txt
.telegraf.handler[message]
```

Where

- `message`: A batch of telegraf messages separated by "\n" and preceded by the associated endpoint.

returns a tuple list containing (table name; table data)

```q
q)test_message
"telegraf/kdb system,host=thunderchild.team.savvi.io uptime_format=\"142 days, 22:54\" 1601289566000000000\nsystem,host=thunderchild.team.savvi.io uptime=12351247i 1601289566000000000\nsystem,host=thunderchild.team.savvi.io load15=0.3,n_cpus=56i,n_users=0i,load1=0.26,load5=0.48 1601289566000000000\ndisk,device=nvme0n1p1,fstype=ext4,host=thunderchild.team.savvi.io,mode=ro,path=/etc/telegraf/telegraf.conf free=210399662080i,used=746372100096i,used_percent=78.00941975947482,inodes_total=62513152i,inodes_free=61206760i,inodes_used=1306392i,total=1007998959616i 1601289566000000000\n"
q).telegraf.handler test_message
`events_system +`time`host`uptime_format`uptime`load15`n_cpus`n_users`load1`load5!(2020.09.28D10:39:26.000000000 2020.09.28D10:39:26.000000000 2020.09.28D10:39:26.000000000;`thunderchild.team.savvi.io`thunderchild.team.savvi.io`thunderchild.team.savvi.io;("142 days, 22:54";"";"");0N 12351247 0N;0n 0n 0.3;0N 0N 56;0N 0N 0;0n 0n 0.26;0n 0n 0.48)
`events_disk   +`time`device`fstype`host`mode`path`free`used`used_percent`inodes_total`inodes_free`inodes_used`total!(,2020.09.28D10:39:26.000000000;,`nvme0n1p1;,`ext4;,`thunderchild.team.savvi.io;,`ro;,`/etc/telegraf/telegraf.conf;,210399662080;,746372100096;,78.00942;,62513152;,61206760;,1306392;,1007998959616)
q)first[.telegraf.handler test_message] 0
`events_system
q)first[.telegraf.handler test_message] 1
host                       uptime_format     time                          uptime   load15 n_cpus n_users load1 load5
---------------------------------------------------------------------------------------------------------------------
thunderchild.team.savvi.io "142 days, 22:54" 2020.09.28D10:39:26.000000000                                           
thunderchild.team.savvi.io ""                2020.09.28D10:39:26.000000000 12351247                                  
thunderchild.team.savvi.io ""                2020.09.28D10:39:26.000000000          0.3    56     0       0.26  0.48 
```

!!! warning "Parser differences"

	There is presently a functional difference between results parsed using the q native parser vs the C++ parser. Parsing using the C++ version leaves "\" within messages and as a result subsequent processing can distinguish if a message is a string or not. However the q native parser by default removes "\" and therefore subsequent processing cannot distinguish if an item is a string or not by default. This is a problem when dealing with evolving schemas in particular. In the case that a user defines the type of a parsed element as string in the schema file (telegraf_kdb_schema.q) then strings can be handled by the q parser. As such parsing to string types is only avaiable in both parsers when using pre-defined schemas.

## `.telegraf.getChunkSizeThreshold`

_Get the message size in bytes above which parsing is processed asynchronously._

```txt
.telegraf.getChunkSizeThreshold[]
```

returns a long indicating the message size in bytes above which messages are processed asynchronously

!!! warning "Differences between q and C++ implementations"
	
	Processing messages asynchronously is only supported when using the C++ implementation of the interface. In the case that the q parser is to be used this function is undefined

```q
// Initialise interface to use the C++ parser
q).telegraf.init[1b]
q).telegraf.getChunkSizeThreshold[]
25000

// Initialise interface to use the q parser
q).telegraf.init[0b]
'undefined
  [0]  .telegraf.getChunkSizeThreshold[]
       ^
```

## `.telegraf.setChunkSizeThreshold`

_Set the message size in bytes above which parsing is processed asynchronously._

```txt
.telegraf.setChunkSizeThreshold[new_threshold]
```

Where

- `new_threshold` is a long indicating new chunk size threshold of q long type.

returns a null on successful invocation otherwise will error.

!!! warning "Undefined behaviour"
	
	Invocation of this function is unsupported in two scenarios.
	
	1. If the shared library was build from source with the `FIXED_CHUNK_SIZE` set. See [here](https://github.com/KxSystems/telegraf_kdb_handler#installation) for more information

	2. If using the q parser, the option to process messages asynchronously is not supported as such this function is undefined.

```q
// Interface initialised using the C++ parser with FIXED_CHUNK_SIZE not set
q).telegraf.getChunkSizeThreshold[]
25000
q).telegraf.setChunkSizeThreshold[28000]
q).telegraf.getChunkSizeThreshold[]
28000

// Interface initialised using the C++ parser with a FIXED_CHUNK_SIZE set
q).telegraf.setChunkSizeThreshold[28000]
'chunk size threshold is fixed
  [0]  .telegraf.setChunkSizeThreshold[28000]

// Interface initialised using the q parser
q).telegraf.setChunkSizeThreshold[28000]
'undefined
  [0]  .telegraf.setChunkSizeThreshold[28000]
       ^
```

## `.telegraf.saveCurrentSchema`

_Save the current in process schema map for each table prefixed with the appropriate endpoint into a .txt file._

```txt
.telegraf.saveCurrentSchema[file]
```

Where

- `file` is a symbol file name (hsym) which the schema configuration file is to be saved as relative to the current directory. The file extension used must be `.txt`.

returns null on successful invocation file and generates a file containing the appropriate schema definition for the current schema map defined in process.

!!! Note "Console width change"

	When saving the schema console width will temporarily be changed to 1000:1000

```q
// Save schema file for schemas currently defined in process
q).telegraf_influx.saveCurrentSchema[`:schema.txt]
`:schema.txt
q)\cat schema.txt
"(`diagnostics;`time`table`fleet`model`name`driver`device_version`load_capacit..
"(`readings;`time`table`name`fleet`driver`model`device_version`load_capacity`f..
"(`system;`time`table`host`uptime`uptime_format`load1`load5`load15`n_cpus`n_us..
"(`diskio;`time`table`host`name`reads`read_time`weighted_io_time`iops_in_progr..
"(`process;`time`table`host`running`sleeping`dead`paging`blocked`zombies`stopp..
"(`swap;`time`table`host`total`used`free`used_percent`in`out!\"PSSJJJFJJ\")"
"(`cpu;`time`table`cpu`host`usage_iowait`usage_irq`usage_softirq`usage_guest_n..
"(`kernel;`time`table`host`context_switches`boot_time`processes_forked`entropy..
"(`processes;`time`table`host`paging`idle`blocked`zombies`stopped`running`slee..
"(`mem;`time`table`host`used`buffered`huge_pages_total`available`huge_page_siz..

// Attempt to save a file with an incorrect extension
q).telegraf_influx.saveCurrentSchema[`:schema.json]
'File extension must be '.txt'.
  [0]  .telegraf_influx.saveCurrentSchema[`:schema.json]
       ^
```
