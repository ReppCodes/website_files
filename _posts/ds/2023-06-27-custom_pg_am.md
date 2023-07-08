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
* Can be added to a cluster at runtime using `CREATE EXTENSION` 

### Cons
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
* index_fetch_end
* index_fetch_tuple
* tuple_insert 
* tuple_insert_speculative
* tuple_complete_speculative
* multi_insert
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

## Part 1: A Black Hole
Michael Paqiuer has graciously provided a template for implementing this API that we will be using for our experiments here. It is called `blackhole_am`, and it's basically `/dev/null` for PostgreSQL.  Our first step here is going to be just getting it installed in a local build of PostgreSQL and demosntrating its use.

### Go get PostgreSQL
We'll be installing PostgreSQL from source, to give us the flexibility to tweak things or attach a debugger as needed.  A [GitHub mirror](https://github.com/postgres/postgres) is available, so we'll be using that.
```sh
# these are the dependencies I had to install on a stock WSL Ubuntu.  You may already have them.
sudo apt install libicu-dev libreadline-dev libipc-run-perl flex bison

mkdir workspace
cd workspace
git clone git@github.com:postgres/postgres.git
cd postgres
git fetch
git switch REL_16_STABLE
export LD_LIBRARY_PATH=/usr/local/pgsql/
./configure -enable-cassert --enable-debug --enable-depend --enable-tap-tests CFLAGS='-ggdb3 -O0 -fno-omit-frame-pointer'
bear -- make -j8 -s 
make install -s 
export PATH=/usr/local/pgsql/bin:$PATH 
# this is for removing data from prior test clusters
rm -rf /usr/local/pgsql/data 

initdb -D /usr/local/pgsql/data 
pg_ctl -D /usr/local/pgsql/data -l /tmp/logfile start
```

You should now have a functioning cluster that you can connect to with `psql`.

### Go get the template and install it
We need to install the template for `blackhole_am` into our running postgres cluster, to give us access to the TAM it represents.
```sh
cd workspace
git clone git@github.com:michaelpq/pg_plugins.git
cp -r pg_plugins/blackhole_am postgres/contrib
cd cd postgres/contrib
make
make install

# create a demo db and load our new access method onto it via extension
createdb am_demo
psql -d am_demo -c 'CREATE EXTENSION blackhole_am;'
```

### Let's test drive a black hole
Hop into your database with `psql -d am_demo` and let's try this out.
```sql
-- heap table, with expected behavior
CREATE TABLE showme (i int) USING heap;
-- CREATE TABLE
INSERT INTO showme SELECT * FROM generate_series(1,10);
-- INSERT 0 10
SELECT * FROM showme; 
--   i
-- -----
--    1
--    2
--    3
--    4
--    5
--    6
--    7
--    8
--    9
--   10
-- (10 rows)

-- blackhole table, nothing comes back from here
CREATE TABLE nada (i int) USING blackhole_am;
-- CREATE TABLE
INSERT INTO nada SELECT * FROM generate_series(1,10);
-- INSERT 0 10
SELECT * FROM nada;
--  i
-- ---
-- (0 rows)
```

Ok, so we have an installed access method that neatly destroys everything we throw at it.  That's cool, and a great starting point.  Thanks Mr. Paquier!

## Part 2: Roll your own
Now that we have a skeleton in place, let's try to edit the provided template to give the most basic possible functionality. We'll start with CREATE, INSERT, and SELECT. This will allow us to persist our own data.

All code discussed in this article is available [here](https://github.com/ReppCodes/postgres_am_demo).

## Resources Reference
In building this project out, I had to read and learn quite a lot.  Here is a list of the resources
I found helpful, both to give credit and in case they're helpful for you in working with this
material also.
* [Fujitsu TAM Blog](https://www.postgresql.fastware.com/blog/postgresql-table-access-methods)
* [Table Access Methods and blackholes by Michael Paquier](https://paquier.xyz/postgresql-2/postgres-12-table-am-blackhole/)
* [Blackhole AM Template by Michael Paquier](https://github.com/michaelpq/pg_plugins/blob/main/blackhole_am/blackhole_am.c)
* [Andres Freund presentation on pluggable AM](https://anarazel.de/talks/2019-05-30-pgcon-pluggable-table-storage/pluggable.pdf)
