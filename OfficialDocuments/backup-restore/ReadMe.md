## 7.3.1 バックアップポリシーの確立
バックアップは定期的にスケジュールする必要があります。 完全バックアップ (特定の時点でのデータのスナップショット) は、MySQL でいくつかのツールを使用して実行できます。

負荷が少ない日曜日の午後 1 時に、次のコマンドを使用して、すべてのデータベースのすべての InnoDB テーブルの完全バックアップを作成するとします。
```
C:\Users\masami>mysqldump --all-databases --master-data --single-transaction > backup_sunday_1_PM.sql -u root -p
WARNING: --master-data is deprecated and will be removed in a future version. Use --source-data instead.
Enter password: **********
```
MySQL バイナリログは、増分バックアップのセットを形成するため、リカバリに重要です。 完全バックアップの作成時にログをフラッシュさせる場合、その後作成されるバイナリログファイルには、バックアップ以降に行われたすべてのデータの変更が含まれます。 ここで、前述の mysqldump コマンドを少し修正して、完全バックアップの時点で MySQL バイナリログをフラッシュするようにし、ダンプファイルに新しい現在のバイナリログの名前が含まれるようにします。
```
C:\Users\masami>mysqldump --single-transaction --flush-logs --master-data=2 --all-databases > backup_sunday_1_PM.sql -u root -p
WARNING: --master-data is deprecated and will be removed in a future version. Use --source-data instead.
Enter password: **********
```
このコマンドを実行した時点でbinログは次のシーケンスに進む
例えば、DYNABOOK-B552-bin.000009がある状態で--flush-logs をすると、
次からbinログはDYNABOOK-B552-bin.000010に切り替わることで増分バックアップができる


MySQL バイナリログはディスク領域を占有します。 領域を解放するため、ときどきそれらをパージします。 これを実行する 1 つの方法は、完全バックアップを作成したときなど、必要なくなったバイナリログを削除することです。
```
C:\Users\masami>mysqldump --single-transaction --flush-logs --master-data=2 --all-databases --delete-master-logs > backup_sunday_1_PM.sql -u root -p
WARNING: --master-data is deprecated and will be removed in a future version. Use --source-data instead.
WARNING: --delete-master-logs is deprecated and will be removed in a future version. Use --delete-source-logs instead.
Enter password: **********
```
これにより、今あるバイナリログは削除される

## 7.3.2 リカバリへのバックアップの使用
今度は、水曜日の午前 8 時に、バックアップからのリカバリが必要な致命的な予期しない終了があるとします。 リカバリするには、まず存在する最後の完全バックアップ (日曜日の午後 1 時のもの) をリストアします。

Windowsで単純にバックアップデータをリストアしようとするとエラーになった  
文字コードをutf8に指定する必要がある
```
C:\Users\masami>mysql -u root -p < "C:\Users\masami\backup_sunday_1_PM.sql"
Enter password: **********
ERROR at line 1157: Unknown command '\ﾎ'.

C:\Users\masami>mysql -u root -p --default-character-set=utf8 < "C:\Users\masami\backup_sunday_1_PM.sql"
Enter password: **********
```
この時点で、データは日曜日の午後 1 時現在の状態にリストアされます。 それ以降に行われた変更をリストアするには、特定のタイミングまで増分バックアップを使用する場合、ログを指定します

```
C:\Users\masami>mysqlbinlog "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000012" | mysql -u root -p
Enter password: **********
```

クラッシュ直前まで復元する場合は、開始地点のログに...を付加します
```
mysqlbinlog "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000012" ... | mysql
```
## 7.4.1 mysqldump による SQL フォーマットでのデータのダンプ

デフォルトでは標準出力する
```
C:\Users\masami>mysqldump world -u root -p
Enter password: **********
-- MySQL dump 10.13  Distrib 8.2.0, for Win64 (x86_64)
--
-- Host: localhost    Database: world
-- ------------------------------------------------------
-- Server version       8.2.0

・・・

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2023-12-05 15:30:02
```
ファイルに出力する場合
```
mysqldump -u root -p  > dump.sql
```
すべてのデータベースをダンプするには、--all-databases オプションを付けて mysqldump を呼び出します。
```
C:\Users\masami>mysqldump --all-databases -u root -p  > dump.sql
Enter password: **********
```
特定のデータベースのみをダンプするには、コマンド行でそれらを指定し、--databases オプションを使用します
```
C:\Users\masami>mysqldump --databases world sakila -u root -p  > dump.sql
Enter password: **********
```
データベースから特定のテーブルのみをダンプ
```
C:\Users\masami>mysqldump world city -u root -p  > dump.sql
Enter password: **********
```

