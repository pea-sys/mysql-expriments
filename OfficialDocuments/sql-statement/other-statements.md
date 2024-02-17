### 13.7.8 その他の管理ステートメント

### 13.7.8.7 RESET PERSIST ステートメント
RESET PERSIST は、永続化されたグローバルシステム変数設定をデータディレクトリの mysqld-auto.cnf オプションファイルから削除します。
```sql
mysql> RESET PERSIST max_connections;
Query OK, 0 rows affected (0.00 sec)

mysql> RESET PERSIST IF EXISTS max_connections;
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

### 13.7.8.8 RESTART ステートメント
このステートメントは、MySQL サーバーを停止して再起動します
```sql
mysql> RESTART;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT 1;
ERROR 2013 (HY000): Lost connection to MySQL server during query
No connection. Trying to reconnect...
Connection id:    14
Current database: *** NONE ***

+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (2.10 sec)
```
### 13.7.8.9 SHUTDOWN ステートメント
このステートメントは、MySQL サーバーを停止します
```Sql
mysql> SHUTDOWN;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT 1;
ERROR 2013 (HY000): Lost connection to MySQL server during query
No connection. Trying to reconnect...
ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost:3306' (10061)
ERROR:
Can't connect to the server
```

### 13.8.2 EXPLAIN ステートメント
```sql
mysql> DESCRIBE City;
+-------------+----------+------+-----+---------+----------------+
| Field       | Type     | Null | Key | Default | Extra          |
+-------------+----------+------+-----+---------+----------------+
| ID          | int      | NO   | PRI | NULL    | auto_increment |
| Name        | char(35) | NO   |     |         |                |
| CountryCode | char(3)  | NO   | MUL |         |                |
| District    | char(20) | NO   |     |         |                |
| Population  | int      | NO   |     | 0       |                |
+-------------+----------+------+-----+---------+----------------+
5 rows in set (0.03 sec)
```

```sql
mysql> CREATE TABLE t1 (
    ->     c1 INTEGER DEFAULT NULL,
    ->     c2 INTEGER DEFAULT NULL
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql>
mysql> CREATE TABLE t2 (
    ->     c1 INTEGER DEFAULT NULL,
    ->     c2 INTEGER DEFAULT NULL
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> CREATE TABLE t3 (
    ->     pk INTEGER NOT NULL PRIMARY KEY,
    ->     i INTEGER DEFAULT NULL
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> EXPLAIN ANALYZE SELECT * FROM t1 JOIN t2 ON (t1.c1 = t2.c2)\G
*************************** 1. row ***************************
EXPLAIN: -> Inner hash join (t2.c2 = t1.c1)  (cost=0.7 rows=1) (actual time=0.37..0.37 rows=0 loops=1)
    -> Table scan on t2  (cost=0.35 rows=1) (never executed)
    -> Hash
        -> Table scan on t1  (cost=0.35 rows=1) (actual time=0.023..0.023 rows=0 loops=1)

1 row in set (0.01 sec)

mysql> EXPLAIN ANALYZE SELECT * FROM t3 WHERE i > 8\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: (t3.i > 8)  (cost=0.35 rows=1) (actual time=0.0127..0.0127 rows=0 loops=1)
    -> Table scan on t3  (cost=0.35 rows=1) (actual time=0.012..0.012 rows=0 loops=1)

1 row in set (0.00 sec)

mysql> EXPLAIN ANALYZE SELECT * FROM t3 WHERE pk > 17\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: (t3.pk > 17)  (cost=0.46 rows=1) (actual time=0.0176..0.0176 rows=0 loops=1)
    -> Index range scan on t3 using PRIMARY over (17 < pk)  (cost=0.46 rows=1) (actual time=0.0167..0.0167 rows=0 loops=1)

1 row in set (0.00 sec)
```

### 13.8.3 HELP ステートメント
```sql
mysql> HELP 'status'
Many help items for your request exist.
To make a more specific request, please type 'help <item>',
where <item> is one of the following
topics:
   FLUSH
   SHOW
   SHOW ENGINE
   SHOW FUNCTION STATUS
   SHOW MASTER STATUS
   SHOW PROCEDURE STATUS
   SHOW REPLICA STATUS
   SHOW SLAVE STATUS
   SHOW STATUS
   SHOW TABLE STATUS

mysql> HELP 'contents'
You asked for help about help category: "Contents"
For more information, type 'help <item>', where <item> is one of the following
categories:
   Account Management
   Administration
   Components
   Compound Statements
   Contents
   Data Definition
   Data Manipulation
   Data Types
   Functions
   Geographic Features
   Help Metadata
   Language Structure
   Loadable Functions
   Plugins
   Prepared Statements
   Replication Statements
   Storage Engines
   Table Maintenance
   Transactions
   Utility
   ```
### 13.8.4 USE ステートメント
USE ステートメントは、指定されたデータベースを後続のステートメントのデフォルト (現行) データベースとして使用するように MySQL に指示します
```sql
mysql> use sakila;
Database changed
```