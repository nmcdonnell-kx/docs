---
title: Function reference | Telegraf Handler | Interfaces | Documentation for kdb+ and q
keywords: ffi, api, fusion, interface, q
hero: <i class="fab fa-superpowers"></i> Fusion for Kdb+
---

:fontawesome-brands-github:
[KxSystems/telegraf_kdb_handler](https://github.com/KxSystems/telegraf_kdb_handler)

# telegraf kdb handler function reference

The following functions are exposed in `telegraf_kdb` namespace.

<pre markdown="1" class="language-text">
.telegraf_kdb **Telegraf Handler**

    [handler](#telegrafkdbhandler)                                  Converts a batch of telegraf line protocol messages into list of tuple of (table name; table).
    [get_chunk_size_threshold](#telegraf_kdbchunk_size_threshold)   Get current chunk size threshold, message with size above which is processed asynchronously.
    [set_chunk_size_threshold](#telegraf_kdbchunk_size_threshold)   Set chunk size threshold, message with size above which is processed asynchronously.
    [save_current_schema](#telegraf_kdbsave_current_schema)         Save current schema for tables as a text file.
</pre>

### `.telegraf_kdb.handler`

_Converts a batch of telegraf line protocol messages into list of tuple of (table name; table data)._

Synatx: `.telegraf_kdb.handler[message]`

Where

- `message`: Batch telegraf lines separated by "\n" and preceded by endpoint.

Return

- list of tuple of (table name; table data).

```q

q)test_message
"telegraf/kdb system,host=thunderchild.team.savvi.io uptime_format=\"142 days, 22:54\" 1601289566000000000\nsystem,host=thunderchild.team.savvi.io uptime=12351247i 1601289566000000000\nsystem,host=thunderchild.team.savvi.io load15=0.3,n_cpus=56i,n_users=0i,load1=0.26,load5=0.48 1601289566000000000\ndisk,device=nvme0n1p1,fstype=ext4,host=thunderchild.team.savvi.io,mode=ro,path=/etc/telegraf/telegraf.conf free=210399662080i,used=746372100096i,used_percent=78.00941975947482,inodes_total=62513152i,inodes_free=61206760i,inodes_used=1306392i,total=1007998959616i 1601289566000000000\n"
q).telegraf_kdb.handler test_message
`events_system +`time`host`uptime_format`uptime`load15`n_cpus`n_users`load1`load5!(2020.09.28D10:39:26.000000000 2020.09.28D10:39:26.000000000 2020.09.28D10:39:26.000000000;`thunderchild.team.savvi.io`thunderchild.team.savvi.io`thunderchild.team.savvi.io;("142 days, 22:54";"";"");0N 12351247 0N;0n 0n 0.3;0N 0N 56;0N 0N 0;0n 0n 0.26;0n 0n 0.48)
`events_disk   +`time`device`fstype`host`mode`path`free`used`used_percent`inodes_total`inodes_free`inodes_used`total!(,2020.09.28D10:39:26.000000000;,`nvme0n1p1;,`ext4;,`thunderchild.team.savvi.io;,`ro;,`/etc/telegraf/telegraf.conf;,210399662080;,746372100096;,78.00942;,62513152;,61206760;,1306392;,1007998959616)
q)first[.telegraf_kdb.handler test_message] 0
`events_system
q)first[.telegraf_kdb.handler test_message] 1
host                       uptime_format     time                          uptime   load15 n_cpus n_users load1 load5
---------------------------------------------------------------------------------------------------------------------
thunderchild.team.savvi.io "142 days, 22:54" 2020.09.28D10:39:26.000000000                                           
thunderchild.team.savvi.io ""                2020.09.28D10:39:26.000000000 12351247                                  
thunderchild.team.savvi.io ""                2020.09.28D10:39:26.000000000          0.3    56     0       0.26  0.48 

```

### `.telegraf_kdb.get_chunk_size_threshold`

_Get current chunk size threshold, message with size above which is processed asynchronously._

Syntax: `.telegraf_kdb.get_chunk_size_threshold[]`

Returns

- long

```q

q).telegraf_kdb.get_chunk_size_threshold[]
28000

```

### `.telegraf_kdb.set_chunk_size_threshold`

_Set chunk size threshold, message with size above which is processed asynchronously._

Syntax: `.telegraf_kdb.set_chunk_size_threshold[new_threshold]`

Where

- `new_threshold` is a new chunk size threshold of q long type.

```q

q).telegraf_kdb.set_chunk_size_threshold[25000]
q)

```

If the shared library was built from source with `FIXED_CHUNK_SIZE` (See [Installation guide](https://github.com/KxSystems/telegraf_kdb_handler#install)) flag, this function will rteturn error.

```q

q).telegraf_kdb.set_chunk_size_threshold[25000]
'chunk size threshold is fixed
  [0]  .telegraf_kdb.set_chunk_size_threshold[25000]
       ^

```

### `.telegraf_kdb.save_current_schema`

_Save curent schema of each table which has a prefix defined in `:telegraf_influx_schema.q into a file in the form the schema file specifies._

Syntax: `.telegraf_kdb.save_current_schema[file]`

Where

- `file` is a symbol file name to which schema is saved. The extension must be `.txt`.

*Note: When saving the schema console width will be temporarily changed to 1000:1000.*

```q

q).telegraf_influx.save_current_schema[`:schema.txt]
`:schema.txt
q).telegraf_influx.save_current_schema[`:schema.json]
'File extension must be '.txt'.
  [0]  .telegraf_influx.save_current_schema[`:schema.json]
       ^

```
