## 4.6.8 mysqlbinlog — バイナリログファイルを処理するためのユーティリティー
サーバーのバイナリログは、データベースの内容に対する変更を記述する「イベント」を含むファイルで構成されます。 サーバーはこれらのファイルをバイナリ形式で書き出します。 内容をテキスト形式で表示するには、mysqlbinlog ユーティリティーを使用します。

※PostgreSQLでいうWALみたいなものっぽい  
 mysqldumpでベースバックアップを取り、mysqlbinlogで差分バックアップを取る。

```
C:\Windows\System32>mysqlbinlog "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000001"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:04 server id 1  end_log_pos 126 CRC32 0x0f09f8f9  Start: binlog v 4, server v 8.2.0 created 231123  8:13:04 at startup
ROLLBACK/*!*/;
BINLOG '
AIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAfn4CQ8=
'/*!*/;
# at 126
#231123  8:13:04 server id 1  end_log_pos 157 CRC32 0x74c8e500  Previous-GTIDs
# [empty]
# at 157
#231123  8:13:09 server id 1  end_log_pos 180 CRC32 0x374ec549  Stop
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```

verboseを使うと対応するクエリがコメントで見れる
```
C:\Windows\System32>mysqlbinlog -vv "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000008"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123 17:02:03 server id 1  end_log_pos 126 CRC32 0x622c3b1c  Start: binlog v 4, server v 8.2.0 created 231123 17:02:03
BINLOG '
+wZfZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAARw7LGI=
'/*!*/;
# at 126
#231123 17:02:03 server id 1  end_log_pos 157 CRC32 0xb4385d34  Previous-GTIDs
# [empty]
# at 157
#231123 17:05:19 server id 1  end_log_pos 180 CRC32 0xd0b81fbb  Stop
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --base64-output:バイナリログのエントリを base-64 エンコードで出力
```
C:\Windows\System32>mysqlbinlog --base64-output=AUTO "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000001"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:04 server id 1  end_log_pos 126 CRC32 0x0f09f8f9  Start: binlog v 4, server v 8.2.0 created 231123  8:13:04 at startup
ROLLBACK/*!*/;
BINLOG '
AIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAfn4CQ8=
'/*!*/;
# at 126
#231123  8:13:04 server id 1  end_log_pos 157 CRC32 0x74c8e500  Previous-GTIDs
# [empty]
# at 157
#231123  8:13:09 server id 1  end_log_pos 180 CRC32 0x374ec549  Stop
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --database:このデータベースのみのエントリをリスト
```
C:\Windows\System32>mysqlbinlog --database=sakila "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000001"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:04 server id 1  end_log_pos 126 CRC32 0x0f09f8f9  Start: binlog v 4, server v 8.2.0 created 231123  8:13:04 at startup
ROLLBACK/*!*/;
BINLOG '
AIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAfn4CQ8=
'/*!*/;
WARNING: The option --database has been used. It may filter parts of transactions, but will include the GTIDs in any case. If you want to exclude or include transactions, you should use the options --exclude-gtids or --include-gtids, respectively, instead.
# at 126
#231123  8:13:04 server id 1  end_log_pos 157 CRC32 0x74c8e500  Previous-GTIDs
# [empty]
# at 157
#231123  8:13:09 server id 1  end_log_pos 180 CRC32 0x374ec549  Stop
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --disable-log-bin:バイナリロギングを無効化
```
C:\Windows\System32>mysqlbinlog --disable-log-bin "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000001"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!32316 SET @OLD_SQL_LOG_BIN=@@SQL_LOG_BIN, SQL_LOG_BIN=0*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:04 server id 1  end_log_pos 126 CRC32 0x0f09f8f9  Start: binlog v 4, server v 8.2.0 created 231123  8:13:04 at startup
ROLLBACK/*!*/;
BINLOG '
AIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAfn4CQ8=
'/*!*/;
# at 126
#231123  8:13:04 server id 1  end_log_pos 157 CRC32 0x74c8e500  Previous-GTIDs
# [empty]
# at 157
#231123  8:13:09 server id 1  end_log_pos 180 CRC32 0x374ec549  Stop
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!32316 SET SQL_LOG_BIN=@OLD_SQL_LOG_BIN*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --hexdump:コメント内にログの 16 進ダンプを表示
```
C:\Windows\System32>mysqlbinlog --hexdump "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000001"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:04 server id 1  end_log_pos 126 CRC32 0x0f09f8f9
# Position  Timestamp   Type   Source ID        Size      Source Pos    Flags
# 00000004 00 8b 5e 65   0f   01 00 00 00   7a 00 00 00   7e 00 00 00   00 00
# 00000017 04 00 38 2e 32 2e 30 00  00 00 00 00 00 00 00 00 |..8.2.0.........|
# 00000027 00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
# 00000037 00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
# 00000047 00 00 00 00 00 8b 5e 65  13 00 0d 00 08 00 00 00 |.......e........|
# 00000057 00 04 00 04 00 00 00 62  00 04 1a 08 00 00 00 08 |.......b........|
# 00000067 08 08 02 00 00 00 0a 0a  0a 2a 2a 00 12 34 00 0a |.............4..|
# 00000077 28 00 01 f9 f8 09 0f                             |.......|
#       Start: binlog v 4, server v 8.2.0 created 231123  8:13:04 at startup
ROLLBACK/*!*/;
BINLOG '
AIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAfn4CQ8=
'/*!*/;
# at 126
#231123  8:13:04 server id 1  end_log_pos 157 CRC32 0x74c8e500
# Position  Timestamp   Type   Source ID        Size      Source Pos    Flags
# 0000007e 00 8b 5e 65   23   01 00 00 00   1f 00 00 00   9d 00 00 00   80 00
# 00000091 00 00 00 00 00 00 00 00  00 e5 c8 74             |...........t|
#       Previous-GTIDs
# [empty]
# at 157
#231123  8:13:09 server id 1  end_log_pos 180 CRC32 0x374ec549
# Position  Timestamp   Type   Source ID        Size      Source Pos    Flags
# 0000009d 05 8b 5e 65   03   01 00 00 00   17 00 00 00   b4 00 00 00   00 00
# 000000b0 49 c5 4e 37                                      |I.N7|
#       Stop
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --raw:イベントを生の (バイナリ) 形式で出力ファイルに書き込み		
* --read-from-remote-master:バイナリログをローカルログファイルからではなく MySQL マスターから読み取り
```
C:\Windows\System32>mysqlbinlog --read-from-remote-server --host=localhost -u root --raw "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000008" -p
Enter password: **********
```
* --offset:ログの最初の N エントリをスキップ
```
C:\Windows\System32>mysqlbinlog --offset=9183342 "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000002"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:12 server id 1  end_log_pos 126 CRC32 0xbae7499a  Start: binlog v 4, server v 8.2.0 created 231123  8:13:12 at startup
ROLLBACK/*!*/;
BINLOG '
CIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAIi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAZpJ57o=
'/*!*/;
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;

