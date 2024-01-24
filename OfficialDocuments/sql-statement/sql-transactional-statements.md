### 13.3.6 LOCK TABLES および UNLOCK TABLES ステートメント
ロックが必要なセッションは、必要なすべてのロックを 1 つの LOCK TABLES ステートメントで取得する必要があります。
```sql
mysql> LOCK TABLES t1 READ;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT COUNT(*) FROM t1;
+----------+
| COUNT(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(*) FROM t2;
ERROR 1100 (HY000): Table 't2' was not locked with LOCK TABLES
```
ロックされたテーブルを、同じ名前を使用して 1 つのクエリーで複数回参照することはできません。 代わりにエイリアスを使用し、そのテーブルと各エイリアスのための個別のロックを取得します
```sql
mysql> INSERT INTO t SELECT * FROM t;
ERROR 1100 (HY000): Table 't' was not locked with LOCK TABLES
mysql> INSERT INTO t SELECT * FROM t AS t1;
Query OK, 1 row affected (0.01 sec)
Records: 1  Duplicates: 0  Warnings: 0
```
ステートメントがエイリアスを使用してテーブルを参照する場合は、その同じエイリアスを使用してテーブルをロックする必要があります。 エイリアスを指定しないでテーブルをロックすることはできません。
```sql
mysql> LOCK TABLE t READ;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM t AS myalias;
ERROR 1100 (HY000): Table 'myalias' was not locked with LOCK TABLES
```
逆に、エイリアスを使用してテーブルをロックする場合は、ステートメント内でそのエイリアスを使用してテーブルを参照する必要があります
```Sql
mysql> LOCK TABLE t AS myalias READ;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM t;
ERROR 1100 (HY000): Table 't' was not locked with LOCK TABLES
mysql> SELECT * FROM t AS myalias;
+------+------+------+
| i    | d    | f    |
+------+------+------+
|    2 |    2 |    1 |
|    2 |    2 |    1 |
+------+------+------+
2 rows in set (0.00 sec)
```
行時にトランザクション特性のグローバル値およびセッション値を確認できます
```sql
+--------------------------------+--------------------------------+
| @@GLOBAL.transaction_isolation | @@GLOBAL.transaction_read_only |
+--------------------------------+--------------------------------+
| REPEATABLE-READ                |                              0 |
+--------------------------------+--------------------------------+
1 row in set (0.00 sec)

mysql> SELECT @@SESSION.transaction_isolation, @@SESSION.transaction_read_only;
+---------------------------------+---------------------------------+
| @@SESSION.transaction_isolation | @@SESSION.transaction_read_only |
+---------------------------------+---------------------------------+
| REPEATABLE-READ                 |                               0 |
+---------------------------------+---------------------------------+
1 row in set (0.00 sec)
```
### 13.3.8.2 XA トランザクションの状態
XA トランザクションは、次の各状態を経由して処理されます。
```sql
mysql> XA START 'xatest';
Query OK, 0 rows affected (0.00 sec)

mysql>  INSERT INTO t (i) VALUES(10);
Query OK, 1 row affected (0.00 sec)

mysql> XA END 'xatest';
Query OK, 0 rows affected (0.00 sec)

mysql> XA PREPARE 'xatest';
Query OK, 0 rows affected (0.00 sec)

mysql> XA COMMIT 'xatest';
Query OK, 0 rows affected (0.00 sec)
```

### 13.4.2 レプリケーションサーバーを制御するための SQL ステートメント

