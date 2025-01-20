# Monitor-Postgresql:
#### Ensure optimal performance of Postgres database with 24x7 Postgres monitoring
1. Monitor active connections: The number of active connections impacts server performance. Keep track of the current connection count and analyze user sessions to terminate idle ones that unnecessarily slow down the server.
---
3. Analyze response times: High response times in the database indicate a decline in performance. If response times are increasing, analyze long-running queries to identify potential issues.
---
5. Track disk usage: Monitor PostgreSQL disk usage statistics to analyze server efficiency. Rapid increases in disk usage may suggest frequent access to storage, slowing down the network. You may have to investigate why data retrieval isn't happening from the cache.
---
7. Top 10 queries by CPU: Monitoring the top CPU-consuming queries helps identify which queries are taxing your server resources the most. Analyze these queries so you can optimize them to reduce CPU usage and enhance overall performance.
---
9. Long-running queries: Tracking long-running queries is essential to prevent them from degrading database performance. Identify and optimize these queries to improve response times and resource utilization significantly.
---
10. Top 50 table row details: Obtain detailed insights into the top 50 queries to get a comprehensive overview of your database activity. This can help in pinpointing non-essential queries running in the background, allowing you to isolate and address them to prevent performance degradation.
---
#### Optimize database performance by tracking buffer statistics Gain insights into database performance by tracking critical buffer statistics.
1. Cache Hit Ratio: Measure the efficiency of your database cache by calculating the ratio of cache hits to lookups. A higher percentage indicates better performance.
---
3. Block Reads/Minute: Track the number of times data is read from disk. High values might indicate cache inefficiency or excessive data access.
---
5. Buffer Reads/Minute: Monitor the overall cache utilization by tracking the number of cache hits per minute.
---
#### Performance metrics that you can track with PostgreSQL monitoring:
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
### Lets look in to some of the most important configuration parameters in tuning the PostgreSQL database server:

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

