# 4.5.1 mysql — MySQL コマンドラインクライアント

サーバーアクセス
```
PS C:\Users\masami> mysql --user=root --password sakila
Enter password: **********
```
スクリプトファイル実行
```
C:\Users\masami>mysql --user=root --password sakila < "C:\Users\masami\source\repos\mysql-expriments\OfficialDocuments\mysql-client-command\script.sql" > output.tab
Enter password: **********
```
* --auto-rehash:名前補完機能。  無効にすると動作が早くなるが、Windowsでは常に機能しない。

* --auto-vertical-output:結果セットが現在のウィンドウに対して大きすぎる場合には縦に表示
    * 無効時
    ```
    mysql> select 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30;
    +---+---+---+---+---+---+---+---+---+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+
    | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 | 25 | 26 | 27 | 28 | 29 | 30 |
    +---+---+---+---+---+---+---+---+---+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+
    | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 | 25 | 26 | 27 | 28 | 29 | 30 |
    +---+---+---+---+---+---+---+---+---+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+----+
    1 row in set (0.00 sec)
    ```
    * 有効時
    ```
    C:\Users\masami>mysql --user=root --password sakila --auto-vertical-output
    Enter password: **********
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 19
    Server version: 8.2.0 MySQL Community Server - GPL

    Copyright (c) 2000, 2023, Oracle and/or its affiliates.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> select 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30;
    *************************** 1. row ***************************
    1: 1
    2: 2
    3: 3
    4: 4
    5: 5
    6: 6
    7: 7
    8: 8
    9: 9
    10: 10
    11: 11
    12: 12
    13: 13
    14: 14
    15: 15
    16: 16
    17: 17
    18: 18
    19: 19
    20: 20
    21: 21
    22: 22
    23: 23
    24: 24
    25: 25
    26: 26
    27: 27
    28: 28
    29: 29
    30: 30
    1 row in set (0.00 sec)
    ```

* --batch:カラム区切り文字としてタブを使用し、各行を新しい行に出力します。
※Windowsで機能しない。コマンドプロンプトがクラッシュするのでバグレポート提出。
```
mysql --user=root --password sakila --batch
```

* --binary-as-hex を有効にすると、CHAR() や UNHEX() などの関数によって返されるものも含め、すべてのバイナリ文字列の表示に影響します。
    * 有効(デフォルト)
    ```
    C:\Users\masami>mysql --user=root --password sakila --binary-as-hex
    Enter password: **********

    mysql> SELECT CHAR(0x41), UNHEX('41');
    +------------------------+--------------------------+
    | CHAR(0x41)             | UNHEX('41')              |
    +------------------------+--------------------------+
    | 0x41                   | 0x41                     |
    +------------------------+--------------------------+
    1 row in set (0.00 sec)
    ```
    * 無効(--skip-binary-as-hex)
    ```
    C:\Users\masami>mysql --user=root --password sakila --skip-binary-as-hex
    Enter password: **********
    
    mysql> SELECT CHAR(0x41), UNHEX('41');
    +------------+-------------+
    | CHAR(0x41) | UNHEX('41') |
    +------------+-------------+
    | A          | A           |
    +------------+-------------+
    1 row in set (0.00 sec)
    ```

