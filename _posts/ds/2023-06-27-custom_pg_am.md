---
title: PostgreSQL and Custom Table Access Methods
published: true
tags: postgres, C, database, disk, storage
---

***
## PostgreSQL Table Access Methods (TAMs)

### Why PostgreSQL?
PostgreSQL is the most popular open source database, the only one that has a positive trend in usage, and the backbone of a large number of commercial products.  I'm not going to go spend much time talking about why digging into PostgreSQL is worthwhile, that is already well-trodden ground.

### Pluggable Table Storage
In PostgreSQL 12, a new API was added to allow for implementing custom storage methods. Prior to this, data was only ever stored in "heap" tables, which are a well understood format.  The goal of this was to support new implementations, such as columnar or in-memory tables, that server different use cases than the heap table format is able to.

Implementing custom behavior for stored data is also possible using Foreign Data Wrappers and
Extensions. In comparison to those approaches, there are some strengths and weaknesses to using the
Table AM API.

### Pros 
* Much tighter integration with all the core server mechanisms 
    * query planning
    * autovacuum
    * autoanalyze
* Better performance when interacting with the rest the server

### Cons
* Must be compiled into the PostgreSQL binary, cannot be added at runtime
* Some limitations on the functionality available (e.g. must be tuple-based).

For this article, we'll be implementing a simple storage method of our own to demonstrate how to use this API.

## User Interface
To create a new access method, a user invokes the correct `CREATE` call, and indicates the desired
handler from those available in `pg_am`.
```sql
CREATE ACCESS METHOD my_heap TYPE TABLE HANDLER heap_tableam_handler;
```

To use this newly created access method when creating a table, the `USING` clause is provided.
```sql
CREATE TABLE foo_table(client_id int, desc text) USING my_heap ...; 
```

## Implementation
The TAM API has a several callback functions that must have an implementation provided

### Core API Callbacks
* slot_callbacks 
* scan_begin 
* scan_end 
* scan_rescan 
* scan_getnextslot 

### these are common helper functions
* parallelscan_estimate 
* parallelscan_initialize
* parallelscan_reinitialize
* index_fetch_begin 
* index_fetch_reset 
* index_fetch_end = blackhole_index_fetch_end,
* index_fetch_tuple
* tuple_insert 
* tuple_insert_speculative
* tuple_complete_speculative
* multi_insert =
* tuple_delete
* tuple_update
* tuple_lock
* finish_bulk_insert
* tuple_fetch_row_version
* tuple_get_latest_tid
* tuple_tid_valid
* tuple_satisfies_snapshot
* index_delete_tuples
* relation_set_new_filelocator
* relation_nontransactional_truncate
* relation_copy_data
* relation_copy_for_cluster
* relation_vacuum
* scan_analyze_next_block
* scan_analyze_next_tuple
* index_build_range_scan
* index_validate_scan
* relation_size
* relation_needs_toast_table
* relation_estimate_size
* scan_bitmap_next_block
* scan_bitmap_next_tuple
* scan_sample_next_block
* scan_sample_next_tuple


## Resources Reference
In building this project out, I had to read and learn quite a lot.  Here is a lit of the resources
I found helpful, both to give credit and in case they're helpful for you in working with this
material also.
* [Fujitsu TAM Blog](https://www.postgresql.fastware.com/blog/postgresql-table-access-methods)
* [Table Access Methods and blackholes by Michael Paquier](https://paquier.xyz/postgresql-2/postgres-12-table-am-blackhole/)
* [Blackhole AM Template by Michael Paquier](https://github.com/michaelpq/pg_plugins/blob/main/blackhole_am/blackhole_am.c)
