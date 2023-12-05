## myisamchk
myisamchk ユーティリティーは、データベーステーブルに関する情報を取得したり、データベーステーブルのチェック、修復、または最適化を実行したりします。


* --analyze:キー値の分布を分析
```
C:\Users\masami>myisamchk --analyze "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
- check data record references index: 2
- check record links
```
* --backup:.MYD ファイルのバックアップを file_name-time.BAK として作成
```
C:\Users\masami>myisamchk --backup "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
- check data record references index: 2
- check record links
```

* --check:テーブルにエラーがないか確認  
  --check-only-changed:最後に行われた検査以降に変更されたテーブルのみをチェック
```
C:\Users\masami>myisamchk --check-only-changed "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI is already checked
```

* --correct-checksum:テーブルのチェックサム情報を修正
```
C:\Users\masami>myisamchk --correct-checksum "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
- check data record references index: 2
- check record links
```
* --description:テーブルの説明情報を出力

```
C:\Users\masami>myisamchk --description "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"

MyISAM file:         C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Record format:       Packed
Collation:           utf8mb4_0900_ai_ci (255)
Data records:                    2  Deleted blocks:                 0
Recordlength:                   15

table description:
Key Start Len Index   Type
1   1     4   unique  long
2   5     336 fulltext ? packed
    1     4           float

C:\Users\masami>myisamchk -dv "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"

MyISAM file:         C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Record format:       Packed
Collation:           utf8mb4_0900_ai_ci (255)
File-version:        1
Creation time:       2023-11-25 21:59:37
Recover time:        2023-11-26  9:41:12
Status:              checked,optimized keys
Data records:                    2  Deleted blocks:                 0
Datafile parts:                  2  Deleted data:                   0
Datafile pointer (bytes):        6  Keyfile pointer (bytes):        7
Datafile length:                76  Keyfile length:              3072
Max datafile length: 281474976710654  Max keyfile length: 18446744073709551614
Recordlength:                   15

table description:
Key Start Len Index   Type                     Rec/key         Root  Blocksize
1   1     4   unique  long                           0         1024       1024
2   5     336 fulltext ? packed                       0         2048       1024
    1     4           float
```
* --extend-check:データファイルからすべての行をリカバリする修復を試みる、非常に徹底したテーブルチェックを実行または修復を実行
```
C:\Users\masami>myisamchk --extend-check "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
- check data record references index: 2
- check records and index references
```
* --fast:正しく閉じられていないテーブルのみをチェック
```
C:\Users\masami>myisamchk --fast "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI is already checked
```
* --force:myisamchk がエラーをテーブル内で発見した場合、自動的に修復オペレーションを実行する。
```
C:\Users\masami>myisamchk --force "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
myisamchk: warning: 1 client is using or hasn't closed the table properly
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
- check data record references index: 2
- check record links
```
* --information:チェックされたテーブルの統計を出力
```
C:\Users\masami>myisamchk --information "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
Key:  1:  Keyblocks used:   2%  Packed:    0%  Max levels:  1
- check data record references index: 2
Key:  2:  Keyblocks used:   2%  Packed:   93%  Max levels:  1
Total:    Keyblocks used:   2%  Packed:   87%

- check record links
Records:                 2    M.recordlength:       34   Packed:             0%
Recordspace used:      100%   Empty space:           0%  Blocks/Record:   1.00
Record blocks:           2    Delete blocks:         0
Record data:            69    Deleted data:          0
Lost space:              0    Linkdata:              7
```
* --medium-check:--extend-check 操作よりも速いチェックを実行
```
C:\Users\masami>myisamchk --medium-check "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
- check data record references index: 2
- check record links
```
* --quick:データファイルを変更しないことで、修復のスピードを向上
```
C:\Users\masami>myisamchk --quick "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
- check data record references index: 2
- check record links
```
* --recover:一意ではない一意なキー以外のすべてを修復できる修復を実行
```
C:\Users\masami>myisamchk --recover "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
- recovering (with sort) MyISAM-table 'C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI'
Data records: 2
- Fixing index 1
- Fixing index 2
```

* --safe-recover:すべての行を順に読み取り、検出された行に基づいてすべてのインデックスツリーを更新する古いリカバリ方法を使用して修復
```
C:\Users\masami>myisamchk --safe-recover "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
- recovering (with keycache) MyISAM-table 'C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI'
Data records: 2
```
* --set-auto-increment:新しいレコードが指定された値で開始するように AUTO_INCREMENT ナンバリングを強制
```
C:\Users\masami>myisamchk --set-auto-increment=10 "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Table: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI doesn't have an auto increment key
```
* --unpack	myisampack でパックされたテーブルをアンパック
```
C:\Users\masami>myisamchk --unpack "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
- recovering (with sort) MyISAM-table 'C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI'
Data records: 2
- Fixing index 1
- Fixing index 2
```
* --update-state:情報を .MYI ファイルに保存し、いつテーブルがチェックされたか、およびテーブルがクラッシュしたかどうかをチェックします。
```
C:\Users\masami>myisamchk --update-state "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI"
Checking MyISAM file: C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI
Data records:       2   Deleted blocks:       0
- check file-size
- check record delete-chain
- check key delete-chain
- check index reference
- check data record references index: 1
- check data record references index: 2
- check record links
```