### 7.4.2 SQL フォーマットバックアップのリロード
--all-databases または --databases オプションを使用して、mysqldump によってダンプファイルが作成された場合、それには CREATE DATABASE および USE ステートメントが含まれ、データをロードするデフォルトのデータベースを指定する必要がありません。
```
C:\Users\masami>mysql -u root -p --default-character-set=utf8 < dump.sql
Enter password: **********
```

サーバアクセス後はsourceコマンドでリストアできる  
クライアントの文字コードによって失敗する  
PowerShellの場合、UTF-8にする
```
chcp 65001
```
```
mysql> source dump.sql
Query OK, 0 rows affected (0.00 sec)

・・・
mysql>
```
### 7.4.5.1 データベースのコピーの作成
```
C:\Windows\System32>mysqldump world > dump.sql -u root -p
Enter password: **********

C:\Windows\System32>mysqladmin create world-dummy -u root -p
Enter password: **********

C:\Windows\System32>mysql world-dummy < dump.sql -u root -p
Enter password: **********
```

### 7.4.5.4 テーブル定義と内容の個別のダンプ
--no-data オプションは mysqldump にテーブルデータをダンプしないように伝えるため、ダンプファイルにはテーブルを作成するステートメントのみが含まれます。 逆に、--no-create-info オプションは、ダンプファイルにテーブルデータのみが含まれるように、mysqldump に出力から CREATE ステートメントを抑制するように伝えます。
```
C:\Windows\System32>mysqldump --no-data world > dump-defs.sql -u root -p
Enter password: **********

C:\Windows\System32>mysqldump --no-data --routines --events world > dump-defs.sql -u root -p
Enter password: **********
```
### 7.5 Point-in-Time (増分) リカバリ
point-in-time リカバリとは、特定の時点までのデータ変更のリカバリのことです。

### 7.5.1 バイナリログを使用したポイントインタイムリカバリ
すべてのバイナリログファイルのリストを表示するには、次のステートメントを使用します。
```sql
mysql> SHOW BINARY LOGS;
+--------------------------+-----------+-----------+
| Log_name                 | File_size | Encrypted |
+--------------------------+-----------+-----------+
| DYNABOOK-B552-bin.000012 |   4217336 | No        |
| DYNABOOK-B552-bin.000013 |   6095107 | No        |
+--------------------------+-----------+-----------+
2 rows in set (0.01 sec)
```
現在のバイナリログファイルの名前を判断するには、次のステートメントを発行します。
```sql
mysql> SHOW MASTER STATUS;
+--------------------------+----------+--------------+------------------+-------------------+
| File                     | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+--------------------------+----------+--------------+------------------+-------------------+
| DYNABOOK-B552-bin.000014 |      157 |              |                  |                   |
+--------------------------+----------+--------------+------------------+-------------------+
1 row in set, 1 warning (0.01 sec)

```
バイナリログからイベントを適用すると、それらが表すデータ変更が再実行されます。
```
C:\Users\masami>mysqlbinlog "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000014" | mysql -u root -p
Enter password: **********
```
MySQL サーバーに適用するバイナリログが複数ある場合、安全な方法は、サーバーへの単一の接続を使用してそれらをすべて処理することです。
```
mysqlbinlog binlog.000001 binlog.000002 | mysql -u root -p
```
別の方法として、ログ全体を単一のファイルに書き込み、そのファイルを処理する方法があります
```
mysqlbinlog binlog.000001 >  /tmp/statements.sql
mysqlbinlog binlog.000002 >> /tmp/statements.sql
mysql -u root -p -e "source /tmp/statements.sql"
```

### 7.5.2 イベントの位置を使用したポイントインタイムリカバリ
mysqlbinlog ユーティリティを使用して特定期間の前後のログコンテンツを確認する
```
C:\Users\masami>mysqlbinlog --start-datetime="2023-12-05 08:00:00" --stop-datetime="2023-12-05 20:08:00" --verbose "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000014" -u root -p
Enter password: **********
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231205 20:07:34 server id 1  end_log_pos 126 CRC32 0x5c8b0570  Start: binlog v 4, server v 8.2.0 created 231205 20:07:34 at startup
# Warning: this binlog is either in use or was not closed properly.
ROLLBACK/*!*/;
BINLOG '
dgRvZQ8BAAAAegAAAH4AAAABAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAB2BG9lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAXAFi1w=
'/*!*/;
# at 126
#231205 20:07:34 server id 1  end_log_pos 157 CRC32 0x1a32890c  Previous-GTIDs
# [empty]
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
position4~126を適用
```
C:\Users\masami>mysqlbinlog --start-position=4 --stop-position=126 "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000014"  | mysql -u root -p
Enter password: **********
```
直近まで復旧する場合はstop-positionを指定しない