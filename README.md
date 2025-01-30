# Monitor-Postgresql:
---
## Performance metrics that you can track with PostgreSQL monitoring:
  1. Connection statistics
      ```
      : Keep an eye on the number of active connections in the database. Become mindful of total users in the database.
      ```
  2. Lock statistics
      ```
      A database involves execution of complex transactions that fetch, modify/alter data. PostgreSQL utilizes locks to perform serial changes to important sections of the database. Applications Manager's PostgreSQL performance
      ```
  3. Buffer statistics
     ```
     helps track buffer hits and blocks reads/min and capture cache-hit ratio value. Analyze cache Hit Ratio values and take necessary steps if the value becomes low as it degrades PostgreSQL performance.
     ```
  4. Disk usage details
     ```
      PostgreSQL performance monitor, plan capacity of PostgreSQL database better by keeping tabs on the disk and index usage. If you see a surge in index usage, you may need to analyze and get rid of unused indexes as they are said to slow down data modification and the backup processes.
     ```
  5. Index scan details
      ```
       Index scans fetch all the required values entirely from the index without visiting the table at all. This helps accelerate the execution of a query.
      ```
  6. Query statistics
      ```
       gathering information about the number of rows inserted, updated, and deleted every minute. This helps you gain insight into the type of queries your database serves.
      ```
  7. Transaction details
      ```
      keep an eye on the number of commits and rollbacks taking place in your database. Higher values of commits usually indicate increased efficiency of server.
      ```
  8. Table scan details
      ```
      ```
  9.Session details and database details: 
      ```
      top 10 queries by CPU utilization and shows a list of long-running queries. This information helps you analyze which queries are slowing the database.
      ```
## Lets look in to some of the most important configuration parameters in tuning the PostgreSQL database server:

- shared_buffers :- default size:- 128 MB | Recommended	25% of the RAM.
```
Shared buffers are a specific section of memory designated to cache frequently accessed index blocks and data. By utilizing shared buffers, the database system can enhance its performance by minimizing the need to read data from disk, which is typically slow.
Note: Increasing this configuration requires an increase in max_wal_size in order to process the larger amount of data when a checkpoint occurs.
```
- work_mem :- Default	4 MB |
```
Work memory in PostgreSQL is the memory allocated for executing individual operations, like sorting or hashing. This memory holds temporary data structures and intermediate results generated during the operation. The amount of work memory required by an operation depends on the complexity and size of input data, as well as available system memory. Inadequate work memory can cause operations to use disk-based temporary files, resulting in slower performance.
Note: Postgres 13 presents a fresh configuration named hash_mem_multiplier, which serves as an extra option to optimize the allocation of memory.

**Recommended**	 :- A reasonable value can be chosen based on the amount of data that is queried and brought into memory. You might also have to consider the max_connections parameter before setting a value, as several connections may be running in parallel. (Total RAM used = work_mem * concurrent connections). Finding the optimal value for work_mem can be challenging, but a reasonable default value that could work universally is around 64 MB.
```
- maintenance_work_mem:- 64MB |
```
The maintenance_work_mem parameter controls the amount of memory used for maintenance operations such as VACUUM, index creation, and adding foreign keys. Since only one of these operations can be executed at a time in one database session it is safe to set this value larger than work_mem. Larger settings might improve performance for vacuuming and for restoring database dumps.

**Recommended** One thing to be considered here is that when autovacuum runs, memory consumed = autovacuum_max_workers x maintenance_work_mem. The value of this can be 512 MB.
```
- effective_cache_size | Default size 4 GB | Recommended	50% of the memory
```
The effective cache size parameter specifies the amount of memory that the database server can use for caching data and index blocks in memory. This parameter represents the estimated amount of memory used by the operating system file system cache and any other processes that may be running on the same system.

The effective cache size parameter is utilized by the query planner to calculate the cost of various query execution plans. This setting allows the planner to make an informed estimate of the amount of data that is expected to be cached in memory, which is then used to make optimal decisions regarding query execution plan selection.
```
- max_worker_processes | Default	8 |  Recommended	It is recommended to use 50% of the processors so that the system can run normally.
```
The max_worker_processes parameter in PostgreSQL sets a limit on the number of concurrent background worker processes that can be running simultaneously. These worker processes perform various functions such as parallel query execution, handling connections, and background tasks.
```

