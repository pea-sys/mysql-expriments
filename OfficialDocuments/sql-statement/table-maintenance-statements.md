### 13.7.3 テーブル保守ステートメント
### 13.7.3.1 ANALYZE TABLE ステートメント
ANALYZE TABLE では、テーブル統計が生成されます

ヒストグラム統計分析  
オプション WITH N BUCKETS 句では、ヒストグラムのバケット数を指定
```sql
mysql> ANALYZE TABLE t UPDATE HISTOGRAM ON a, b, c WITH 10 BUCKETS;
+----------+-----------+----------+----------------------------------------------+
| Table    | Op        | Msg_type | Msg_text                                     |
+----------+-----------+----------+----------------------------------------------+
| sakila.t | histogram | status   | Histogram statistics created for column 'a'. |
| sakila.t | histogram | status   | Histogram statistics created for column 'b'. |
| sakila.t | histogram | status   | Histogram statistics created for column 'c'. |
+----------+-----------+----------+----------------------------------------------+
3 rows in set (0.01 sec)

mysql> ANALYZE TABLE t UPDATE HISTOGRAM ON a, b WITH 10 BUCKETS;
+----------+-----------+----------+----------------------------------------------+
| Table    | Op        | Msg_type | Msg_text                                     |
+----------+-----------+----------+----------------------------------------------+
| sakila.t | histogram | status   | Histogram statistics created for column 'a'. |
| sakila.t | histogram | status   | Histogram statistics created for column 'b'. |
+----------+-----------+----------+----------------------------------------------+
2 rows in set (0.01 sec)

mysql> ANALYZE TABLE t DROP HISTOGRAM ON b;
+----------+-----------+----------+----------------------------------------------+
| Table    | Op        | Msg_type | Msg_text                                     |
+----------+-----------+----------+----------------------------------------------+
| sakila.t | histogram | status   | Histogram statistics removed for column 'b'. |
+----------+-----------+----------+----------------------------------------------+
1 row in set (0.00 sec)
```

サンプリングを示します。 この例では、データ量が histogram_generation_max_mem_size の制限を超えていることを確認するために、employees テーブルの birth_date カラムのヒストグラム統計を生成する前に、制限は低い値 (2000000 バイト) に設定されます
```sql
mysql> SET histogram_generation_max_mem_size = 2000000;
Query OK, 0 rows affected (0.00 sec)

mysql> USE employees;
Database changed
mysql>  ANALYZE TABLE employees UPDATE HISTOGRAM ON birth_date WITH 16 BUCKETS\G
*************************** 1. row ***************************
   Table: employees.employees
      Op: histogram
Msg_type: status
Msg_text: Histogram statistics created for column 'birth_date'.
1 row in set (0.11 sec)

mysql> SELECT HISTOGRAM->>'$."sampling-rate"'
    ->        FROM INFORMATION_SCHEMA.COLUMN_STATISTICS
    ->        WHERE TABLE_NAME = "employees"
    ->        AND COLUMN_NAME = "birth_date";
+---------------------------------+
| HISTOGRAM->>'$."sampling-rate"' |
+---------------------------------+
| 0.05560504404475539             |
+---------------------------------+
1 row in set (0.01 sec)
```
sampled_pages_read および sampled_pages_skipped INNODB_METRICS カウンタを使用して、InnoDB データページのサンプリングを監視できます

ヒストグラム統計を生成する前にカウンタを有効にする必要があるサンプリングカウンタの使用方法を示しています
```sql
mysql> SET GLOBAL innodb_monitor_enable = 'sampled%';
Query OK, 0 rows affected (0.00 sec)

mysql> USE employees;
Database changed
mysql> ANALYZE TABLE employees UPDATE HISTOGRAM ON birth_date WITH 16 BUCKETS\G
*************************** 1. row ***************************
   Table: employees.employees
      Op: histogram
Msg_type: status
Msg_text: Histogram statistics created for column 'birth_date'.
1 row in set (0.10 sec)

mysql> USE INFORMATION_SCHEMA;
Database changed
mysql> SELECT NAME, COUNT FROM INNODB_METRICS WHERE NAME LIKE 'sampled%'\G
*************************** 1. row ***************************
 NAME: sampled_pages_read
COUNT: 50
*************************** 2. row ***************************
 NAME: sampled_pages_skipped
COUNT: 836
2 rows in set (0.00 sec)
```

### 13.7.3.2 CHECK TABLE ステートメント
CHECK TABLE は、1 つまたは複数のテーブルをエラーがないかどうかチェックします
* QUICK

