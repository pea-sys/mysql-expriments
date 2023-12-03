# mysqlslap
mysqlslap は MySQL サーバーのクライアント負荷をエミュレートし、各段階のタイミングをレポートする診断プログラムです。 

```
C:\Users\masami>mysqlslap --delimiter=";" --create="CREATE TABLE a (b int);INSERT INTO a VALUES (23)" --query="SELECT * FROM a" --concurrency=50 --iterations=200 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.022 seconds
        Minimum number of seconds to run all queries: 0.000 seconds
        Maximum number of seconds to run all queries: 1.016 seconds
        Number of clients running queries: 50
        Average number of queries per client: 1

```
* --auto-generate-sql:SQL ステートメントがファイルおよびコマンドオプションを使用して指定されない場合、自動的に生成
```
C:\Users\masami>mysqlslap --create="CREATE TABLE t1 (id MEDIUMINT AUTO_INCREMENT,PRIMARY KEY (id));" --auto-generate-sql-add-autoincrement --concurrency=50 --iterations=200 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.018 seconds
        Minimum number of seconds to run all queries: 0.000 seconds
        Maximum number of seconds to run all queries: 1.016 seconds
        Number of clients running queries: 50
        Average number of queries per client: 0
```

*--auto-generate-sql-execute-number:自動的に生成するクエリーの数を指定
```
C:\Users\masami>mysqlslap --auto-generate-sql --auto-generate-sql-execute-number=1 --concurrency=1 --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.000 seconds
        Minimum number of seconds to run all queries: 0.000 seconds
        Maximum number of seconds to run all queries: 0.000 seconds
        Number of clients running queries: 1
        Average number of queries per client: 1
```
パラメータ指定を間違えると1時間待っても終了しない。
```
C:\Users\masami>mysqlslap --auto-generate-sql-execute-number=1 --concurrency=1 --iterations=1 -u root -p
Enter password: **********

```
* --auto-generate-sql-load-type:テストの負荷タイプを指定します


* --auto-generate-sql-guid-primary:自動生成されたテーブルに GUID ベースの主キーを追加
```
C:\Users\masami>mysqlslap --auto-generate-sql --auto-generate-sql-guid-primary --concurrency=1 --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.031 seconds
        Minimum number of seconds to run all queries: 0.031 seconds
        Maximum number of seconds to run all queries: 0.031 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
--auto-generate-sql-load-type:テストの負荷タイプを指定します
```
C:\Users\masami>mysqlslap --auto-generate-sql --auto-generate-sql-load-type=write --concurrency=1 --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.016 seconds
        Minimum number of seconds to run all queries: 0.016 seconds
        Maximum number of seconds to run all queries: 0.016 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --auto-generate-sql-secondary-indexes:自動生成されたテーブルに追加するセカンダリインデックスの数を指定
```
C:\Users\masami>mysqlslap --auto-generate-sql --auto-generate-sql-secondary-indexes=3 --concurrency=1 --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.016 seconds
        Minimum number of seconds to run all queries: 0.016 seconds
        Maximum number of seconds to run all queries: 0.016 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --auto-generate-sql-unique-query-number:自動テスト用に生成する異なるクエリーの数
```
C:\Users\masami>mysqlslap --auto-generate-sql --auto-generate-sql-unique-query-number=2 --concurrency=1 --verbose --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.016 seconds
        Minimum number of seconds to run all queries: 0.016 seconds
        Maximum number of seconds to run all queries: 0.016 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --auto-generate-sql-unique-write-number:--auto-generate-sql-write-number 用に生成する異なるクエリーの数
```
C:\Users\masami>mysqlslap --auto-generate-sql --auto-generate-sql-unique-write-number=2 --concurrency=1 --verbose --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.015 seconds
        Minimum number of seconds to run all queries: 0.015 seconds
        Maximum number of seconds to run all queries: 0.015 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --auto-generate-sql-write-number	各スレッドで実行する行挿入の回数
```
C:\Users\masami>mysqlslap --auto-generate-sql --auto-generate-sql-unique-write-number=2 --concurrency=1 --verbose --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.015 seconds
        Minimum number of seconds to run all queries: 0.015 seconds
        Maximum number of seconds to run all queries: 0.015 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --commit:コミットの前に実行するステートメントの数
```
C:\Users\masami>mysqlslap --auto-generate-sql --commit=5 --concurrency=1 --verbose --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.016 seconds
        Minimum number of seconds to run all queries: 0.016 seconds
        Maximum number of seconds to run all queries: 0.016 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --concurrency:SELECT ステートメントを発行する際、シミュレートするクライアントの数		
* --create:テーブルの作成に使用するステートメントを含むファイルまたは文字列		
* --create-schema:テストを実行するスキーマ
* --detach:N 個のステートメントが終わるごとに各接続を切り離す (閉じてからふたたび開く)
```
C:\Users\masami>mysqlslap --auto-generate-sql --detach=3 --concurrency=1 --verbose --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.031 seconds
        Minimum number of seconds to run all queries: 0.031 seconds
        Maximum number of seconds to run all queries: 0.031 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --engine:テーブルの作成に使用するストレージエンジン