- max_parallel_workers | Default	2 | Recommended	It is recommended to use 50% of the processors so that the system can run optimally.
```
The max_parallel_workers parameter in PostgreSQL limits the total count of parallel worker processes that can be active simultaneously. These worker processes are employed to enhance system performance during parallel query execution and other operations. Careful configuration of this parameter is crucial because it can significantly impact the resource utilization and overall performance of the system.
```
- max_parallel_workers_per_gather | default 2 | Recommended	Parallel workers are taken from the pool of processors established by the max_worker_processes and limited by max_parallel_workers.
```
Description	The max_parallel_workers_per_gather parameter in PostgreSQL manages the maximum count of parallel worker processes that can be engaged in a single operation. This parameter sets an upper boundary on the number of workers that can execute parallel table scans and query plans. It can significantly impact the performance of parallel queries, and requires careful configuration that considers the system resources and workload characteristics.
```
- max_wal_size |  Default	1 GB |  Recommended	Increasing this parameter can increase the amount of time needed for crash recovery

```
The max_wal_size parameter in PostgreSQL sets an upper limit on the size of the write-ahead log (WAL) that the database system can use. The WAL is a critical part of PostgreSQL's transaction management mechanism, responsible for maintaining data consistency and durability even in the event of system crashes or failures.

Configuring this parameter allows you to set the maximum size for WAL grow during automatic checkpoints. WAL size can exceed max_wal_size under special circumstances, such as heavy load, a failing archive_command, or a high wal_keep_size setting.
```

- default_statistics_target | Default	Sample size = 300 * default_statistics_target | Recommended	Larger values increase the time needed to work on ANALYZE, but might improve the quality of the planner's estimates.

```
The default_statistics_target configuration parameter specifies the level of detail used by the query optimizer when collecting statistics about the database. the number of rows that are examined by the optimizer to determine the most efficient query execution plan is governed by this parameter . It determines the sample size used by the optimizer when analyzing a table for this purpose.
```
---
## Monitoring I/O Load
- pg_stat_activity: This view shows information about the current activity of all backend processes. You can monitor the I/O load by checking the state of queries and their durations.

```
SELECT
  pid,
  datname,
  usename,
  state,
  query,
  wait_event_type,
  wait_event
FROM
  pg_stat_activity where state='active';

#Using pg_stat_io (postgresql16 and above)
select * from pg_stat_io where backend_type ='user_backend';
```
- pg_stat_database: This view provides aggregate statistics per database. You can use it to monitor I/O statistics such as blocks read from disk and blocks hit in the cache.
```
SELECT
  datname,
  blks_read,
  blks_hit,
  (blks_hit * 100.0 / (blks_read + blks_hit)) AS hit_ratio
FROM
  pg_stat_database;

```


- pg_stat_statements: This extension tracks execution statistics of all SQL statements executed by the server. You can identify queries with high buffer reads using this extension.
```
SELECT
  query,
  calls,
  total_time,
  rows,
  shared_blks_hit,
  shared_blks_read,
  (shared_blks_hit + shared_blks_read) AS total_blks,
  (shared_blks_read * 100.0 / (shared_blks_hit + shared_blks_read)) AS read_ratio
FROM
  pg_stat_statements
ORDER BY
  shared_blks_read DESC
LIMIT 10;

```
- pg_stat_bgwriter how to understand allits columns dataas human
The pg_stat_bgwriter view in PostgreSQL provides statistics about the background writer process, which is responsible for writing dirty buffers to disk. Understanding these columns can help you monitor and optimize the performance of your PostgreSQL instance. Here's a breakdown of the columns.
```
select * from pg_stat_bgwriter;
```
---