正しくないリンクをチェックするための行のスキャンを行いません
```sql
mysql> CHECK TABLE t FAST QUICK;
mysql> CHECK TABLE t QUICK;
+----------+-------+----------+----------+
| Table    | Op    | Msg_type | Msg_text |
+----------+-------+----------+----------+
| sakila.t | check | status   | OK       |
+----------+-------+----------+----------+
1 row in set (0.00 sec)
```
* FAST

正しく閉じられていないテーブルのみを検査します
```sql
mysql> CHECK TABLE t FAST;
+----------+-------+----------+----------+
| Table    | Op    | Msg_type | Msg_text |
+----------+-------+----------+----------+
| sakila.t | check | status   | OK       |
+----------+-------+----------+----------+
1 row in set (0.00 sec)
```
* CHANGED

最後のチェック以降に変更されたか、または正しく閉じられていないテーブルのみをチェックします
```sql
mysql> CHECK TABLE t CHANGED;
+----------+-------+----------+----------+
| Table    | Op    | Msg_type | Msg_text |
+----------+-------+----------+----------+
| sakila.t | check | status   | OK       |
+----------+-------+----------+----------+
1 row in set (0.00 sec)
```
* MEDIUM

削除されたリンクが有効であることを検証するために行をスキャンします
```sql
mysql> CHECK TABLE t MEDIUM;
+----------+-------+----------+----------+
| Table    | Op    | Msg_type | Msg_text |
+----------+-------+----------+----------+
| sakila.t | check | status   | OK       |
+----------+-------+----------+----------+
1 row in set (0.00 sec)
```

* EXTENDED

行ごとにすべてのキーの完全なキールックアップを実行します
```sql
mysql> CHECK TABLE t EXTENDED;
+----------+-------+----------+----------+
| Table    | Op    | Msg_type | Msg_text |
+----------+-------+----------+----------+
| sakila.t | check | status   | OK       |
+----------+-------+----------+----------+
1 row in set (0.00 sec)
```
### 13.7.3.4 OPTIMIZE TABLE ステートメント
OPTIMIZE TABLE では、テーブルデータおよび関連するインデックスデータの物理記憶域が再編成され、記憶領域が削減され、テーブルへのアクセス時の I/O の効率が向上します
```sql
mysql> OPTIMIZE TABLE t;
+----------+----------+----------+-------------------------------------------------------------------+
| Table    | Op       | Msg_type | Msg_text                                                          |
+----------+----------+----------+-------------------------------------------------------------------+
| sakila.t | optimize | note     | Table does not support optimize, doing recreate + analyze instead |
| sakila.t | optimize | status   | OK                                                                |
+----------+----------+----------+-------------------------------------------------------------------+
2 rows in set (0.06 sec)
```

### 13.7.3.5 REPAIR TABLE ステートメント
REPAIR TABLE は、特定のストレージエンジンに対してのみ、破損している可能性のあるテーブルを修復します

* NO_WRITE_TO_BINLOG または LOCAL

REPAIR TABLE ステートメントはレプリカにレプリケートされるようにバイナリログに書き込まれます

* QUICK

QUICK オプションを使用した場合、REPAIR TABLE はデータファイルではなく、インデックスファイルのみを修復しようとします。
```sql
mysql> REPAIR TABLE t QUICK;
+----------+--------+----------+---------------------------------------------------------+
| Table    | Op     | Msg_type | Msg_text                                                |
+----------+--------+----------+---------------------------------------------------------+
| sakila.t | repair | note     | The storage engine for the table doesn't support repair |
+----------+--------+----------+---------------------------------------------------------+
1 row in set (0.00 sec)
```
* EXTENDED

EXTENDED オプションを使用した場合、MySQL はソートしながら 1 回につき 1 つのインデックスを作成する代わりに、1 行ごとにインデックスを作成します。
```sql
mysql> REPAIR TABLE t EXTENDED;
+----------+--------+----------+---------------------------------------------------------+
| Table    | Op     | Msg_type | Msg_text                                                |
+----------+--------+----------+---------------------------------------------------------+
| sakila.t | repair | note     | The storage engine for the table doesn't support repair |
+----------+--------+----------+---------------------------------------------------------+
1 row in set (0.00 sec)
```

* USE_FRM 

USE_FRM オプションは、.MYI インデックスファイルがない場合や、そのヘッダーが破損している場合に使用できます
```Sql
mysql> REPAIR TABLE t USE_FRM;
+----------+--------+----------+---------------------------------------------------------+
| Table    | Op     | Msg_type | Msg_text                                                |
+----------+--------+----------+---------------------------------------------------------+
| sakila.t | repair | note     | The storage engine for the table doesn't support repair |
+----------+--------+----------+---------------------------------------------------------+
1 row in set (0.00 sec)
```