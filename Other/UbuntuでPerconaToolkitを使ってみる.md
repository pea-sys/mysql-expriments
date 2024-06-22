https://docs.percona.com/percona-toolkit/index.html

```
sudo apt-get install percona-toolkit
```

### pt_align

出力内容を見やすく整形します

```
root@masami-L:~# vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 5947616  53796 991836    0    0  1067    54  213  372  3  2 94  1  0
root@masami-L:~#  vmstat | pt-align
r b swpd    free  buff  cache si so  bi bo  in  cs us sy id wa st
1 0    0 5948572 53940 991932  0  0 745 38 175 306  2  2 96  0  0
```

### pt-archibe

指定したテーブルのレコードを、他のテーブルに取り込める形式のファイルにアーカイブします

次のテーブル内容をアーカイブします

```sql
root@masami-L /# mysql -u root employees
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.36-0ubuntu0.22.04.1 (Ubuntu)

Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select * from employees where emp_no between 10100 and 10110;
+--------+------------+------------+------------+--------+------------+
| emp_no | birth_date | first_name | last_name  | gender | hire_date  |
+--------+------------+------------+------------+--------+------------+
|  10100 | 1953-04-21 | Hironobu   | Haraldson  | F      | 1987-09-21 |
|  10101 | 1952-04-15 | Perla      | Heyers     | F      | 1992-12-28 |
|  10102 | 1959-11-04 | Paraskevi  | Luby       | F      | 1994-01-26 |
|  10103 | 1953-11-26 | Akemi      | Birch      | M      | 1986-12-02 |
|  10104 | 1961-11-19 | Xinyu      | Warwick    | M      | 1987-04-16 |
|  10105 | 1962-02-05 | Hironoby   | Piveteau   | M      | 1999-03-23 |
|  10106 | 1952-08-29 | Eben       | Aingworth  | M      | 1990-12-19 |
|  10107 | 1956-06-13 | Dung       | Baca       | F      | 1994-03-22 |
|  10108 | 1952-04-07 | Lunjin     | Giveon     | M      | 1986-10-02 |
|  10109 | 1958-11-25 | Mariusz    | Prampolini | F      | 1993-06-16 |
|  10110 | 1957-03-07 | Xuejia     | Ullian     | F      | 1986-08-22 |
+--------+------------+------------+------------+--------+------------+
```

```
root@masami-L /# pt-archiver --no-check-charset --no-delete --source=localhost,D=employees,t=employees --file "/tmp/pt-archiver_resul
t.txt" --where "emp_no between 10100 and 10100"
root@masami-L /# cat /tmp/pt-archiver_result.txt
10100   1953-04-21      Hironobu        Haraldson       F       1987-09-21
```

出力したファイルは`LOAD DATA INFILE`で取り込めます。
行レベルで簡単にバックアップが取れるのは便利。`--no-delete`オプションがないと対象行が削除されます。

### pt-config-diff

２つの MySQL の設定ファイルの差分を出力します

```
root@masami-L /e/mysql# pt-config-diff  my.cnf  my.2.conf
1 config difference
Variable                  my.cnf my.2.conf
========================= ====== =========
mysqlx                    0      1
```

### pt-deadlock-logger

データベースのデッドロック情報をダンプします
類似の機能が MySQL にあるので出番はなさそう

### pt-diskstats

ディスク I/O の統計情報を出力します
`iostats`があるので出番はなさそう

### pt-duplicate-key-checker

重複インデックスを検出します

プリマリキーにインデックスを張ります

```
mysql> create index emp_no_emp on employees(emp_no);
Query OK, 0 rows affected (2.66 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

```
root@masami-L /e/percona-toolkit# pt-duplicate-key-checker
# ########################################################################
# employees.employees
# ########################################################################

# emp_no_emp is a duplicate of PRIMARY
# Key definitions:
#   KEY `emp_no_emp` (`emp_no`)
#   PRIMARY KEY (`emp_no`),
# Column types:
#         `emp_no` int not null
# To remove this duplicate index, execute:
ALTER TABLE `employees`.`employees` DROP INDEX `emp_no_emp`;

# ########################################################################
# Summary of indexes
# ########################################################################