* --column-type-info:結果セットのメタデータを表示します。
```
C:\Users\masami>mysql --user=root --password sakila --column-type-info
Enter password: **********
mysql> select 1,2,3;
Field   1:  `1`
Catalog:    `def`
Database:   ``
Table:      ``
Org_table:  ``
Type:       LONGLONG
Collation:  binary (63)
Length:     2
Max_length: 1
Decimals:   0
Flags:      NOT_NULL BINARY NUM

Field   2:  `2`
Catalog:    `def`
Database:   ``
Table:      ``
Org_table:  ``
Type:       LONGLONG
Collation:  binary (63)
Length:     2
Max_length: 1
Decimals:   0
Flags:      NOT_NULL BINARY NUM

Field   3:  `3`
Catalog:    `def`
Database:   ``
Table:      ``
Org_table:  ``
Type:       LONGLONG
Collation:  binary (63)
Length:     2
Max_length: 1
Decimals:   0
Flags:      NOT_NULL BINARY NUM


+---+---+---+
| 1 | 2 | 3 |
+---+---+---+
| 1 | 2 | 3 |
+---+---+---+
1 row in set (0.00 sec)
```
* --compress, -C:可能であれば、クライアントとサーバーの間で送信されるすべての情報を圧縮します。将来削除予定。
* --compression-algorithms=value:サーバーへの接続に許可される圧縮アルゴリズム。上記オプションより高速。
```
C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.096 seconds
        Minimum number of seconds to run all queries: 0.031 seconds
        Maximum number of seconds to run all queries: 1.047 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0


C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql -C -u root -p
WARNING: --compress is deprecated and will be removed in a future version. Use --compression-algorithms instead.
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.206 seconds
        Minimum number of seconds to run all queries: 0.047 seconds
        Maximum number of seconds to run all queries: 1.156 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0

C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql --compression-algorithms=zstd -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.100 seconds
        Minimum number of seconds to run all queries: 0.031 seconds
        Maximum number of seconds to run all queries: 1.047 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0


C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql --compression-algorithms=zlib -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.100 seconds
        Minimum number of seconds to run all queries: 0.046 seconds
        Maximum number of seconds to run all queries: 1.047 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0
```
* --connect-timeout=value:接続タイムアウトまでの秒数(初期値0)
```
C:\Users\masami>mysql --user=root --password sakila --connect-timeout=1
Enter password: **********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 440
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
* --delimiter=str:ステートメント区切り文字を設定します。 デフォルトはセミコロン (;) 文字です
```
C:\Users\masami>mysql --user=root --password sakila --delimiter=:y
Enter password: **********
mysql> select 1:y
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)
```
* --dns-srv-name=name:MySQL サーバーへの接続の確立に使用する候補ホストを決定する DNS SRV レコードの名前を指定します。
```
C:\Users\masami>mysql --user=root --password sakila --dns-srv-name=example.com
Enter password: **********
ERROR 2070 (HY000): DNS SRV lookup failed with error : 9501
```

* --execute=statement, -e statement:ステートメントを実行して、終了します。
```
C:\Users\masami>mysql -u root -p sakila --execute="SELECT VERSION();SELECT NOW()"
Enter password: **********
+-----------+
| VERSION() |
+-----------+
| 8.2.0     |
+-----------+
+---------------------+
| NOW()               |
+---------------------+
| 2023-11-22 17:46:40 |
+---------------------+
```

* --html, -H:HTML 出力を生成します。
```
C:\Users\masami>mysql -u root -p sakila --html
Enter password: **********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 14
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select count(*) from actor;
```
<TABLE BORDER=1><TR><TH>count(*)</TH></TR><TR><TD>200</TD></TR></TABLE>1 row in set (0.03 sec)

* --login-path=name:.mylogin.cnf ログインパスファイルの指定されたログインパスからオプションを読み取ります。
```
mysql> CREATE USER sample IDENTIFIED BY RANDOM PASSWORD;
+--------+------+----------------------+-------------+
| user   | host | generated password   | auth_factor |
+--------+------+----------------------+-------------+
| sample | %    | U34ytHX}ecnAVb;[b*Yc |           1 |
+--------+------+----------------------+-------------+
1 row in set (0.02 sec)
```
.mylogin.cnfにsampleユーザーを書き込む
```
C:\Users\masami>mysql_config_editor set --login-path=sample --host=localhost --user=sample --password
Enter password: ********************
```
sampleセクションを明示してログイン  
アクセス拒否だけどログイン突破
```
C:\Users\masami>mysql --login-path=sample sakila
ERROR 1044 (42000): Access denied for user 'sample'@'%' to database 'sakila'
```
余計なパラメータを付加したり、データベース名を先に指定するとコマンドエラーになる

* --protocol={TCP|SOCKET|PIPE|MEMORY}:サーバーへの接続に使用するトランスポートプロトコル。  

```
C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql --protocol=TCP -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.029 seconds
        Minimum number of seconds to run all queries: 0.015 seconds
        Maximum number of seconds to run all queries: 0.047 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0


C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql --protocol=PIPE -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.031 seconds
        Minimum number of seconds to run all queries: 0.015 seconds
        Maximum number of seconds to run all queries: 0.047 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0


C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql --protocol=MEMORY -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.089 seconds
        Minimum number of seconds to run all queries: 0.015 seconds
        Maximum number of seconds to run all queries: 1.032 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0
```
MEMORYが案外遅い。

* --reconnect:サーバーとの接続が失われたとき、再接続を自動的に試行します。

```
C:\Users\masami>mysql -u root -p sakila -p
Enter password: **********

mysql> select 1;
ERROR 2013 (HY000): Lost connection to MySQL server during query
No connection. Trying to reconnect...
Connection id:    8
Current database: sakila

+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.01 sec)
C:\Users\masami>mysql -u root -p sakila --skip-reconnect -p
Enter password: **********

