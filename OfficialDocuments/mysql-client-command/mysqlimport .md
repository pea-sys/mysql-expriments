# 4.5.5 mysqlimport — データインポートプログラム
mysqlimport クライアントは、LOAD DATA SQL ステートメントへのコマンドラインインタフェースを提供します。

### Setup
```
C:\Users\masami>mysqladmin create test -u root -p
Enter password: **********

C:\Users\masami>mysql -u root test -p
Enter password: **********

mysql> CREATE TABLE imptest(id INT, n VARCHAR(30));
Query OK, 0 rows affected (0.04 sec)

mysql> SET GLOBAL local_infile=on;
Query OK, 0 rows affected (0.00 sec)
```
### Import
* --local:クライアントホストから入力ファイルをローカルで読み込む
```
C:\Users\masami>mysqlimport -u root --local test "C:\Users\masami\source\repos\mysql-expriments\OfficialDocuments\mysql-client-command\imptest.txt" -p
Enter password: **********
test.imptest: Records: 2  Deleted: 0  Skipped: 0  Warnings: 4
```
* --use-threads:並列ファイルロードのスレッド数
```
C:\Users\masami>mysqlimport -u root --use-threads=4 --local test "C:\Users\masami\source\repos\mysql-expriments\OfficialDocuments\mysql-client-command\imptest.txt" -p
Enter password: **********
test.imptest: Records: 2  Deleted: 0  Skipped: 0  Warnings: 4
```