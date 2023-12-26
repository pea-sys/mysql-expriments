### 8.11.1 内部ロック方法
Table_locks_immediate および Table_locks_waited ステータス変数をチェックすることでシステム上のテーブルロック競合を分析できます。
```sql
mysql> SHOW STATUS LIKE 'Table%';
+----------------------------+-------+
| Variable_name              | Value |
+----------------------------+-------+
| Table_locks_immediate      | 18    |
| Table_locks_waited         | 0     |
| Table_open_cache_hits      | 524   |
| Table_open_cache_misses    | 32    |
| Table_open_cache_overflows | 0     |
+----------------------------+-------+
5 rows in set (0.00 sec)
```