### Top time consuming queries
```
select userid::regrole, dbid, query ,calls, total_time/1000 as total_time_seconds ,min_time/1000 as min_time_seconds,max_time/1000 as max_time_seconds,mean_time/1000 as mean_time_seconds
    from pg_stat_statements
    order by mean_time desc
    limit 10;
```
###  sql queries having high i/o activity
```
postgres# select userid::regrole, dbid, query,queryid,mean_time/1000 as mean_time_seconds 
    from pg_stat_statements
    order by (blk_read_time+blk_write_time) desc
    limit 10;
```
### Queries with high memory usage:
```
postgres=# select userid::regrole, dbid, queryid,query  from pg_stat_statements 
            order by (shared_blks_hit+shared_blks_dirtied) desc limit 10;
```
### QUERY TO FIND BLOCKING SESSION DETAILS
```
dbaclass#select pid as blocked_pid, usename, pg_blocking_pids(pid) as "blocked_by(pid)", query as blocked_query from pg_stat_activity where cardinality(pg_blocking_pids(pid)) > 0;

output:

 blocked_pid | usename  | blocked_by(pid) |        blocked_query
-------------+----------+-------------+------------------------------
        4206 | postgres | {3673}      | alter table test drop query;
```
### Archive Process Status
```
postgres# select * from pg_stat_archiver;
-[ RECORD 1 ]------+---------------------------------
archived_count     | 0
last_archived_wal  |
last_archived_time |
failed_count.      | 0
last_failed_wal.   |
last_failed_time   |
stats_reset        | 26-SEP-20 11:13:08.540237 +03:00

```

### Get Config values from psql prompt.
```

postgres=# select * from pg_settings;

\x
postgres=# select * from pg_settings where name='port';

2. Alternatively you can check postgresql.conf file

postgres=# show config_file;
config_file
---------------------------------
/pgdata/data/postgresql.conf
(1 row)

cat /pgdata/data/postgresql.conf

```
### Last pg config reload time
```
postgres=# select pg_conf_load_time() ;
pg_conf_load_time
----------------------------------
2020-07-06 13:20:18.048689+05:30
(1 row)
```

### Wal Switch manually
```
postgres=# select pg_switch_wal();
pg_switch_wal
---------------
0/1D392648
(1 row)
```
### View existing connection limit setting:( datconnlimit )
```
postgres=# select datname,datallowconn,datconnlimit from pg_database where datname='test_dev';
-[ RECORD 1 ]--+------------
datname        | test_dev
datallowconn  | t.
datconnlimit  | -1.       -- >Means unlimited connections allowed

-- To set a specific limit for connection

test_dev=# alter database test_dev connection limit 100;
ALTER DATABASE

-- To restrict all the connections to db

test_dev=# alter database test_dev connection limit 0;
ALTER DATABASE
```

### Find wal file details and its size
```
-- List down all the wal files present in pg_wal

postgres=# select * from pg_ls_waldir();
name                     | size     | modification
------------------------------------------+----------+---------------------------
0000000100000079000000D5 | 16777216 | 22-APR-22 20:51:26 +03:00
0000000100000079000000D8 | 16777216 | 22-APR-22 20:39:33 +03:00
0000000100000079000000D6 | 16777216 | 22-APR-22 20:07:40 +03:00
0000000100000079000000D9 | 16777216 | 22-APR-22 20:47:21 +03:00
0000000100000079000000D7 | 16777216 | 22-APR-22 20:21:45 +03:00
00000001000000790000005C.00005BC8.backup | 323 | 21-APR-22 10:14:40 +03:00
(6 rows)

-- Find total size of wal:

postgres=# select sum(size) from pg_ls_waldir();
sum
----------
83886403
(1 row)

-- Find current wal file lsn:

postgres=# select pg_current_wal_insert_lsn(),pg_current_wal_lsn();
```
### Find list of installed extension:

```

psql# \dx

(or)

psql#\dx+

(or)

psql#SELECT * FROM pg_extension;

-- For finding available extension in server:

psql# SELECT * FROM pg_available_extensions;
```





