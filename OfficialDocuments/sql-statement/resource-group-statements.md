### 13.7.2 リソースグループ管理ステートメント
MySQL では、リソースグループの作成および管理がサポートされており、グループで使用可能なリソースに従ってスレッドが実行されるように、サーバー内で実行されているスレッドを特定のグループに割り当てることができます。 

### 13.7.2.1 ALTER RESOURCE GROUP ステートメント
グループスレッドの優先度を変更します
```sql
mysql> ALTER RESOURCE GROUP rg2 THREAD_PRIORITY = 5;
ERROR 3654 (HY000): Invalid thread priority value 5 for System resource group rg2. Allowed range is [-20, 0].
mysql> ALTER RESOURCE GROUP rg2 THREAD_PRIORITY = -1;
Query OK, 0 rows affected (0.00 sec)
```
グループを無効にし、そのグループに割り当てられているスレッドをデフォルトグループに移動します
```sql
mysql> ALTER RESOURCE GROUP rg2 DISABLE FORCE;
Query OK, 0 rows affected (0.00 sec)
```
### 13.7.2.2 CREATE RESOURCE GROUP ステートメント
単一の CPU と最も低い優先度を持つ有効なユーザーグループを作成します:
```sql
mysql> CREATE RESOURCE GROUP rg1
    ->   TYPE = USER
    ->   VCPU = 0
    ->   THREAD_PRIORITY = 19;
Query OK, 0 rows affected (0.01 sec)
```
CPU アフィニティがなく (すべての CPU を使用できる)、優先度が最も高い無効なシステムグループを作成します
```sql
mysql> CREATE RESOURCE GROUP rg2
    ->   TYPE = SYSTEM
    ->   THREAD_PRIORITY = -20
    ->   DISABLE;
Query OK, 0 rows affected (0.00 sec)
```
### 13.7.2.3 DROP RESOURCE GROUP ステートメント
グループを削除します。グループにスレッドが含まれている場合は失敗します
```sql
mysql> DROP RESOURCE GROUP rg1;
Query OK, 0 rows affected (0.00 sec)
```
グループを削除し、既存のスレッドをデフォルトグループに移動します
```sql
mysql> DROP RESOURCE GROUP rg2 FORCE;
Query OK, 0 rows affected (0.00 sec)
```

### 13.7.2.4 SET RESOURCE GROUP ステートメント
* 現在のセッションスレッドをグループに割り当てます
```sql
mysql> SET RESOURCE GROUP rg1;
Query OK, 0 rows affected (0.00 sec)
```
名前付きスレッドをグループに割り当てます:
```Sql
mysql> SET RESOURCE GROUP rg1 FOR 14, 78, 4;
Query OK, 0 rows affected, 3 warnings (0.00 sec)

mysql> SHOW WARNINGS;
+---------+------+----------------------------------------------------------------------------------------------------------+
| Level   | Code | Message
    |
+---------+------+----------------------------------------------------------------------------------------------------------+
| Warning | 3661 | Unable to bind resource group rg1 with thread id (14).(Resource group type & thread type doesn't match). |
| Warning | 3660 | Invalid thread id (78).
    |
| Warning | 3661 | Unable to bind resource group rg1 with thread id (4).(Resource group type & thread type doesn't match).  |
+---------+------+----------------------------------------------------------------------------------------------------------+
3 rows in set (0.00 sec)
```