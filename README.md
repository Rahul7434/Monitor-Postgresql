# Monitor-Postgresql

### Performance metrics that you can track with PostgreSQL monitoring:
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
  