```
C:\Users\masami>mysqlslap --auto-generate-sql --engine=INNODB --concurrency=1 --verbose --iterations=1 -u root -p
Enter password: **********
Benchmark
        Running for engine INNODB
        Average number of seconds to run all queries: 0.031 seconds
        Minimum number of seconds to run all queries: 0.031 seconds
        Maximum number of seconds to run all queries: 0.031 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0

C:\Users\masami>mysqlslap --auto-generate-sql --engine=MEMORY --concurrency=1 --verbose --iterations=1 -u root -p
Enter password: **********
Benchmark
        Running for engine MEMORY
        Average number of seconds to run all queries: 0.000 seconds
        Minimum number of seconds to run all queries: 0.000 seconds
        Maximum number of seconds to run all queries: 0.000 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --no-drop:テスト実行中に作成されたスキーマを削除しない
```
C:\Users\masami>mysqlslap --no-drop --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.000 seconds
        Minimum number of seconds to run all queries: 0.000 seconds
        Maximum number of seconds to run all queries: 0.000 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0

C:\Users\masami>mysqlshow mysqlslap -u root -p
Enter password: **********
Database: mysqlslap
+--------+
| Tables |
+--------+
+--------+
```
* --number-char-cols:--auto-generate-sql が指定された場合に使用する VARCHAR カラムの数
```
C:\Users\masami>mysqlslap --auto-generate-sql --number-char-cols=3 --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.016 seconds
        Minimum number of seconds to run all queries: 0.016 seconds
        Maximum number of seconds to run all queries: 0.016 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --number-int-cols:--auto-generate-sql が指定された場合に使用する INT カラムの数
```
C:\Users\masami>mysqlslap --auto-generate-sql --number-int-cols=3 --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.015 seconds
        Minimum number of seconds to run all queries: 0.015 seconds
        Maximum number of seconds to run all queries: 0.015 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
* --number-of-queries:各クライアントのクエリー数をおよそこの数に制限
```
C:\Users\masami>mysqlslap --auto-generate-sql --number-of-queries=5 --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.000 seconds
        Minimum number of seconds to run all queries: 0.000 seconds
        Maximum number of seconds to run all queries: 0.000 seconds
        Number of clients running queries: 1
        Average number of queries per client: 5
