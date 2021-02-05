# Type mapping between Arrow and kdb+

The data layout of an Arrow table is defined by its schema.  The schema is composed from a list of fields, one for each column in the table.  The field  describes the name of the column and its datatype. This page examines each of these and details how they are mapped in kdb+.

:fontawesome-brands-github: 
[KxSystems/arrowkdb](https://github.com/KxSystems/arrowkdb)

## Arrow Datatypes

Currently Arrow supports over 35 datatypes including concrete, parameterized and nested datatypes.

Similar to the C++ Arrow library and pyarrow, `arrowkdb` exposes the Arrow datatype constructors to q.  When one of these constructors is called it will return an integer datatype identifier which can then be passed to other functions, e.g. when creating a field.

### Concrete Datatypes

These cover the datatypes where there is a single fixed representation of that datatype.

| **Arrow Datatype** | **Description**                                         | **Kdb+ representation of Arrow array**               |
| ------------------ | ------------------------------------------------------- | ---------------------------------------------------- |
| na                 | NULL type having no physical storage                    | Mixed list of empty lists                            |
| boolean            | Boolean as 1 bit, LSB bit-packed ordering               | 1h                                                   |
| uint8              | Unsigned 8-bit little-endian integer                    | 4h                                                   |
| int8               | Signed 8-bit little-endian integer                      | 4h                                                   |
| uint16             | Unsigned 16-bit little-endian integer                   | 5h                                                   |
| int16              | Signed 16-bit little-endian integer                     | 5h                                                   |
| uint32             | Unsigned 32-bit little-endian integer                   | 6h                                                   |
| int32              | Signed 32-bit little-endian integer                     | 6h                                                   |
| uint64             | Unsigned 64-bit little-endian integer                   | 7h                                                   |
| int64              | Signed 64-bit little-endian integer                     | 7h                                                   |
| float16            | 2-byte floating point value (populated from uint16_t)   | 5h                                                   |
| float32            | 4-byte floating point value                             | 8h                                                   |
| float64            | 8-byte floating point value                             | 9h                                                   |
| utf8               | UTF8 variable-length string                             | Mixed list of 10h                                    |
| large_utf8         | Large UTF8 variable-length string                       | Mixed list of 10h                                    |
| binary             | Variable-length bytes (no guarantee of UTF8-ness)       | Mixed list of 4h                                     |
| large_binary       | Large variable-length bytes (no guarantee of UTF8-ness) | Mixed list of 4h                                     |
| date32             | int32_t days since the UNIX epoch                       | 14h (with automatic epoch offsetting)                |
| date64             | int64_t milliseconds since the UNIX epoch               | 12h (with automatic epoch offsetting and ms scaling) |
| month_interval     | Interval described as a number of months                | 13h                                                  |
| day_time_interval  | Interval described as number of days and milliseconds   | 16h (with automatic ns scaling)                      |



### Parameterized Datatypes

These represent multiple logical interpretations of the underlying physical data, where each parameterized interpretation is a distinct datatype in its own right.

| **Arrow Datatype**            | **Description**                                              | **Kdb+ representation of Arrow array**                     |
| ----------------------------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| fixed_size_binary(byte_width) | Fixed-size binary. Each value occupies the same number of bytes. | Mixed list of 4h                                           |
| timestamp(time_unit)          | Exact timestamp encoded with int64_t (as number of seconds, milliseconds, microseconds or nanoseconds since UNIX epoch) | 12h (with automatic epoch offsetting and TimeUnit scaling) |
| time32(time_unit)             | Time as signed 32-bit integer, representing either seconds or milliseconds since midnight | 19h (with automatic TimeUnit scaling)                      |
| time64(time_unit)             | Time as signed 64-bit integer, representing either microseconds or nanoseconds since midnight | 16h (with automatic TimeUnit scaling)                      |
| duration(time_unit)           | Measure of elapsed time in either seconds, milliseconds, microseconds or nanoseconds | 16h (with automatic TimeUnit scaling)                      |
| decimal128(precision, scale)  | Precision- and scale-based signed 128-bit integer in two's complement | Mixed list of 4h (each of length 16)                       |



### Nested Datatypes

These are used to define higher level groupings of either the child datatypes or its constituent fields (a field specifies its datatype and the field's name).

| **Arrow Datatype**                               | **Description**                                              | **Kdb+ representation of Arrow array**                       |
| ------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| list(datatype_id)                                | List datatype specified in terms of its child datatype       | Mixed list for the parent list array containing a set of sub-lists (of type determined by the child datatype), one for each of the list value sets |
| large_list(datatype_id)                          | Large list datatype specified in terms of its child datatype | Mixed list for the parent list array containing a set of sub-lists (of type determined by the child datatype), one for each of the list value sets |
| fixed_size_list(datatype_id, list_size)          | Fixed size list datatype specified in terms of its child datatype and the fixed size of each of the child lists | Same as variable length lists, except each of the sub-lists must be of length equal to the list_size |
| map(key_datatype_id, item_datatype_id)           | Map datatype specified in terms of its key and item child datatypes | Mixed list for the parent map array, with a dictionary for each map value set |
| struct(field_ids)                                | Struct datatype specified in terms of a list of its constituent child field identifiers | Mixed list for the parent struct array, containing child lists for each field in the struct |
| dictionary(value_datatype_id, index_datatype_id) | A dictionary datatype specified in terms of its value and index datatypes, similar to pandas categorical | Two item item mixed list, first item contains the values list and the second item contains the indices list. |
| sparse_union(field_ids)                          | Union datatype specified in terms of a list of its constituent child field identifiers | Similar to a struct array except the mixed list has an additional type_id array (5h) at the start which identifies the live field in each union value set |
| dense_union(field_ids)                           | Union datatype specified in terms of a list of its constituent child field identifiers | Similar to a struct array except the mixed list has an additional type_id array (5h) at the start which identifies the live field in each union value set |



### Inferred Datatypes

It is also possible to have `arrowkbd` infer a suitable Arrow datatype from the type of a kdb+ list.  Similarly Arrow schemas can be inferred from a kdb+ table.  This approach is easier to use but only supports a subset of the Arrow datatypes and is considerably less flexible.  Inferring Arrow datatypes is suggested if you are less familiar with Arrow or do not wish to use the more complex or nested Arrow datatypes.

| Kdb+ list type    | Inferred Arrow Datatype | Notes                                         |
| ----------------- | ----------------------- | --------------------------------------------- |
| 1h                | boolean                 |                                               |
| 2h                | fixed_size_binary(16)   | Writing path only, reads as mixed list of 4h  |
| 4h                | int8                    |                                               |
| 5h                | int16                   |                                               |
| 6h                | int32                   |                                               |
| 7h                | int64                   |                                               |
| 8h                | float32                 |                                               |
| 9h                | float64                 |                                               |
| 10h               | int8                    | Writing path only, reads as 4h                |
| 11h               | utf8                    | Writing path only, reads as mixed list of 10h |
| 12h               | timestamp(nano)         |                                               |
| 13h               | month_interval          |                                               |
| 14h               | date32                  |                                               |
| 15h               | NA                      | Cast in q with `timestamp$                    |
| 16h               | time64(nano)            |                                               |
| 17h               | NA                      | Cast in q with `time$                         |
| 18h               | NA                      | Cast in q with `time$                         |
| 19h               | time32(milli)           |                                               |
| Mixed list of 4h  | binary                  |                                               |
| Mixed list of 10h | utf8                    |                                               |

??? warning "The inference only works for a trivial kdb lists containing simple datatypes"

	Only mixed lists of char arrays or byte arrays are supported, mapped to Arrow utf8 and binary datatypes respectively.  Other mixed list structures (e.g. those used by the nested arrow datatypes) cannot be interpreted - if required these should be created manually using the datatype constructors



### Parquet Datatype Limitations

The Parquet file format is less fully featured compared to Arrow and consequently the Arrow/Parquet file writer currently does not support some datatypes or represents them using a different datatype:

| Arrow Datatype    | Status as of apache-arrow-2.0.0                         |
| ----------------- | ------------------------------------------------------- |
| float16           | Unsupported                                             |
| month_interval    | Unsupported                                             |
| day_time_interval | Unsupported                                             |
| duration          | Unsupported                                             |
| large_utf8        | Unsupported                                             |
| large_binary      | Unsupported                                             |
| sparse_union      | Unsupported                                             |
| dense_union       | Unsupported                                             |
| date64            | Mapped to date32(days)                                  |
| fixed_size_list   | Mapped to list                                          |
| dictionary        | Categorical representation stored                       |
| uint32            | Parquet v2.0 only, otherwise mapped to int64            |
| timestamp(nano)   | Parquet v2.0 only, otherwise mapped to timestamp(milli) |



## Arrow Fields

An Arrow field describes a column in the table and is composed of a datatype and a string field name.

Similar to the C++ Arrow library and pyarrow, `arrowkdb` exposes the Arrow field constructor to q.  The field constructor takes the field name and its datatype identifier and will return an integer field identifier which can then be passed to other functions, e.g. when creating a schema.



## Arrow Schemas

An Arrow schema is built up from a list of fields and is used when working with table data.  The datatype of each field in the schema determines the array data layout for that column in the table.

Similar to the C++ Arrow library and pyarrow, `arrowkdb` exposes the Arrow schema constructor to q.  The schema constructor takes a list of field identifiers and will return an integer schema identifier which can then be passed to other functions, e.g. when writing Arrow or Parquet files.

If you are less familiar with Arrow or do not wish to use the more complex or nested Arrow datatypes, `arrowkdb` can infer the schema from a kdb+ table.  Each column in the table is mapped to a field in the schema.  The column's name is used as the field name and the field's datatype is [inferred from the column's kdb+ type](arrow-types/#inferred-datatypes).



## Arrow Tables

An Arrow table is composed from a schema and a mixed list of Arrow array data kdb objects:

- The array data for each column in the table is then populated using a builder object specific to that field's datatype
- Similarly datatype specific reader objects are used to interpret and inspect the array data for each column in the table

The mixed list of Arrow array data kdb objects should be ordered in schema field number.  Each kdb object representing one of the arrays must be structured according to the field's datatype.  This required array data structure is detailed above for each of the datatypes.

Alternatively, separate APIs are provided where the Arrow table is created from a kdb+ table using an inferred schema.
