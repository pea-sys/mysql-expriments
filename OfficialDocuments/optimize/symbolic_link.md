### 8.12.2 シンボリックリンクの使用
データベースやテーブルをデータベースディレクトリからほかの場所に移動して、それらを新しい場所へのシンボリックリンクに置き換えることができます。

データディレクトリの場所を特定するには、次のステートメントを使用します。
```sql
mysql> SHOW VARIABLES LIKE 'datadir';
+---------------+---------------------------------------------+
| Variable_name | Value                                       |
+---------------+---------------------------------------------+
| datadir       | C:\ProgramData\MySQL\MySQL Server 8.2\Data\ |
+---------------+---------------------------------------------+
1 row in set, 1 warning (0.04 sec)
```

### 8.12.2.3 Windows 上のデータベースへのシンボリックリンクの使用
Windows では、データベースディレクトリにシンボリックリンクを使用できます。

Windows では、mklink コマンドを使用してシンボリックリンクを作成できます。 このコマンドには管理者権限が必要です。

```sql
C:\ProgramData\MySQL\MySQL Server 8.2\Data>mklink /d sakila D:\sl
既に存在するファイルを作成することはできません。

C:\ProgramData\MySQL\MySQL Server 8.2\Data>mklink /d test D:\sl
test <<===>> D:\sl のシンボリック リンクが作成されました
```