### 13.4.1.2 RESET MASTER ステートメント
バイナリロギングが有効になっている (log_bin が ON) サーバーの場合、RESET MASTER は既存のバイナリログファイルをすべて削除し、バイナリログインデックスファイルをリセットして、バイナリロギングが開始される前の状態にサーバーをリセットします
```sql
mysql> RESET MASTER TO 1234;
Query OK, 0 rows affected, 1 warning (0.08 sec)

mysql>
mysql> SHOW BINARY LOGS;
+--------------------------+-----------+-----------+
| Log_name                 | File_size | Encrypted |
+--------------------------+-----------+-----------+
| DYNABOOK-B552-bin.001234 |       157 | No        |
+--------------------------+-----------+-----------+
1 row in set (0.00 sec)
```
### 13.4.2.2 CHANGE REPLICATION FILTER ステートメント
```Sql
mysql> CHANGE REPLICATION FILTER
    ->     REPLICATE_DO_DB = (d1), REPLICATE_IGNORE_DB = (d2);
Query OK, 0 rows affected (0.00 sec)
```
同じフィルタリングルールが複数回指定されている場合、実際には last のそのようなルールのみが使用されます
```
mysql> CHANGE REPLICATION FILTER
    ->     REPLICATE_DO_DB = (db1, db2), REPLICATE_DO_DB = (db3, db4);
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> CHANGE REPLICATION FILTER
    ->     REPLICATE_DO_DB = (db3, db4);
Query OK, 0 rows affected (0.00 sec)
```
特殊文字を含まないテーブルおよびデータベースの名前は引用符で囲む必要はありません。 REPLICATION_WILD_TABLE および REPLICATION_WILD_IGNORE_TABLE で使用される値は文字列式で、(特殊な) ワイルドカード文字が含まれている可能性があるため、引用符で囲む必要があります。
```sql
mysql> CHANGE REPLICATION FILTER
    ->     REPLICATE_WILD_DO_TABLE = ('db1.old%');
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> CHANGE REPLICATION FILTER
    ->     REPLICATE_WILD_IGNORE_TABLE = ('db1.new%', 'db2.new%');
Query OK, 0 rows affected (0.00 sec)
```
REPLICATE_REWRITE_DB で使用される値は、データベース名のペアを表します。
```sql
mysql> CHANGE REPLICATION FILTER REPLICATE_REWRITE_DB = ((db1, db2));
Query OK, 0 rows affected (0.00 sec)
```
特定のタイプのすべてのフィルタの設定を解除する場合は、次の例に示すように、フィルタ値を明示的に空のリストに設定します。
```sql
mysql> CHANGE REPLICATION FILTER
    ->     REPLICATE_DO_DB = (), REPLICATE_IGNORE_DB = ();
Query OK, 0 rows affected (0.00 sec)
```
### 13.4.2.3 CHANGE REPLICATION SOURCE TO ステートメント
次の例では、レプリカが使用するソースサーバーを変更し、レプリカが読取りを開始するソースバイナリログ座標を確立します
```sql
mysql> CHANGE REPLICATION SOURCE TO
    ->   SOURCE_HOST='source2.example.com',
    ->   SOURCE_USER='replication',
    ->   SOURCE_PASSWORD='password',
    ->   SOURCE_PORT=3306,
    ->   SOURCE_LOG_FILE='source2-bin.001',
    ->   SOURCE_LOG_POS=4,
    ->   SOURCE_CONNECT_RETRY=10;
Query OK, 0 rows affected, 2 warnings (0.14 sec)
```
無視されるサーバーのリストをクリアするには、このオプションを空のリストとともに使用する必要があります
```Sql
mysql> CHANGE REPLICATION SOURCE TO IGNORE_SERVER_IDS = ();
Query OK, 0 rows affected (0.03 sec)

```
### 13.4.2.7 START REPLICA | SLAVE ステートメント
START REPLICA | SLAVE は、レプリケーションスレッドの一方または両方を起動します。 MySQL 8.0.22 から、START SLAVE のかわりに START REPLICA を使用します。

失敗したマルチスレッドレプリカをシングルスレッドモードに変更するには、次に示す順序で次の一連のステートメントを発行します
```sql
mysql> START SLAVE UNTIL SQL_AFTER_MTS_GAPS;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SET @@GLOBAL.slave_parallel_workers = 0;
Query OK, 0 rows affected, 2 warnings (0.00 sec)

mysql> START SLAVE SQL_THREAD;
Query OK, 0 rows affected, 2 warnings (0.00 sec)
```

* asynchronous_connection_failover_add_source()

レプリケーションソースサーバーの構成情報をレプリケーションチャネルのソースリストに追加します。
```sql
mysql> SELECT asynchronous_connection_failover_add_source('channel2', '127.0.0.1', 3310, '', 80);
+------------------------------------------------------------------------------------+
| asynchronous_connection_failover_add_source('channel2', '127.0.0.1', 3310, '', 80) |
+------------------------------------------------------------------------------------+
| The UDF asynchronous_connection_failover_add_source() executed successfully.       |
+------------------------------------------------------------------------------------+
1 row in set (0.01 sec)
```
* asynchronous_connection_failover_add_managed()
管理対象グループ (Group Replication グループメンバー) の一部であるレプリケーションソースサーバーの構成情報を、レプリケーションチャネルのソースリストに追加します。
```sql
mysql> SELECT asynchronous_connection_failover_add_managed('channel2', 'GroupReplication', 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa', '127.0.0.1', 3310, '', 80, 60);
+-----------------------------------------------------------------------------------------------------------------------------------------------------+
| asynchronous_connection_failover_add_managed('channel2', 'GroupReplication', 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa', '127.0.0.1', 3310, '', 80, 60) |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+
| The UDF asynchronous_connection_failover_add_managed() executed successfully.
                              |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.02 sec)
```
* asynchronous_connection_failover_delete_managed()

レプリケーションチャネルのソースリストから管理対象グループ全体を削除します
```sql
mysql> SELECT asynchronous_connection_failover_delete_managed('channel2', 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa');
+-----------------------------------------------------------------------------------------------------+
| asynchronous_connection_failover_delete_managed('channel2', 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa') |
+-----------------------------------------------------------------------------------------------------+
| The UDF asynchronous_connection_failover_delete_managed() executed successfully.                    |
+-----------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)
```