mysql> select 1;
ERROR 2013 (HY000): Lost connection to MySQL server during query
```

* --safe-updates, --i-am-a-dummy, -U

このオプションを有効にすると、WHERE 句または LIMIT 句でキーを使用しない UPDATE および DELETE ステートメントでエラーが発生します。 
```
C:\Users\masami>mysql -u root -p sakila --safe-updates -p
Enter password: **********

mysql> update actor set last_name = 'test';
ERROR 1175 (HY000): You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column.
```

* --select-limit=value:--safe-updates 使用時の、自動的に設定される SELECT ステートメントの制限。 (デフォルト値は 1,000 です。)  

```
C:\Users\masami>mysql -u root -p sakila --safe-updates --select-limit=30 -p
Enter password: **********

mysql> select count(*) from actor where first_name like '%A';
+----------+
| count(*) |
+----------+
|       30 |
+----------+
1 row in set (0.00 sec)

mysql> update actor set last_name = 'test' where first_name like '%A';
ERROR 1175 (HY000): You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column.
mysql> update actor set last_name = 'test' where first_name like '%A' limit 50;
Query OK, 30 rows affected (0.01 sec)
Rows matched: 30  Changed: 30  Warnings: 0
```
LIMIT句をつけるとアップデート出来る

* --shared-memory-base-name=name:Windows の場合、共有メモリを使用してローカルサーバに接続するために使用する共有メモリ名。 デフォルト値は MYSQL です。
```
C:\Users\masami>mysql -u root -protocol=MEMORY --shared-memory-base-name=TEST -p sakila
mysql: [Warning] Using a password on the command line interface can be insecure.
Enter password: **********
```

* --silent, -s:サイレントモード。 出力の生成を少なくします。
```
C:\Users\masami>mysql -u root -s -p sakila
Enter password: **********
mysql> select 1;
1
1
mysql> exit

C:\Users\masami>mysql -u root -s -s -p sakila
Enter password: **********
mysql> select 1;
1
mysql> exit
```

* --skip-column-names, -N:結果にカラム名を記述しません。
```
C:\Users\masami>mysql -u root --skip-column-names -p sakila
Enter password: **********


mysql> select 1;
+---+
| 1 |
+---+
1 row in set (0.00 sec)
```
* --verbose, -v:冗長モード。 プログラムの動作についてより多くの出力を生成します。

```
C:\Users\masami>mysql -u root  -s -p sakila
Enter password: **********
mysql> select count(*) from actor;
count(*)
200

C:\Users\masami>mysql -u root  -s -v -v -v -v -p sakila
Enter password: **********
mysql> select count(*) from actor;
--------------
select count(*) from actor
--------------

+----------+
| count(*) |
+----------+
|      200 |
+----------+
```
* --version, -V:バージョン情報を表示して終了します。
```
C:\Users\masami>mysql -V
mysql  Ver 8.2.0 for Win64 on x86_64 (MySQL Community Server - GPL)
```
* --vertical, -E:クエリー出力行を縦に出力します (カラム値ごとに一行)。
```
C:\Users\masami>mysql -u root  --vertical -p sakila
Enter password: **********
mysql> select * from actor limit 5;
*************************** 1. row ***************************
   actor_id: 1
 first_name: PENELOPE
  last_name: GUINESS
last_update: 2006-02-15 04:34:33
*************************** 2. row ***************************
   actor_id: 2
 first_name: NICK
  last_name: WAHLBERG
last_update: 2006-02-15 04:34:33
*************************** 3. row ***************************
   actor_id: 3
 first_name: ED
  last_name: CHASE
last_update: 2006-02-15 04:34:33
*************************** 4. row ***************************
   actor_id: 4
 first_name: JENNIFER
  last_name: DAVIS
last_update: 2006-02-15 04:34:33
*************************** 5. row ***************************
   actor_id: 5
 first_name: JOHNNY
  last_name: LOLLOBRIGIDA
last_update: 2006-02-15 04:34:33
5 rows in set (0.00 sec)
```

* --wait, -w:接続が確立できない場合、中止せずに待機してからリトライします。
```
C:\Users\masami>mysql -u root  --wait -p sakila
Enter password: **********
mysql> select * from actor limit 5;
No connection. Trying to reconnect...
Connection id:    8
Current database: sakila

