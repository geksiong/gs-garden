---
title: MySQL Notes & Tips
date: 2019-01-07
updated: 2021-08-12

tags:
  - mysql
  - tech

---
# MySQL Notes & Tips

## Unicode

  - MySQL's default collations may change in future, and there are some gotchas with older defaults, so always specify what you want at the start to avoid conversion pain later
    - MySQL 5.7 introduced `utf8mb4_unicode_520_ci`
    - from MySQL 8.0.1, default is `utf8mb4_0900_ai_ci`
  - collations may be applied to database and tables. Double check if you use both.
  - on older versions, ensure you are using `utf8mb4`, not `utf8`
    - https://medium.com/@adamhooper/in-mysql-never-use-utf8-use-utf8mb4-11761243e434
    - https://mathiasbynens.be/notes/mysql-utf8mb4)
    - use `utf8mb4_unicode_ci` (determines sort order, slightly slower than `general_ci` but much more correct)
  - `utf8` is a variable length encoding - `ut8mb4` can use max 4 bytes per char
  - when connecting, specify `useUnicode=yes&characterEncoding=UTF-8` in the connection string

## Upgrading MySQL versions

- remember to do `REPAIR TABLE` and `OPTIMIZE TABLE` after upgrading (unnecessary if the table is altered)

## How to restore deleted records

> This assumes you have a disk backup containing the records you accidentally deleted!

- Create a new server mounting the old disk
- Export the old table
```sh
mysqldump -u <user> -p <database> <table> --where="<condition>" --no-create-info > records_to_restore.sql
```

- Check the sql contains the inserts you want
  - in particular, that it does NOT contain DROP/CREATE tables statements. That's what the `no-create-info` is for.
- Copy the file to the original server
- Import the records into the original database
```sh
mysql -u <user> -p <database> < records_to_restore.sql
```


## Tuning MySQL in AWS RDS

### RDS Low Freeable Memory

When using Amazon RDS, 80% to 90% of the available memory on an instance is allocated with the default parameters. This allocation is optimal for performance, but if you set parameters that use more memory, then modify other parameters to use less memory to compensate. After you identify which components are using memory, you can look for bottlenecks at the instance and database level. Then, you can configure your sessions for optimal performance. 

### Defaults

* innodb_buffer_pool_size = 75% of instance memory size (DBInstanceClassMemory * 3/4)
  * check that BufferCacheHitRatio is not too low
  * can consider reducing to 5/8 or 1/2
  * if BufferCacheHitRatio is low, may need more instance RAM

thread_stack
net_buffer_length
read_buffer_size

* `sort_buffer_size` & `join_buffer_size`
  * You can also see increases in memory usage if multiple buffers of the same type, such as join_buffer_size or sort_buffer_size, are allocated when the session is performing JOIN or SORT operations. 
  * For example, MySQL allocates one JOIN buffer to perform JOIN between two tables. If a query involves multi-table JOINs and all the queries require a JOIN buffer, then MySQL allocates one JOIN buffer fewer than the total number of tables. 
  * Configuring your session variables with a value that is too high can cause issues if the queries aren't optimized. 

* `max_heap_table_size` & `tmp_table_size`
  * MySQL creates internal temporary tables to perform some operations. These tables are created initially as memory-based tables. 
  * When the size of these tables reaches the value specified by tmp_table_size or max_heap_table_size (whichever has the lowest value), then the table is converted to a disk-based table.
  * When multiple sessions create internal temporary tables, you might see increases in memory utilization. To reduce memory utilization, avoid using temporary tables in your queries.
  
* If you perform bulk inserts to MYISAM tables, then `bulk_insert_buffer_size` bytes of memory are used.

### Troubleshooting

To check for memory pressure, monitor the CloudWatch metrics for SwapUsage in addition to FreeableMemory. If you see that a large amount of swap is used and you have low FreeableMemory, your DB instance might be under memory pressure, which can degrade performance.

Run `SHOW FULL PROCESSLIST;` to see all the open connections. Then, run `SHOW ENGINE INNODB STATUS;` to see InnoDB information, such as long-running transactions, memory utilization statistics, and locks. Review the BUFFER POOL AND MEMORY section to see if there are any free pages.

It's a best practice to keep at least 5% of the instance memory free, so set a CloudWatch alarm that notifies you when you've reached at 95% utilization.

---
### Parameters

#### innodb_flush_method

* default is NULL
  * Unix: fsync
  * Windows: async_unbuffered
  
#### innodb_buffer_pool_size

* one of the most important settings, often set to 80% or more of memory
* Run `innodb status \G`
* The performance of InnoDB depends on whether data is present in the cache. Whenever there is a cache miss, it causes a random I/O request to disk, resulting in very slow operation.


#### key_buffer_size

* `key_buffer_size` is the size of the buffer used for index blocks. The key buffer is also known as the key cache.
* Can be changed dynamically.
* The value of this variable indicates the amount of memory requested. Internally, the server allocates as much memory as possible up to this amount, but the actual allocation might be less.
* Default = 8,388,608 (about 8Mb)
* Max = 4GB-1 on 32-bit systems, OS_PER_PROCESS_LIMIT on 64-bit systems
* Rule of thumb: < 0.25 of system memory for MyISAM engine, up to 0.70 for InnoDB
* Check the performance of this buffer using `SHOW STATUS`
  * `Key_read_requests`, `Key_reads`, `Key_write_requests`, `Key_writes`
  * `Key_reads / key_read_requests` should be < 0.01
  * `Key_writes / `Key_write_requests` is usually near 1.0 if using mostly updates & deletes, but can be much smaller if tend to do updates many rows at the same time, or using DELAY_KEY_WRITE table option
  * Fraction of key_buffer in use = `1 - ((Key_blocks_unused * key_cache_block_size) / key_buffer_size)`
    * Note that some space if used internally for administrative structures

#### max_allowed_packet

* Maximum size of a sendable packet, i.e. a single SQL state, a single row, or a log sent from master to slave
* If value too small, will see an error in error log

#### thread_stack

* default value is typically sufficient
* increase this only when you see error in error log

#### thread_cache_size

* number of unused threads to store in a cache until they need to be used for a connection
* generally little effect on performance unless you have hundreds of connections per minute

#### max_connections

* maximum amount of concurrent connections.
* It is best to consider the maximum amount of connections you have had in the past before setting this number, so you’ll have a buffer between that upper number and the max_connections value.

#### table_cache

* This value should be kept higher than your open_tables value. To determine this value use:

`SHOW STATUS LIKE 'open%';`

---
**Sources**
- https://i.stack.imgur.com/X7UrX.jpg
- https://www.liquidweb.com/kb/mysql-performance-innodb-buffers-directives/
