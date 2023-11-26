# 4.5.2 mysqladmin — A MySQL Server 管理プログラム
mysqladmin は管理操作を実行するためのクライアントです。

* create db_name:db_name という名前の新しいデータベースを作成します。
```
C:\Users\masami>mysqladmin create db_name-test -u root -p
Enter password: **********
C:\Users\masami>mysql -u root -p db_name-test
Enter password: **********
mysql>
```
* drop db_name:db_name という名前のデータベースとそのテーブルをすべて削除します。

```
C:\Users\masami>mysqladmin drop db_name-test -u root -p
Enter password: **********
Dropping the database is potentially a very bad thing to do.
Any data stored in the database will be destroyed.

Do you really want to drop the 'db_name-test' database [y/N] y
Database "db_name-test" dropped
```
* extended-status:サーバーステータス変数とその値を表示します。
```
C:\Users\masami>mysqladmin extended-status -u root -p
Enter password: **********
+-------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Variable_name                                         | Value
                              |
+-------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Aborted_clients                                       | 0
                              |
| Aborted_connects                                      | 9
                              |
| 
・・・
```
* flush-hosts:ホストキャッシュ内の情報をすべてフラッシュします。
```
C:\Users\masami>mysqladmin flush-hosts -u root -p
Enter password: **********
```
* flush-logs [log_type ...]:ログをすべてフラッシュします。
```
C:\Users\masami>mysqladmin flush-logs binary -u root -p
Enter password: **********
```
* flush-status:ステータス変数をクリアします。
```
C:\Users\masami>mysqladmin flush-status -u root -p
Enter password: **********
```

* flush-tables:テーブルをすべてフラッシュします。

```
C:\Users\masami>mysqladmin flush-table -u root -p
Enter password: **********
```
* flush-threads:スレッドキャッシュをフラッシュします。
```
C:\Users\masami>mysqladmin flush-thread -u root -p
Enter password: **********
```
* password new_password:新しいパスワードを設定します(非推奨)。
```
C:\Users\masami>mysqladmin password ******** -u root -p
Enter password: ********
mysqladmin: [Warning] Using a password on the command line interface can be insecure.
Warning: Since password will be sent to server in plain text, use ssl connection to ensure password safety.

```

* ping:サーバーが使用可能かどうかをチェックします。
```
C:\Users\masami>mysqladmin ping -u root -p
Enter password: **********
mysqld is alive
```
* processlist:アクティブなサーバースレッドのリストを表示します。
```
C:\Users\masami>mysqladmin processlist -u root -p
Enter password: **********
+-----+-----------------+-----------------+----+---------+-------+------------------------+------------------+
| Id  | User            | Host            | db | Command | Time  | State                  | Info             |
+-----+-----------------+-----------------+----+---------+-------+------------------------+------------------+
| 5   | event_scheduler | localhost       |    | Daemon  | 18072 | Waiting on empty queue |                  |
| 526 | root            | localhost:55076 |    | Sleep   | 511   |                        |                  |
| 527 | root            | localhost:55077 |    | Sleep   | 511   |                        |                  |
| 543 | root            | localhost       |    | Query   | 0     | init                   | show processlist |
+-----+-----------------+-----------------+----+---------+-------+------------------------+------------------+
```
* reload:付与テーブルをリロードします。
```
C:\Users\masami>mysqladmin reload -u root -p
Enter password: **********
```
* refresh:全テーブルをフラッシュし、ログファイルを閉じて、開きます。
```
C:\Users\masami>mysqladmin refresh -u root -p
Enter password: **********
```
* shutdown:サーバーを停止します。
```
C:\Users\masami>mysqladmin shutdown -u root -p
Enter password: **********

C:\Users\masami>mysqladmin ping -u root -p
Enter password: **********
mysqladmin: connect to server at 'localhost' failed
error: 'Can't connect to MySQL server on 'localhost:3306' (10061)'
Check that mysqld is running on localhost and that the port is 3306.
You can check this by doing 'telnet localhost 3306'
```
* status:短いサーバーステータスメッセージを表示します。
```
C:\Users\masami>mysqladmin status -u root -p
Enter password: **********
Uptime: 193  Threads: 2  Questions: 4  Slow queries: 0  Opens: 121  Flush tables: 3  Open tables: 40  Queries per second avg: 0.020
```

* variables:サーバーシステム変数とその値を表示します。
```
C:\Users\masami>mysqladmin status -u root -p
Enter password: **********
Uptime: 193  Threads: 2  Questions: 4  Slow queries: 0  Opens: 121  Flush tables: 3  Open tables: 40  Queries per second avg: 0.020

C:\Users\masami>mysqladmin variables -u root -p
Enter password: **********
+----------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Variable_name                                            | Value



                                                 |
+----------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| activate_all_roles_on_login                              | OFF
                                                 |
| admin_address                                            |
                                                 |
| admin_port                                               | 33062
・・・
```
* variables:サーバーシステム変数とその値を表示します。
```
C:\Users\masami>mysqladmin version -u root -p
Enter password: **********
mysqladmin  Ver 8.2.0 for Win64 on x86_64 (MySQL Community Server - GPL)
Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          8.2.0
Protocol version        10
Connection              Shared memory: MYSQL
TCP port                0
Uptime:                 5 min 19 sec

Threads: 2  Questions: 8  Slow queries: 0  Opens: 138  Flush tables: 3  Open tables: 57  Queries per second avg: 0.025
```