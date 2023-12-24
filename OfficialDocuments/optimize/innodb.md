### 8.5.5 InnoDB テーブルの一括データロード
* InnoDB にデータをインポートする場合、自動コミットモードでは挿入のたびに、ディスクへのログのフラッシュを実行するため、それをオフにします。 
```sql
SET autocommit=0;
... SQL import statements ...
COMMIT;
```
* 副キーに UNIQUE 制約がある場合、インポートセッション中に一意性チェックを一時的にオフにすることで、テーブルインポートを高速化できます。
```sql
SET unique_checks=0;
... SQL import statements ...
SET unique_checks=1;
```
* テーブルに FOREIGN KEY 制約がある場合、インポートセッションの間の外部キーチェックをオフにすることで、テーブルインポートを高速化できます。
```sql
SET foreign_key_checks=0;
... SQL import statements ...
SET foreign_key_checks=1;
```