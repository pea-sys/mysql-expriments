## 5.8.2.1 Windows コマンド行での複数の MySQL インスタンスの起動

* 2 つのオプションファイルを作成します。  
格納場所は任意  

my-opts1.cnf
```
[mysqld]
datadir = C:/mydata1
port = 3307
```
my-opts2.cnf

```
[mysqld]
datadir = C:/mydata2
port = 3308
```

* インスタンス起動
```
C:\Users\masami>"C:\Program Files\MySQL\MySQL Server 8.2\bin\mysqld" defaults-file="C:\Users\masami\source\repos\mysql-expriments\OfficialDocuments\MySQLServer\my-opts1.cnf"

C:\Users\masami>"C:\Program Files\MySQL\MySQL Server 8.2\bin\mysqld" defaults-file="C:\Users\masami\source\repos\mysql-expriments\OfficialDocuments\MySQLServer\my-opts2.cnf"
```
インスタンス停止
```
mysqladmin --port=3307 --host=127.0.0.1 --user=root --password shutdown
mysqladmin --port=3308 --host=127.0.0.1 --user=root --password shutdown
```

[!NOTE]  
C:\Program Files\MySQL\MySQL Server 8.2\dataフォルダがない場合は作成する必要がある

名前付きパイプ接続をサポートする各サーバーは、一意のパイプ名を使用する必要があり
```
[mysqld]
datadir = C:/mydata1
port = 3307
enable-named-pipe
socket = mypipe1
```