```
--only-print:データベースに接続しない。mysqlslap が実行したであろう内容を出力するのみ
```
C:\Users\masami>mysqlslap --auto-generate-sql --only-print --iterations=1 -u root -p
Enter password: **********
DROP SCHEMA IF EXISTS `mysqlslap`;
CREATE SCHEMA `mysqlslap`;
use mysqlslap;
CREATE TABLE `t1` (intcol1 INT(32) ,charcol1 VARCHAR(128));
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (17469,'W3Wt5qxGdIZGuOXTvlHkHjkAoSL5YDYac6GC90EfjXL9JlPPGAvTOmZu90FZpLYSyromAiuJAYiBF4akMvKuC1Mnd9lFuWNaLsYfoYiRobo3zakQzy5m6gRs7YGERh');
INSERT INTO t1 VALUES (41,'nagT4IIMk551R1Bv2RcrO2ZsMLwebn6BI9snJvuNr2ZZkLXtSnkMxxJNLpyGbqiomg24SQlq9Aq6LZ89NOacKmwBSOfQjHyei9hZbJIOWxQt6h5ksW9HZxCmWhcbnF');
INSERT INTO t1 VALUES (15574,'BsqAL4mARB7xdnZ35Tpeho2mQAZ80BibAzOH83vL2qXwEgcLofo41utJIe087n8DojnLWHDaTwbrKTJwy3kSOTp2FPZ3jRaN80II0wehTepdCiysezAHeBFSGr2uYF');
INSERT INTO t1 VALUES (20328,'QMQbtpiOchI2r1zHcsTbYKq3phoL9FjMP2PSmxxsYTuhTw9SC5vEp1YP0yZz3JG7ppd6pDbxGQP8Mbuu1wXQNtvlDC8Y5tc1y6M14c2p1FqDQkzCfZkuq59mZa28e7');
INSERT INTO t1 VALUES (9905,'IB2PHaEOuECzDqndIHDqNSY5iKt72Jnlj4jYhLjCltlBYM2QkgnKZEqAvtXKwnPAKEvOzPwaeMXQyZXkyOzsgRg6AHAbcnjIO9Mml7xEl7p8jK2M3wH1v54gMGqmXr');
INSERT INTO t1 VALUES (6411,'nZgWk8jKX2T0DyyPnH0IJNgdkx8t71q0oZOP0XOvGCAjPcw6x6dxrpLw2dYxc5h39p5JscbtikausdTYNRNBvnove32uX1Ff5hq6EPjWyJLDEHEcDcP2oh0DLvF22B');
INSERT INTO t1 VALUES (30877,'OeCNcDKDbYeAxsYbTB0S5Ax9APZurgOebzYl413pFSCxTc17nLTgZELHvjtsOLsfWS74v6IAnRONhj6NAX90kizZXkIsW5vf3MeROSXktb5YQITMz5Tkl0P8yW13Cs');
INSERT INTO t1 VALUES (27152,'7A3L3NnKTblS3Ro3dT391S28KhIE8mWT3A0S7cNb65ID94S3zdmOFeQXPFhpGkbO2bZ2wosQQD94lmsvZ5GchaucswyMbFrqLlXxmAWMaZZwCS2ZujENcSOHDpSgKI');
INSERT INTO t1 VALUES (16858,'qbhONG3TMLpoMZE02NrpefozX3l1kYlte92yHqLuS1zDF9d9y0rkxFFFWfY82a5A4ugvX1vzAeZ8dCG9DohAj3Sky0XPErt7tF85GTWI9D6Kc9RSf4L56cG6mnLkKm');
INSERT INTO t1 VALUES (6021,'ML4TFMtO1lMjr6SIMn7LsGqup4cqtwsaP88rYXuze24bY9fyNI6JemGMteM6YHPRy2RIqCKIfsoM0vF2RGdedSrwq95ypgKgRMbbChEImE689y3Fb1vF8aW5kxi1k9');
INSERT INTO t1 VALUES (18283,'2rcN83iyGgXMcql46x93aHooXcjSnQXHYjn8PSlyxjD1WoBA9YETLlnDtaYYW12HHWiO1W3HjJb9niqiI67q8BeDKjmiWYoELINnPILHAuyILNwnypv5bQ6ZKA8oYD');
SELECT intcol1,charcol1 FROM t1;
INSERT INTO t1 VALUES (23840,'mvAJHs28CTkE8C7Yv8OHNpvL9KCojruDBKuWHzKIXOgnzPNIsDsr0ugxPpgPrCApefBdDZ0HQGqiIzITfTOC0878xNARcEiBzLGqN3cCChaZiGgbkJc0IYn4gdQW1i');
SELECT intcol1,charcol1 FROM t1;
INSERT INTO t1 VALUES (25249,'jIlhvcGIeJedWRTLveGZJOPZosNpESFXZiSB6k6HxpcuBpsmZXb9d0CwX7QMbNu0H55tLeIIgrRlqbpWTZ5E27CGnMMA1XRlzLyX7SmGJ926Qboa0ZHSN1ribdQshG');
SELECT intcol1,charcol1 FROM t1;
INSERT INTO t1 VALUES (26281,'hbn4IFcafeEDMFNStY706yWh1fbNGD1PtEqthRzqj5Y9LCc1RArlYYTK5ByDmySmrmcH0upSpkTyHv6OoQ8deWJPQz0AXZvPIgB7n4Qng7uXCX8DPdrOHmSiZK2HeS');
SELECT intcol1,charcol1 FROM t1;
INSERT INTO t1 VALUES (7355,'TR1DO7LZxLMn5DOOFPh41C6wjZK0orZSBcJS0XZJNJDcv5ugF0cbPrSzcMCl0vMP2Kcx2800Gyt0QEtH0vY0nbQ1818vGPpsGviNMLiY8e8HuAWbfpvNKJJ5B1hstZ');
SELECT intcol1,charcol1 FROM t1;
INSERT INTO t1 VALUES (32029,'RMZjKaI4jTCOTq5na7QcN0zq8qr51WN9FyzQ2bRGtobfZW25LPnRTEXHe5dyIOsjSk1NqkhndsXNLjhPLlAF1MCrbFZefMoui75IOWJu43Pe41qZNEW67lPcqTe3vx');
DROP SCHEMA IF EXISTS `mysqlslap`;
```
* --post-query:テスト完了後に実行するステートメントを含むファイルまたは文字列
```
C:\Users\masami>mysqlslap --auto-generate-sql --post-query="SELECT 1;" --iterations=1 -u root -p
Enter password: **********
Benchmark
        Average number of seconds to run all queries: 0.015 seconds
        Minimum number of seconds to run all queries: 0.015 seconds
        Maximum number of seconds to run all queries: 0.015 seconds
        Number of clients running queries: 1
        Average number of queries per client: 0
```
