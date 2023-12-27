### MySQL メモリー使用量の監視

使用可能な MySQL メモリーインストゥルメントを表示するには、パフォーマンススキーマの setup_instruments テーブルをクエリーします。 次のクエリーは、すべてのコード領域に対して何百ものメモリーインストゥルメントを返します。
```sql
SELECT * FROM performance_schema.setup_instruments
       WHERE NAME LIKE '%memory%';
```

メモリーインストゥルメントを有効にするには、MySQL 構成ファイルに performance-schema-instrument ルールを追加します。 たとえば、すべてのメモリーインストゥルメントを有効にするには、このルールを構成ファイルに追加し、サーバーを再起動します
```
[mysqld]
performance_schema=ON
```
サーバーを再起動したあと、パフォーマンススキーマ setup_instruments テーブルの ENABLED カラムに、有効にしたメモリーインストゥルメントの YES が報告されます。
```sql
mysql> SELECT * FROM performance_schema.setup_instruments
    ->        WHERE NAME LIKE '%memory/innodb%';
+-------------------------------------------+---------+-------+-------------------+-------+------------+-----------------+
| NAME                                      | ENABLED | TIMED | PROPERTIES        | FLAGS | VOLATILITY | DOCUMENTATION   |
+-------------------------------------------+---------+-------+-------------------+-------+------------+-----------------+
| memory/innodb/adaptive hash index         | YES     | NULL  |                   |       |          0 | NULL            |
| memory/innodb/log and page archiver       | YES     | NULL  |                   |       |          0 | NULL            |
| memory/innodb/buf_buf_pool                | YES     | NULL  | global_statistics |       |          0 | NULL            |
| memory/innodb/buf_stat_per_index_t        | YES     | NULL  |                   |       |          0 | NULL            |
| memory/innodb/clone                       | YES     | NULL  |                   |       |          0 | NULL            |
| memory/innodb/dict_stats_bg_recalc_pool_t | YES     | NULL  |                   |       |          0 | NULL            |
・・・
```
次のクエリーは、InnoDB バッファプールのメモリーデータを返します。
```sql
mysql> SELECT * FROM performance_schema.memory_summary_global_by_event_name
    ->        WHERE EVENT_NAME LIKE 'memory/innodb/buf_buf_pool'\G
*************************** 1. row ***************************
                  EVENT_NAME: memory/innodb/buf_buf_pool
                 COUNT_ALLOC: 1
                  COUNT_FREE: 0
   SUM_NUMBER_OF_BYTES_ALLOC: 137170944
    SUM_NUMBER_OF_BYTES_FREE: 0
              LOW_COUNT_USED: 0
          CURRENT_COUNT_USED: 1
             HIGH_COUNT_USED: 1
    LOW_NUMBER_OF_BYTES_USED: 0
CURRENT_NUMBER_OF_BYTES_USED: 137170944
   HIGH_NUMBER_OF_BYTES_USED: 137170944
1 row in set (0.00 sec)
```
同じ基礎となるデータを sys スキーマ memory_global_by_current_bytes テーブルを使用してクエリーすることができます。
```sql
mysql> SELECT * FROM sys.memory_global_by_current_bytes
    ->        WHERE event_name LIKE 'memory/innodb/buf_buf_pool'\G
*************************** 1. row ***************************
       event_name: memory/innodb/buf_buf_pool
    current_count: 1
    current_alloc: 130.82 MiB
current_avg_alloc: 130.82 MiB
       high_count: 1
       high_alloc: 130.82 MiB
   high_avg_alloc: 130.82 MiB
1 row in set (0.01 sec)
```
この sys スキーマクエリーは、現在割り当てられているメモリー (current_alloc) をコード領域別に集計します:
```sql
mysql> SELECT SUBSTRING_INDEX(event_name,'/',2) AS
    ->        code_area, FORMAT_BYTES(SUM(current_alloc))
    ->        AS current_alloc
    ->        FROM sys.x$memory_global_by_current_bytes
    ->        GROUP BY SUBSTRING_INDEX(event_name,'/',2)
    ->        ORDER BY SUM(current_alloc) DESC;
+---------------------------+---------------+
| code_area                 | current_alloc |
+---------------------------+---------------+
| memory/performance_schema | 220.89 MiB    |
| memory/innodb             | 191.46 MiB    |
| memory/sql                | 9.79 MiB      |
| memory/mysys              | 8.58 MiB      |
| memory/temptable          | 1.00 MiB      |
| memory/mysqld_openssl     | 390.01 KiB    |
| memory/refcache           | 31.71 KiB     |
| memory/mysqlx             | 2.76 KiB      |
| memory/vio                |  896 bytes    |
| memory/myisam             |  720 bytes    |
| memory/csv                |  376 bytes    |
| memory/blackhole          |  376 bytes    |
+---------------------------+---------------+
12 rows in set (0.01 sec)
```