C:\Windows\System32>mysqlbinlog --offset=9183344 "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000002"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:12 server id 1  end_log_pos 126 CRC32 0xbae7499a  Start: binlog v 4, server v 8.2.0 created 231123  8:13:12 at startup
ROLLBACK/*!*/;
BINLOG '
CIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAIi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAZpJ57o=
'/*!*/;
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --print-table-metadata:テーブルメタデータの印刷
```
C:\Windows\System32>mysqlbinlog --offset=9183344 --print-table-metadata "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000002"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:12 server id 1  end_log_pos 126 CRC32 0xbae7499a  Start: binlog v 4, server v 8.2.0 created 231123  8:13:12 at startup
ROLLBACK/*!*/;
BINLOG '
CIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAIi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAZpJ57o=
'/*!*/;
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```

```
C:\Windows\System32>mysqlbinlog --offset=9183344 --require-row-format "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000002"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
/*!80019 SET @@SESSION.REQUIRE_ROW_FORMAT=1*/;

DELIMITER /*!*/;
# at 4
#231123  8:13:12 server id 1  end_log_pos 126 CRC32 0xbae7499a  Start: binlog v 4, server v 8.2.0 created 231123  8:13:12 at startup
ROLLBACK/*!*/;
BINLOG '
CIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAIi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAZpJ57o=
'/*!*/;
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --short-form:ログに含まれるステートメントのみを表示
```
C:\Windows\System32>mysqlbinlog --short-form "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000003"
WARNING: --short-form is deprecated and will be removed in a future version

# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
ROLLBACK/*!*/;
# [empty]
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --start-position:引数以上の位置を持つ最初のイベントからバイナリログをデコード
```
C:\Windows\System32>mysqlbinlog --start-datetime="2023-11-26 00:00:00" "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000006"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123 11:51:26 server id 1  end_log_pos 126 CRC32 0xa7051fe3  Start: binlog v 4, server v 8.2.0 created 231123 11:51:26 at startup
ROLLBACK/*!*/;
BINLOG '
Lr5eZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAuvl5lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAeMfBac=
'/*!*/;
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --stop-datetime:タイムスタンプが datetime 引数と同じかそれより大きい最初のイベントでバイナリログの読み取りを停止
```
C:\Windows\System32>mysqlbinlog --start-datetime="2023-11-23 12:00:00" --stop-datetime="2023-11-24 22:00:00" "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000006"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123 11:51:26 server id 1  end_log_pos 126 CRC32 0xa7051fe3  Start: binlog v 4, server v 8.2.0 created 231123 11:51:26 at startup
ROLLBACK/*!*/;
BINLOG '
Lr5eZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAuvl5lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAeMfBac=
'/*!*/;
# at 8854642
#231123 15:52:38 server id 1  end_log_pos 8854719 CRC32 0x0216e98d      Anonymous_GTID  last_committed=13205    sequence_number=13206   rbr_only=no     original_committed_timestamp=1700722358486641   immediate_commit_timestamp=1700722358486641     transaction_length=211
# original_commit_timestamp=1700722358486641 (2023-11-23 15:52:38.486641 東京 (標準時))
# immediate_commit_timestamp=1700722358486641 (2023-11-23 15:52:38.486641 東京 (標準時))
/*!80001 SET @@session.original_commit_timestamp=1700722358486641*//*!*/;
/*!80014 SET @@session.original_server_version=80200*//*!*/;
/*!80014 SET @@session.immediate_server_version=80200*//*!*/;
SET @@SESSION.GTID_NEXT= 'ANONYMOUS'/*!*/;
# at 8854719
#231123 15:52:38 server id 1  end_log_pos 8854853 CRC32 0x8996bbd7      Query   thread_id=521   exec_time=0     error_code=0    Xid = 16328
SET TIMESTAMP=1700722358/*!*/;
SET @@session.pseudo_thread_id=521/*!*/;
SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=0, @@session.unique_checks=1, @@session.autocommit=1/*!*/;
SET @@session.sql_mode=1168113696/*!*/;
SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/;
/*!\C cp932 *//*!*/;
SET @@session.character_set_client=95,@@session.collation_connection=95,@@session.collation_server=255/*!*/;
SET @@session.lc_time_names=0/*!*/;
SET @@session.collation_database=DEFAULT/*!*/;
/*!80011 SET @@session.default_collation_for_utf8mb4=255*//*!*/;
/*!80016 SET @@session.default_table_encryption=0*//*!*/;
create database `db_name-test`
/*!*/;
# at 8854853
#231123 15:54:23 server id 1  end_log_pos 8854930 CRC32 0x00386779      Anonymous_GTID  last_committed=13206    sequence_number=13207   rbr_only=no     original_committed_timestamp=1700722463677083   immediate_commit_timestamp=1700722463677083     transaction_length=207
# original_commit_timestamp=1700722463677083 (2023-11-23 15:54:23.677083 東京 (標準時))
# immediate_commit_timestamp=1700722463677083 (2023-11-23 15:54:23.677083 東京 (標準時))
/*!80001 SET @@session.original_commit_timestamp=1700722463677083*//*!*/;
/*!80014 SET @@session.original_server_version=80200*//*!*/;
/*!80014 SET @@session.immediate_server_version=80200*//*!*/;
SET @@SESSION.GTID_NEXT= 'ANONYMOUS'/*!*/;
# at 8854930
#231123 15:54:23 server id 1  end_log_pos 8855060 CRC32 0xa2596586      Query   thread_id=527   exec_time=0     error_code=0    Xid = 16368
SET TIMESTAMP=1700722463/*!*/;
/*!\C utf8mb4 *//*!*/;
SET @@session.character_set_client=255,@@session.collation_connection=255,@@session.collation_server=255/*!*/;
DROP DATABASE `db_name-test`
/*!*/;
# at 8855060
#231123 15:54:44 server id 1  end_log_pos 8855137 CRC32 0x8abc7c3c      Anonymous_GTID  last_committed=13207    sequence_number=13208   rbr_only=no     original_committed_timestamp=1700722484020801   immediate_commit_timestamp=1700722484020801     transaction_length=211
# original_commit_timestamp=1700722484020801 (2023-11-23 15:54:44.020801 東京 (標準時))
# immediate_commit_timestamp=1700722484020801 (2023-11-23 15:54:44.020801 東京 (標準時))
/*!80001 SET @@session.original_commit_timestamp=1700722484020801*//*!*/;
/*!80014 SET @@session.original_server_version=80200*//*!*/;
/*!80014 SET @@session.immediate_server_version=80200*//*!*/;
SET @@SESSION.GTID_NEXT= 'ANONYMOUS'/*!*/;
# at 8855137
#231123 15:54:44 server id 1  end_log_pos 8855271 CRC32 0x87ee2aa4      Query   thread_id=528   exec_time=0     error_code=0    Xid = 16370
SET TIMESTAMP=1700722484/*!*/;
/*!\C cp932 *//*!*/;
SET @@session.character_set_client=95,@@session.collation_connection=95,@@session.collation_server=255/*!*/;
/*!80016 SET @@session.default_table_encryption=0*//*!*/;
create database `db_name-test`
/*!*/;
# at 8855271
#231123 15:54:56 server id 1  end_log_pos 8855348 CRC32 0x72092e06      Anonymous_GTID  last_committed=13208    sequence_number=13209   rbr_only=no     original_committed_timestamp=1700722496006962   immediate_commit_timestamp=1700722496006962     transaction_length=207
# original_commit_timestamp=1700722496006962 (2023-11-23 15:54:56.006962 東京 (標準時))
# immediate_commit_timestamp=1700722496006962 (2023-11-23 15:54:56.006962 東京 (標準時))
/*!80001 SET @@session.original_commit_timestamp=1700722496006962*//*!*/;
/*!80014 SET @@session.original_server_version=80200*//*!*/;
/*!80014 SET @@session.immediate_server_version=80200*//*!*/;
SET @@SESSION.GTID_NEXT= 'ANONYMOUS'/*!*/;
# at 8855348
#231123 15:54:56 server id 1  end_log_pos 8855478 CRC32 0x12fd9c9c      Query   thread_id=529   exec_time=0     error_code=0    Xid = 16372
SET TIMESTAMP=1700722496/*!*/;
drop database `db_name-test`
/*!*/;
# at 8855478
#231123 16:41:41 server id 1  end_log_pos 8855533 CRC32 0x59b9e538      Rotate to DYNABOOK-B552-bin.000007  pos: 4
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --verify-binlog-checksum:バイナリログのチェックサムを検証
```
C:\Windows\System32>mysqlbinlog --verify-binlog-checksum "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000001"
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#231123  8:13:04 server id 1  end_log_pos 126 CRC32 0x0f09f8f9  Start: binlog v 4, server v 8.2.0 created 231123  8:13:04 at startup
ROLLBACK/*!*/;
BINLOG '
AIteZQ8BAAAAegAAAH4AAAAAAAQAOC4yLjAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAi15lEwANAAgAAAAABAAEAAAAYgAEGggAAAAICAgCAAAACgoKKioAEjQA
CigAAfn4CQ8=
'/*!*/;
# at 126
#231123  8:13:04 server id 1  end_log_pos 157 CRC32 0x74c8e500  Previous-GTIDs
# [empty]
# at 157
#231123  8:13:09 server id 1  end_log_pos 180 CRC32 0x374ec549  Stop
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```
* --stop-never:最後のバイナリログファイルの読み取り後、サーバーとの接続を維持
```
C:\Windows\System32>mysqlbinlog --read-from-remote-server --host=localhost -u root --raw --stop-never "C:\ProgramData\MySQL\MySQL Server 8.2\Data\DYNABOOK-B552-bin.000001" -p
Enter password: **********
```