+----------+------------+--------------+---------------------+
| actor_id | first_name | last_name    | last_update         |
+----------+------------+--------------+---------------------+
|        1 | PENELOPE   | GUINESS      | 2006-02-15 04:34:33 |
|        2 | NICK       | WAHLBERG     | 2006-02-15 04:34:33 |
|        3 | ED         | CHASE        | 2006-02-15 04:34:33 |
|        4 | JENNIFER   | DAVIS        | 2006-02-15 04:34:33 |
|        5 | JOHNNY     | LOLLOBRIGIDA | 2006-02-15 04:34:33 |
+----------+------------+--------------+---------------------+
5 rows in set (0.01 sec)
```
* --xml, -X:XML 出力を生成
```
C:\Users\masami>mysql -u root  --xml -p sakila
Enter password: **********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select * from actor limit 5;
<?xml version="1.0"?>

<resultset statement="select * from actor limit 5;" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <row>
        <field name="actor_id">1</field>
        <field name="first_name">PENELOPE</field>
        <field name="last_name">GUINESS</field>
        <field name="last_update">2006-02-15 04:34:33</field>
  </row>

  <row>
        <field name="actor_id">2</field>
        <field name="first_name">NICK</field>
        <field name="last_name">WAHLBERG</field>
        <field name="last_update">2006-02-15 04:34:33</field>
  </row>

  <row>
        <field name="actor_id">3</field>
        <field name="first_name">ED</field>
        <field name="last_name">CHASE</field>
        <field name="last_update">2006-02-15 04:34:33</field>
  </row>

  <row>
        <field name="actor_id">4</field>
        <field name="first_name">JENNIFER</field>
        <field name="last_name">DAVIS</field>
        <field name="last_update">2006-02-15 04:34:33</field>
  </row>

  <row>
        <field name="actor_id">5</field>
        <field name="first_name">JOHNNY</field>
        <field name="last_name">LOLLOBRIGIDA</field>
        <field name="last_update">2006-02-15 04:34:33</field>
  </row>
</resultset>
5 rows in set (0.00 sec)
```
* --zstd-compression-level=level:zstd 圧縮アルゴリズムを使用するサーバーへの接続に使用する圧縮レベル。
```
C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql --compression-algorithms=zstd --zstd-compression-level=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.080 seconds
        Minimum number of seconds to run all queries: 0.015 seconds
        Maximum number of seconds to run all queries: 1.032 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0


C:\Users\masami>mysqlslap --concurrency=5 --iterations=20 --number-char-cols=3 --auto-generate-sql --compression-algorithms=zstd --zstd-compression-level=22 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.352 seconds
        Minimum number of seconds to run all queries: 0.094 seconds
        Maximum number of seconds to run all queries: 1.484 seconds
        Number of clients running queries: 5
        Average number of queries per client: 0
```

* status, \s:使用中の接続とサーバーに関するステータス情報を表示します。
```
mysql> status
--------------
mysql  Ver 8.2.0 for Win64 on x86_64 (MySQL Community Server - GPL)

Connection id:          519
Current database:       sakila
Current user:           root@localhost
SSL:                    Not in use
Using delimiter:        ;
Server version:         8.2.0 MySQL Community Server - GPL
Protocol version:       10
Connection:             Shared memory: MYSQL
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    cp932
Conn.  characterset:    cp932
TCP port:               0
Binary data as:         Hexadecimal
Uptime:                 8 min 18 sec

Threads: 2  Questions: 16322  Slow queries: 0  Opens: 813  Flush tables: 3  Open tables: 162  Queries per second avg: 32.775
--------------
```

* system command, \! command:デフォルトのコマンドインタプリタを使って指定のコマンドを実行します。
```
mysql> system ping 127.0.0.1

127.0.0.1 に ping を送信しています 32 バイトのデータ:
127.0.0.1 からの応答: バイト数 =32 時間 <1ms TTL=128
127.0.0.1 からの応答: バイト数 =32 時間 <1ms TTL=128
127.0.0.1 からの応答: バイト数 =32 時間 <1ms TTL=128
127.0.0.1 からの応答: バイト数 =32 時間 <1ms TTL=128

127.0.0.1 の ping 統計:
    パケット数: 送信 = 4、受信 = 4、損失 = 0 (0% の損失)、
ラウンド トリップの概算時間 (ミリ秒):
    最小 = 0ms、最大 = 0ms、平均 = 0ms
```
* use db_name, \u db_name:db_name をデフォルトデータベースとして使用します。
```
mysql> use world
Database changed
```