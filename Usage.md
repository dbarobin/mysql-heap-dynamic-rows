# Using Dynamic Row Format #

The new feature is exposed through block\_size and row format options.

For example:

```
create table t1 (
    a int not null,
    b int,
    c varchar(400),
    d varchar(400),
    primary key (a), key (b))
  engine=heap block_size=24;
```

```
create table t1 (
    a int not null,
    b int,
    c varchar(400),
    d varchar(400),
    primary key (a), key (b))
  engine=heap row_format=dynamic;
```

Note that `row_format=dynamic` will use `block_size` of 240.

Use `"show table status"` to find average per-row memory consumption. Note that in a table with few rows, per-row memory consumption will show high numbers due to larger `HP_BLOCK` allocation granularity.

As always, don't forget to set `max_heap_table_size` and `max_rows` properly.

## Current Limitations ##

  1. All columns used as keys have to fit into the first chunk (which also means that you should avoid creating indexes on VARCHAR columns). This greatly simplifies functions that scan based on the index. Heap Engine's Hash index data does not duplicate any part of record data, and so Engine relies on fast access to actual records to verify matches, which is why key columns are never stored in compact format and have to be in the first chunk.
  1. Column definitions in "create table" statement have to be properly ordered. All columns used in keys have to be listed first. This type of ordering will cause key columns to be in the beginning of the record data, and so they will end up in the first chunk (see limitation #1). Automatic reordering (which would resolve the inconvenience) would require two parallel structures for index definition, because there would be two formats of record in the same table - a) original, used throughout all of MySQL, passed into the Heap Engine, b) reordered, internal, used inside Heap Engine code only.