# Size Duplicate Indexes   1166684
# Total Duplicate Indexes  1
# Total Indexes            17
```

削除用 SQL も紹介されます

### pt-fifo-split

ファイルから指定した行範囲ずつ、内容を FIFO で一時ファイルに読み込みます  
使い道があまり想像できないため割愛

### pt-find

MySQL のテーブルを指定したアクションに基づいて検索します。  
検索テーブルに対し、SQL を実行することも可能。

```
root@masami-L ~# pt-find --engine InnoDB
`employees`.`deadlocks`
`employees`.`departments`
`employees`.`dept_emp`
・・・
`sakila`.`payment`
`sakila`.`rental`
`sakila`.`staff`
`sakila`.`store`
`sample`.`tbl`
`sys`.`sys_config`
`world`.`city`
`world`.`country`
`world`.`countrylanguage`
```

半年以上前に作成された InnoDB エンジンのテーブルを検索

```
root@masami-L ~# pt-find --ctime +180 --engine InnoDB
`mysql`.`innodb_index_stats`
`mysql`.`innodb_table_stats`
`mysql`.`replication_group_configuration_version`
```

全てのテーブル、インデックスをサイズでソート

```
root@masami-L ~ [1]# pt-find --printf "%T\t%D.%N\n" | sort -rn
77185024        `employees`.`salaries`
20512768        `employees`.`titles`
17858560        `employees`.`dept_emp`
15220736        `employees`.`employees`
2785280 `sakila`.`rental`
2228224 `sakila`.`payment`
1687552 `mysql`.`help_topic`
524288  `world`.`city`
376832  `sakila`.`inventory`
278528  `sakila`.`film`
278528  `sakila`.`film_actor`
262144  `mysql`.`help_keyword`
196608  `sakila`.`film_text`
163840  `world`.`countrylanguage`
131072  `sakila`.`customer`
114688  `world`.`country`
・・・
0       `performance_schema`.`binary_log_transaction_compression_stats`
0       `performance_schema`.`accounts`
0       `mysql`.`slow_log`
0       `mysql`.`general_log`
0       `employees`.`dept_emp_latest_date`
0       `employees`.`current_dept_emp`
```

### pt-fingerprint

改行や書き方の異なる複数のクエリを正規化した同一のクエリになるよう変換して出力  
同一クエリの収集に使える

クエリをファイルに書き込む

```
SELECT name, password FROM user WHERE id='12345';

select name, password from user
 where id = 5;


SELECT name,password FROM user
WHERE id = 39;
```

コマンド実行

```
root@masami-L ~# pt-fingerprint  fingerprint_test.txt
select name, password from user where id=?
select name, password from user where id = ?
select name,password from user where id = ?
```

### pt-fk-error-logger

外部キー制約に関するエラー情報を標準出力します
ログを監視すれば同様のことが出来るため、出番はなさそう

### pt-heartbeat

MySQL サーバのレプリケーションの遅延を測定します

### pt-index-usage

スロークエリログからクエリを読み込み、使用されていないインデックスを調査して、削除する為のクエリを出力します。  
MySQL の設定をスロークエリーを出力する設定にする必要があります。

不要なインデックスを作成

```
mysql> alter table employees add index idx_birth_date(birth_date);
Query OK, 0 rows affected (3.61 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> alter table employees add index idx_first_date(first_name);
Query OK, 0 rows affected (3.81 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

スロークエリを実行

```
select * from employees;
```

pt-index-usage 実行  
良くわからない結果になってしまった

````
root@masami-L /e/mysql [SIGINT]# pt-index-usage /var/log/mysql/mysql-slow.log
/var/log/mysql/mysql-slow.log:  10% 04:27 remain
/var/log/mysql/mysql-slow.log:  20% 03:56 remain
/var/log/mysql/mysql-slow.log:  29% 03:30 remain
/var/log/mysql/mysql-slow.log:  39% 03:00 remain
/var/log/mysql/mysql-slow.log:  50% 02:29 remain
/var/log/mysql/mysql-slow.log:  60% 01:58 remain
/var/log/mysql/mysql-slow.log:  69% 01:30 remain
/var/log/mysql/mysql-slow.log:  79% 01:00 remain
/var/log/mysql/mysql-slow.log:  90% 00:29 remain
DBD::mysql::db selectall_arrayref failed: Expression #6 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'information_schema.FILES.EXTRA' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by [for Statement "EXPLAIN SELECT LOGFILE_GROUP_NAME, FILE_NAME, TOTAL_EXTENTS, INITIAL_SIZE, ENGINE, EXTRA FROM INFORMATION_SCHEMA.FILES WHERE ENGINE = 'ndbcluster' AND FILE_TYPE = 'UNDO LOG' AND FILE_NAME IS NOT NULL AND LOGFILE_GROUP_NAME IS NOT NULL AND LOGFILE_GROUP_NAME IN (SELECT DISTINCT LOGFILE_GROUP_NAME FROM INFORMATION_SCHEMA.FILES WHERE ENGINE = 'ndbcluster' AND FILE_TYPE = 'DATAFILE' AND TABLESPACE_NAME IN (SELECT DISTINCT TABLESPACE_NAME FROM INFORMATION_SCHEMA.PARTITIONS WHERE TABLE_SCHEMA IN ('employees'))) GROUP BY LOGFILE_GROUP_NAME, FILE_NAME, ENGINE, TOTAL_EXTENTS, INITIAL_SIZE ORDER BY LOGFILE_GROUP_NAME"] at /usr/bin/pt-index-usage line 4620, <$fh> line 13470759.
DBD::mysql::db do failed: Unknown database '`employees`' [for Statement "USE ```employees```"] at /usr/bin/pt-index-usage line 6705, <$fh> line 13470868.
DBD::mysql::db selectall_arrayref failed: Expression #6 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'information_schema.FILES.EXTRA' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by [for Statement "EXPLAIN SELECT LOGFILE_GROUP_NAME, FILE_NAME, TOTAL_EXTENTS, INITIAL_SIZE, ENGINE, EXTRA FROM INFORMATION_SCHEMA.FILES WHERE ENGINE = 'ndbcluster' AND FILE_TYPE = 'UNDO LOG' AND FILE_NAME IS NOT NULL AND LOGFILE_GROUP_NAME IS NOT NULL AND LOGFILE_GROUP_NAME IN (SELECT DISTINCT LOGFILE_GROUP_NAME FROM INFORMATION_SCHEMA.FILES WHERE ENGINE = 'ndbcluster' AND FILE_TYPE = 'DATAFILE' AND TABLESPACE_NAME IN (SELECT DISTINCT TABLESPACE_NAME FROM INFORMATION_SCHEMA.PARTITIONS WHERE TABLE_SCHEMA IN ('employees'))) GROUP BY LOGFILE_GROUP_NAME, FILE_NAME, ENGINE, TOTAL_EXTENTS, INITIAL_SIZE ORDER BY LOGFILE_GROUP_NAME"] at /usr/bin/pt-index-usage line 4620, <$fh> line 15769597.
````

### pt-ioprofile

mysql プロセスによる fifo を監視します

やってみたが、機能しませんでした。

```
root@masami-L ~# pt-ioprofile --cell sizes -run-time 60
Tue May 28 05:25:34 PM JST 2024
Tracing process ID 5170
     total filename
```

```
root@masami-L /# mysqldump --single-transaction -u root -p employees > employees.sql
Enter password:
```

### pt-kill

条件を満たしたクエリを取り消します

２秒以上かかったクエリを取り消し

```
root@masami-L /e/percona-toolkit# pt-kill --busy-time 1 --kill --print
```

```
mysql> select * from salaries limit 5;
+--------+--------+------------+------------+
| emp_no | salary | from_date  | to_date    |
+--------+--------+------------+------------+
|  10001 |  60117 | 1986-06-26 | 1987-06-26 |
|  10001 |  62102 | 1987-06-26 | 1988-06-25 |
|  10001 |  66074 | 1988-06-25 | 1989-06-25 |
|  10001 |  66596 | 1989-06-25 | 1990-06-25 |
|  10001 |  66961 | 1990-06-25 | 1991-06-25 |
+--------+--------+------------+------------+
5 rows in set (0.00 sec)

mysql> select * from salaries;
ERROR 2013 (HY000): Lost connection to MySQL server during query
```

取り消し後にクエリが出力されます

```
root@masami-L /e/percona-toolkit# pt-kill --busy-time 1 --kill --print
# 2024-05-28T17:54:11 KILL 19 (Query 1 sec) select * from salaries
```

### pt-mext

MySQL のステータス変化を出力します

ログイン失敗回数が徐々に増えていることが観察できます

```
root@masami-L /e/percona-toolkit# pt-mext -- "mysqladmin -p ext -i
10 -c 6" | grep "Aborted_connects"
Enter password:
Aborted_connects                              2                    3                    4                    4                    4
```

### pt-summary

MySQL のステータス変数を用途ごとにサマリーを出力

```
# Percona Toolkit MySQL Summary Report #######################
              System time | 2024-05-28 09:13:11 UTC (local TZ: JST +0900)
# Instances ##################################################
  Port  Data Directory             Nice OOM Socket
  ===== ========================== ==== === ======
# MySQL Executable ###########################################
       Path to executable | /usr/sbin/mysqld
              Has symbols | Yes
# Slave Hosts ################################################
No slaves found
# Report On Port 3306 ########################################
                     User | root@localhost
                     Time | 2024-05-28 18:13:11 (JST)
                 Hostname | masami-L
                  Version | 8.0.36-0ubuntu0.22.04.1 (Ubuntu)
                 Built On | Linux x86_64
                  Started | 2024-05-28 16:54 (up 0+01:18:35)
                Databases | 10
                  Datadir | /var/lib/mysql/
                Processes | 2 connected, 2 running
              Replication | Is not a slave, has 0 slaves connected
                  Pidfile | /var/lib/mysql/masami-L.pid (exists)
# Processlist ################################################

  Command                        COUNT(*) Working SUM(Time) MAX(Time)
  ------------------------------ -------- ------- --------- ---------
  Daemon                                1       1      4500      4500
  Query                                 2       2         0         0

  User                           COUNT(*) Working SUM(Time) MAX(Time)
  ------------------------------ -------- ------- --------- ---------
  event_scheduler                       1       1      4500      4500
  root                                  2       2         0         0

  Host                           COUNT(*) Working SUM(Time) MAX(Time)
```

### pt-online-schema-change

テーブルロックをせずにテーブルのスキーマを変更できます
が、バグでうまく動作しないことがあるので使用しない方がいいかもしれません

```
root@masami-L ~ [18]# pt-online-schema-change  --execute --alter-foreign-keys-m
ethod auto --alter "add column TEST int" h=localhost,D=employees,t=employees
No slaves found.  See --recursion-method if host masami-L has slaves.
Not checking slave lag because no slaves were found and --check-slave-lag was not specified.
Operation, tries, wait:
  analyze_table, 10, 1
  copy_rows, 10, 0.25
  create_triggers, 10, 1
  drop_triggers, 10, 1
  swap_tables, 10, 1
  update_foreign_keys, 10, 1
Child tables:
  `employees`.`dept_manager` (approx. 24 rows)
  `employees`.`dept_emp` (approx. 331143 rows)
  `employees`.`titles` (approx. 442664 rows)
  `employees`.`salaries` (approx. 2838426 rows)
Will automatically choose the method to update foreign keys.
Altering `employees`.`employees`...
Creating new table...
Created new table employees._employees_new OK.
Altering new table...
Altered `employees`.`_employees_new` OK.
2024-05-28T19:31:54 Creating triggers...
2024-05-28T19:31:54 Created triggers OK.
2024-05-28T19:31:54 Copying approximately 291671 rows...
2024-05-28T19:32:06 Copied rows OK.
2024-05-28T19:32:06 Max rows for the rebuild_constraints method: 54152
Determining the method to update foreign keys...
2024-05-28T19:32:06   `employees`.`dept_manager`: 24 rows; can use rebuild_constraints
2024-05-28T19:32:06   `employees`.`dept_emp`: too many rows: 331143; must use drop_swap
--alter-foreign-keys-method=drop_swap doesn't work with MySQL 8.0+
See https://bugs.mysql.com/bug.php?id=89441
2024-05-28T19:32:06 Dropping triggers...
2024-05-28T19:32:06 Dropped triggers OK.
Not dropping the new table `employees`.`_employees_new` because --swap-tables failed.  To drop the new table, execute:
DROP TABLE IF EXISTS `employees`.`_employees_new`;
`employees`.`employees` was not altered.
```

### pt-pmp

linux 上のスタックトレースを表示

````
root@masami-L /e/percona-toolkit# pt-pmp
Tue May 28 07:55:42 PM JST 2024
29      ../sysdeps/unix/sysv/linux/poll.c: そのようなファイルやディレクトリはありません.
      9 syscall,io_getevents(libaio.so.1),LinuxAIOHandler::collect,LinuxAIOHandler::poll,os_aio_handler,fil_aio_wait,::??,::??,libstdc++::??(libstdc++.so.6),start_thread,clone3
      3 __futex_abstimed_wait_common64,__futex_abstimed_wait_common,__GI___futex_abstimed_wait_cancelable64,__pthread_cond_wait_common,___pthread_cond_wait,os_event::wait_low,srv_worker_thread,::??,libstdc++::??(libstdc++.so.6),start_thread,clone3
      3 __futex_abstimed_wait_common64,__futex_abstimed_wait_common,__GI___futex_abstimed_wait_cancelable64,__pthread_cond_wait_common,___pthread_cond_wait,Per_thread_connection_handler::block_until_new_connection,::??,::??,start_thread,clone3
      1 __futex_abstimed_wait_common64,__futex_abstimed_wait_common,__GI___futex_abstimed_wait_cancelable64,__pthread_cond_wait_common,___pthread_cond_wait,os_event::wait_low,srv_purge_coordinator_thread,::??,libstdc++::??(libstdc++.so.6),start_thread,clone3
      1 __futex_abstimed_wait_common64,__futex_abstimed_wait_common,__GI___futex_abstimed_wait_cancelable64,__pthread_cond_wait_common,___pthread_cond_wait,os_event::wait_low,buf_resize_thread,::??,libstdc++::??(libstdc++.so.6),start_thread,clone3
      1 __futex_abstimed_wait_common64,__futex_abstimed_wait_common,__GI___futex_abstimed_wait_cancelable64,__pthread_cond_wait_common,___pthread_cond_wait,os_event::wait_low,buf_dump_thread,::??,libstdc++::??(libstdc++.so.6),start_thread,clone3
      1 __futex_abstimed_wait_common64,__futex_abstimed_wait_common,__GI___futex_abstimed_wait_cancelable64,__pthread_cond_wait_common,___pthread_cond_wait,Event_queue::cond_wait,Event_queue::get_top_for_execution_if_time,Event_scheduler::run,::??,::??,start_thread,clone3
      1 __futex_abstimed_wait_common64,__futex_abstimed_wait_common,__GI___futex_abstimed_wait_cancelable64,__pthread_cond_wait_common,___pthread_cond_wait,::??,::??,start_thread,clone3
      ・・・
      ```
````

### pt-query-digest

Slow クエリログの集計結果を表示します

```
root@masami-L /e/percona-toolkit [1]# pt-query-digest /var/log/mysql/mysql-slow
.log

# 210ms user time, 40ms system time, 38.07M rss, 48.34M vsz
# Current date: Tue May 28 20:37:40 2024
# Hostname: masami-L
# Files: /var/log/mysql/mysql-slow.log
# Overall: 4 total, 4 unique, 0.10 QPS, 0.00x concurrency ________________
# Time range: 2024-05-28T11:36:54 to 2024-05-28T11:37:35
# Attribute          total     min     max     avg     95%  stddev  median
# ============     ======= ======= ======= ======= ======= ======= =======
# Exec time           44ms   152us    37ms    11ms    36ms    15ms    20ms
# Lock time           25us       0    10us     6us     9us     3us     8us
# Rows sent             22       1      10    5.50    9.83    4.42    9.83
# Rows examine          79       0      41   19.75   40.45   19.09   38.57
# Query size            87      11      32   21.75   31.70    9.07   30.22

# Profile
# Rank Query ID                          Response time Calls R/Call V/M
# ==== ================================= ============= ===== ====== =====
#    1 0x0EB8D5EC7325DEA5CC075652701D...  0.0374 85.5%     1 0.0374  0.00 SELECT employees
#    2 0x751417D45B8E80EE5CBA2034458B...  0.0039  8.9%     1 0.0039  0.00 SHOW DATABASES
#    3 0x489B4CEB2F4301A7132628303F99...  0.0023  5.3%     1 0.0023  0.00 SHOW TABLES
# MISC 0xMISC                             0.0002  0.3%     1 0.0002   0.0 <1 ITEMS>

# Query 1: 0 QPS, 0x concurrency, ID 0x0EB8D5EC7325DEA5CC075652701DF307 at byte 2712
# This item is included in the report because it matches --limit.
# Scores: V/M = 0.00
# Time range: all events occurred at 2024-05-28T11:37:35
# Attribute    pct   total     min     max     avg     95%  stddev  median
# ============ === ======= ======= ======= ======= ======= ======= =======
# Count         25       1
# Exec time     85    37ms    37ms    37ms    37ms    37ms       0    37ms
# Lock time     40    10us    10us    10us    10us    10us       0    10us
# Rows sent      4       1       1       1       1       1       0       1
# Rows examine   0       0       0       0       0       0       0       0
# Query size    34      30      30      30      30      30       0      30
# String:
# Databases    employees
# Hosts        localhost
# Users        root
# Query_time distribution
#   1us
#  10us
# 100us
#   1ms
#  10ms  ################################################################
# 100ms
#    1s
#  10s+
# Tables
#    SHOW TABLE STATUS FROM `employees` LIKE 'employees'\G
#    SHOW CREATE TABLE `employees`.`employees`\G
# EXPLAIN /*!50100 PARTITIONS*/
select count(*) from employees\G

# Query 2: 0 QPS, 0x concurrency, ID 0x751417D45B8E80EE5CBA2034458B5BC9 at byte 0
# This item is included in the report because it matches --limit.
# Scores: V/M = 0.00
# Time range: all events occurred at 2024-05-28T11:36:54
# Attribute    pct   total     min     max     avg     95%  stddev  median
# ============ === ======= ======= ======= ======= ======= ======= =======
# Count         25       1
# Exec time      8     4ms     4ms     4ms     4ms     4ms       0     4ms
# Lock time     32     8us     8us     8us     8us     8us       0     8us
# Rows sent     45      10      10      10      10      10       0      10
# Rows examine  51      41      41      41      41      41       0      41
# Query size    16      14      14      14      14      14       0      14
# String:
# Databases    employees
# Hosts        localhost
# Users        root
# Query_time distribution
#   1us
#  10us
# 100us
#   1ms  ################################################################
#  10ms
# 100ms
#    1s
#  10s+
show databases\G

# Query 3: 0 QPS, 0x concurrency, ID 0x489B4CEB2F4301A7132628303F99240D at byte 399
# This item is included in the report because it matches --limit.
# Scores: V/M = 0.00
# Time range: all events occurred at 2024-05-28T11:36:54
# Attribute    pct   total     min     max     avg     95%  stddev  median
# ============ === ======= ======= ======= ======= ======= ======= =======
# Count         25       1
# Exec time      5     2ms     2ms     2ms     2ms     2ms       0     2ms
# Lock time     28     7us     7us     7us     7us     7us       0     7us
# Rows sent     45      10      10      10      10      10       0      10
# Rows examine  46      37      37      37      37      37       0      37
# Query size    12      11      11      11      11      11       0      11
# String:
# Databases    employees
# Hosts        localhost
# Users        root
# Query_time distribution
#   1us
#  10us
# 100us
#   1ms  ################################################################
#  10ms
# 100ms
#    1s
#  10s+
show tables\G
```

### pt-show-grant

全ユーザの GRANT 情報を出力します

```
root@masami-L /e/percona-toolkit# pt-show-grants
-- Grants dumped by pt-show-grants
-- Dumped from server Localhost via UNIX socket, MySQL 8.0.36-0ubuntu0.22.04.1 at 2024-05-28 20:42:12
-- Grants for 'debian-sys-maint'@'localhost'
CREATE USER IF NOT EXISTS `debian-sys-maint`@`localhost`;
ALTER USER `debian-sys-maint`@`localhost` IDENTIFIED WITH 'caching_sha2_password' AS '$A$005$
              EV+MQB
                    /B^K\nJJ2y\'6A7n/I32JSHUb/YmmAvE/tfToHyot181LeaaBcxhxj2' REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT UNLOCK PASSWORD HISTORY DEFAULT PASSWORD REUSE INTERVAL DEFAULT PASSWORD REQUIRE CURRENT DEFAULT;
GRANT ALTER, ALTER ROUTINE, CREATE, CREATE ROLE, CREATE ROUTINE, CREATE TABLESPACE, CREATE TEMPORARY TABLES, CREATE USER, CREATE VIEW, DELETE, DROP, DROP ROLE, EVENT, EXECUTE, FILE, INDEX, INSERT, LOCK TABLES, PROCESS, REFERENCES, RELOAD, REPLICATION CLIENT, REPLICATION SLAVE, SELECT, SHOW DATABASES, SHOW VIEW, SHUTDOWN, SUPER, TRIGGER, UPDATE ON *.* TO `debian-sys-maint`@`localhost` WITH GRANT OPTION;
GRANT APPLICATION_PASSWORD_ADMIN,AUDIT_ADMIN,AUTHENTICATION_POLICY_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN,BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,FLUSH_OPTIMIZER_COSTS,FLUSH_STATUS,FLUSH_TABLES,FLUSH_USER_RESOURCES,GROUP_REPLICATION_ADMIN,GROUP_REPLICATION_STREAM,INNODB_REDO_LOG_ARCHIVE,INNODB_REDO_LOG_ENABLE,PASSWORDLESS_USER_ADMIN,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_APPLIER,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SENSITIVE_VARIABLES_OBSERVER,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN,SET_USER_ID,SHOW_ROUTINE,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,TELEMETRY_LOG_ADMIN,XA_RECOVER_ADMIN ON *.* TO `debian-sys-maint`@`localhost` WITH GRANT OPTION;
-- Grants for 'mysql.infoschema'@'localhost'
CREATE USER IF NOT EXISTS `mysql.infoschema`@`localhost`;
ALTER USER `mysql.infoschema`@`localhost` IDENTIFIED WITH 'caching_sha2_password' AS '$A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED' REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT LOCK PASSWORD HISTORY DEFAULT PASSWORD REUSE INTERVAL DEFAULT PASSWORD REQUIRE CURRENT DEFAULT;
GRANT AUDIT_ABORT_EXEMPT,FIREWALL_EXEMPT,SYSTEM_USER ON *.* TO `mysql.infoschema`@`localhost`;
GRANT SELECT ON *.* TO `mysql.infoschema`@`localhost`;
-- Grants for 'mysql.session'@'localhost'
CREATE USER IF NOT EXISTS `mysql.session`@`localhost`;
ALTER USER `mysql.session`@`localhost` IDENTIFIED WITH 'caching_sha2_password' AS '$A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED' REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT LOCK PASSWORD HISTORY DEFAULT PASSWORD REUSE INTERVAL DEFAULT PASSWORD REQUIRE CURRENT DEFAULT;
GRANT AUDIT_ABORT_EXEMPT,AUTHENTICATION_POLICY_ADMIN,BACKUP_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,FIREWALL_EXEMPT,PERSIST_RO_VARIABLES_ADMIN,SESSION_VARIABLES_ADMIN,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN ON *.* TO `mysql.session`@`localhost`;
GRANT SELECT ON `mysql`.`user` TO `mysql.session`@`localhost`;
GRANT SELECT ON `performance_schema`.* TO `mysql.session`@`localhost`;
GRANT SHUTDOWN, SUPER ON *.* TO `mysql.session`@`localhost`;
-- Grants for 'mysql.sys'@'localhost'
CREATE USER IF NOT EXISTS `mysql.sys`@`localhost`;
ALTER USER `mysql.sys`@`localhost` IDENTIFIED WITH 'caching_sha2_password' AS '$A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED' REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT LOCK PASSWORD HISTORY DEFAULT PASSWORD REUSE INTERVAL DEFAULT PASSWORD REQUIRE CURRENT DEFAULT;
GRANT AUDIT_ABORT_EXEMPT,FIREWALL_EXEMPT,SYSTEM_USER ON *.* TO `mysql.sys`@`localhost`;
GRANT SELECT ON `sys`.`sys_config` TO `mysql.sys`@`localhost`;
GRANT TRIGGER ON `sys`.* TO `mysql.sys`@`localhost`;
GRANT USAGE ON *.* TO `mysql.sys`@`localhost`;
-- Grants for 'root'@'localhost'
CREATE USER IF NOT EXISTS `root`@`localhost`;
ALTER USER `root`@`localhost` IDENTIFIED WITH 'auth_socket' REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT UNLOCK PASSWORD HISTORY DEFAULT PASSWORD REUSE INTERVAL DEFAULT PASSWORD REQUIRE CURRENT DEFAULT;
GRANT ALTER, ALTER ROUTINE, CREATE, CREATE ROLE, CREATE ROUTINE, CREATE TABLESPACE, CREATE TEMPORARY TABLES, CREATE USER, CREATE VIEW, DELETE, DROP, DROP ROLE, EVENT, EXECUTE, FILE, INDEX, INSERT, LOCK TABLES, PROCESS, REFERENCES, RELOAD, REPLICATION CLIENT, REPLICATION SLAVE, SELECT, SHOW DATABASES, SHOW VIEW, SHUTDOWN, SUPER, TRIGGER, UPDATE ON *.* TO `root`@`localhost` WITH GRANT OPTION;
GRANT APPLICATION_PASSWORD_ADMIN,AUDIT_ABORT_EXEMPT,AUDIT_ADMIN,AUTHENTICATION_POLICY_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN,BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,FIREWALL_EXEMPT,FLUSH_OPTIMIZER_COSTS,FLUSH_STATUS,FLUSH_TABLES,FLUSH_USER_RESOURCES,GROUP_REPLICATION_ADMIN,GROUP_REPLICATION_STREAM,INNODB_REDO_LOG_ARCHIVE,INNODB_REDO_LOG_ENABLE,PASSWORDLESS_USER_ADMIN,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_APPLIER,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SENSITIVE_VARIABLES_OBSERVER,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN,SET_USER_ID,SHOW_ROUTINE,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,TELEMETRY_LOG_ADMIN,XA_RECOVER_ADMIN ON *.* TO `root`@`localhost` WITH GRANT OPTION;
GRANT PROXY ON ``@`` TO `root`@`localhost` WITH GRANT OPTION;
```

### pt-stalk

指定した条件に達した時点の`netstat`や'vmstat'等の情報を出力します

MySQL への接続数が１以上になると起動

```
root@masami-L /e/percona-toolkit [1]# pt-stalk --variable=Threads_connected --t
hreshold=1
mysql: [Warning] Using a password on the command line interface can be insecure.
2024_05_28_21_10_36 Starting /usr/bin/pt-stalk --function=status --variable=Threads_connected --threshold=1 --match= --cycles=5 --interval=1 --iterations= --run-time=30 --sleep=300 --dest=/var/lib/pt-stalk --prefix= --notify-by-email= --log=/var/log/pt-stalk.log --pid=/var/run/pt-stalk.pid --plugin=
mysqladmin: [Warning] Using a password on the command line interface can be insecure.
2024_05_28_21_10_36 Check results: status(Threads_connected)=2, matched=yes, cycles_true=1
mysqladmin: [Warning] Using a password on the command line interface can be insecure.
2024_05_28_21_10_37 Check results: status(Threads_connected)=2, matched=yes, cycles_true=2
mysqladmin: [Warning] Using a password on the command line interface can be insecure.
2024_05_28_21_10_38 Check results: status(Threads_connected)=2, matched=yes, cycles_true=3
mysqladmin: [Warning] Using a password on the command line interface can be insecure.
2024_05_28_21_10_39 Check results: status(Threads_connected)=2, matched=yes, cycles_true=4
mysqladmin: [Warning] Using a password on the command line interface can be insecure.
2024_05_28_21_10_40 Check results: status(Threads_connected)=2, matched=yes, cycles_true=5
```

パスワードは設定ファイルではなく、コマンドラインで渡さないと警告がたくさん出ます。

定刻置きにファイルが作成されます

```
root@masami-L /# cd var/lib/pt-stalk/
root@masami-L /v/l/pt-stalk# ls
2024_05_28_21_10_40-df
2024_05_28_21_10_40-disk-space
2024_05_28_21_10_40-diskstats
2024_05_28_21_10_40-hostname
2024_05_28_21_10_40-innodbstatus1
2024_05_28_21_10_40-innodbstatus2
2024_05_28_21_10_40-interrupts
2024_05_28_21_10_40-iostat
2024_05_28_21_10_40-iostat-overall
2024_05_28_21_10_40-log_error
2024_05_28_21_10_40-lsof
2024_05_28_21_10_40-meminfo
2024_05_28_21_10_40-mpstat
2024_05_28_21_10_40-mpstat-overall
2024_05_28_21_10_40-mutex-status1
2024_05_28_21_10_40-mutex-status2
2024_05_28_21_10_40-mysqladmin
2024_05_28_21_10_40-netstat
2024_05_28_21_10_40-netstat_s
2024_05_28_21_10_40-opentables1
2024_05_28_21_10_40-opentables2
2024_05_28_21_10_40-output
2024_05_28_21_10_40-pmap
2024_05_28_21_10_40-processlist
2024_05_28_21_10_40-procstat
2024_05_28_21_10_40-procvmstat
2024_05_28_21_10_40-ps
2024_05_28_21_10_40-ps-locks-transactions
2024_05_28_21_10_40-slabinfo
2024_05_28_21_10_40-slave-status
2024_05_28_21_10_40-sysctl
2024_05_28_21_10_40-top
2024_05_28_21_10_40-trigger
```

```
root@masami-L /v/l/pt-stalk# cat 2024_05_28_21_10_40-df
TS 1716898242.004942577 2024-05-28 21:10:42
Filesystem     1K-blocks     Used Available Use% Mounted on
tmpfs             804608     3156    801452   1% /run
/dev/sda5       85611640 22207916  59008872  28% /
tmpfs            4023040        0   4023040   0% /dev/shm
tmpfs               5120        4      5116   1% /run/lock
/dev/sda3         524252     6220    518032   2% /boot/efi
tmpfs             804608       80    804528   1% /run/user/128
tmpfs             804608       68    804540   1% /run/user/1000
TS 1716898243.004188165 2024-05-28 21:10:43
Filesystem     1K-blocks     Used Available Use% Mounted on
tmpfs             804608     3156    801452   1% /run
/dev/sda5       85611640 22208208  59008580  28% /
tmpfs            4023040        0   4023040   0% /dev/shm
tmpfs               5120        4      5116   1% /run/lock
/dev/sda3         524252     6220    518032   2% /boot/efi
tmpfs             804608       80    804528   1% /run/user/128
tmpfs             804608       68    804540   1% /run/user/1000
```

### pt-sift

pt-stalk によって作成されたファイルを閲覧します

```
root@masami-L /v/l/pt-stalk# pt-sift /var/lib/pt-stalk/

  2024_05_28_21_10_40  2024_05_28_21_15_44  2024_05_28_21_20_48

Select a timestamp from the list [2024_05_28_21_20_48]
======== masami-L at 2024_05_28_21_20_48 DEFAULT (3 of 3) ========
--diskstats--



--vmstat--
 r b swpd    free  buff   cache si so bi  bo  in   cs us sy id wa st
13 0    0 5484728 80500 1249760  0  0 21 122 155  301  7  2 91  0  0
 2 0    0 5470976 80556 1258644  0  0  0  29 569 1238  2  3 94  0  0
wa 0% . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
--innodb--
    txns: 5xnot (0s)
    0 queries inside InnoDB, 0 queries in queue
    Main thread: rocess ID=17876, Main thread ID=140304292243008 , state=sleeping, pending reads 0, writes 0, flush 0
    Log: lsn = 809189702, chkp = 809189702, chkp age = 0
    Threads are waiting at:
    Threads are waiting on:
--processlist--
    State
     89
     30  init
     30  Waiting on empty queue
      8  Receiving from client
      1  starting
    Command
     90  Sleep
     31  Query
     30  Daemon
      8  Connect
--stack traces--
    No stack trace file exists
--oprofile--
    No opreport file exists
```

### pg-table-usage

Slow クエリログからクエリを読み込み、どのテーブルへアクセスしたものかを分析表示します

```
root@masami-L /v/l/pt-stalk# pt-table-usage /var/log/mysql/mysql-slow.log
Query_id: 0xE3A3649C5FAC418D.1
SELECT DUAL

Query_id: 0xCC075652701DF307.1
SELECT employees

Query_id: 0xD3F15165B8904806.1
SELECT DUAL

Query_id: 0x7047E58B2A5815B3.1
SELECT DUAL

Query_id: 0xAFF8F82F338E4C33.1
SELECT DUAL

Query_id: 0x1926E13FD2203384.1
SELECT mysql.user
WHERE DUAL
WHERE mysql.user
```

### pt-upgrade

クエリ実行時間の違いや、エラーの有無の比較結果を出力します  
異なるサーバ間で使う以外にもパラメータ調整などにも使用できます

### pt-variable-advisor

MySQL 変数を分析して、問題の可能性がある設定について表示します

```
root@masami-L /# pt-variable-advisor  localhost
# WARN delay_key_write: MyISAM index blocks are never flushed until necessary.

# NOTE max_binlog_size: The max_binlog_size is smaller than the default of 1GB.

# NOTE sort_buffer_size-1: The sort_buffer_size variable should generally be left at its default unless an expert determines it is necessary to change it.

# WARN expire_logs_days: Binary logs are enabled, but automatic purging is not enabled.

# NOTE innodb_data_file_path: Auto-extending InnoDB files can consume a lot of disk space that is very difficult to reclaim later.

# NOTE innodb_flush_method: Most production database servers that use InnoDB should set innodb_flush_method to O_DIRECT to avoid double-buffering, unless the I/O system is very low performance.
```

### pt-visual-explain

explain 文をツリー構造で出力します

```
root@masami-L /# mysql -u root -e "explain select * from employees where emp_no
> 20000 and emp_no < 30000" employees > /tmp/pt-visual-explain_result.txt
root@masami-L /# pt-visual-explain  /tmp/pt-visual-explain_result.txt
Filter with WHERE
+- Bookmark lookup
   +- Table
   |  table          employees
   |  possible_keys  PRIMARY
   +- Index range scan
      key            employees->PRIMARY
      possible_keys  PRIMARY
      key_len        4
      rows           19912
```

パイプで渡す方がファイル出力がなくて便利かもしれません

```
root@masami-L /# mysql -u root -e "explain select * from employees where emp_no
> 20000 and emp_no < 30000" employees | pt-visual-explain
Filter with WHERE
+- Bookmark lookup
   +- Table
   |  table          employees
   |  possible_keys  PRIMARY
   +- Index range scan
      key            employees->PRIMARY
      possible_keys  PRIMARY
      key_len        4
      rows           19912
```

※レプリケーションツールは試していないので割愛
