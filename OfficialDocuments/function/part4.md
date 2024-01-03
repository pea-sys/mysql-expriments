### 12.10.1 自然言語全文検索
```sql
mysql> CREATE TABLE articles (
    ->           id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->           title VARCHAR(200),
    ->           body TEXT,
    ->           FULLTEXT (title,body)
    ->         ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.19 sec)

mysql> INSERT INTO articles (title,body) VALUES
    ->         ('MySQL Tutorial','DBMS stands for DataBase ...'),
    ->         ('How To Use MySQL Well','After you went through a ...'),
    ->         ('Optimizing MySQL','In this tutorial, we show ...'),
    ->         ('1001 MySQL Tricks','1. Never run mysqld as root. 2. ...'),
    ->         ('MySQL vs. YourSQL','In the following database comparison ...'),
    ->         ('MySQL Security','When configured properly, MySQL ...');
Query OK, 6 rows affected (0.02 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM articles
    ->         WHERE MATCH (title,body)
    ->         AGAINST ('database' IN NATURAL LANGUAGE MODE);
+----+-------------------+------------------------------------------+
| id | title             | body                                     |
+----+-------------------+------------------------------------------+
|  1 | MySQL Tutorial    | DBMS stands for DataBase ...             |
|  5 | MySQL vs. YourSQL | In the following database comparison ... |
+----+-------------------+------------------------------------------+
2 rows in set (0.01 sec)
```
デフォルトでは、大文字と小文字が区別される方法で検索が実行されます。

単に一致をカウントするには、次のようなクエリーを使用
```sql
mysql> SELECT COUNT(*) FROM articles
    ->     WHERE MATCH (title,body)
    ->     AGAINST ('database' IN NATURAL LANGUAGE MODE);
+----------+
| COUNT(*) |
+----------+
|        2 |
+----------+
1 row in set (0.00 sec)
```
次のように、クエリーを再作成した方が早い場合もあります。
```sql
mysql> SELECT
    ->     COUNT(IF(MATCH (title,body) AGAINST ('database' IN NATURAL LANGUAGE MODE), 1, NULL))
    ->     AS count
    ->     FROM articles;
+-------+
| count |
+-------+
|     2 |
+-------+
1 row in set (0.00 sec)
```
関連性の値を明示的に取得する
```sql
mysql> SELECT id, MATCH (title,body)
    ->     AGAINST ('Tutorial' IN NATURAL LANGUAGE MODE) AS score
    ->     FROM articles;
+----+---------------------+
| id | score               |
+----+---------------------+
|  1 | 0.22764469683170319 |
|  2 |                   0 |
|  3 | 0.22764469683170319 |
|  4 |                   0 |
|  5 |                   0 |
|  6 |                   0 |
+----+---------------------+
6 rows in set (0.00 sec)
```
次の例はさらに複雑です。 このクエリーでは関連性の値が返され、関連性の降順での行のソートも行われます。 この結果を実現するには、MATCH() を 2 回 (1 回は SELECT リストに、もう 1 回は WHERE 句に) 指定します。 MySQL オプティマイザによって、2 回の MATCH() 呼び出しが同じであり、全文検索コードが 1 回のみ起動されることが検出されるため、追加のオーバーヘッドは発生しません。
```sql
mysql> SELECT id, body, MATCH (title,body) AGAINST
    ->     ('Security implications of running MySQL as root'
    ->     IN NATURAL LANGUAGE MODE) AS score
    ->     FROM articles WHERE MATCH (title,body) AGAINST
    ->     ('Security implications of running MySQL as root'
    ->     IN NATURAL LANGUAGE MODE);
+----+------------------------------------------+----------------------------+
| id | body                                     | score                      |
+----+------------------------------------------+----------------------------+
|  4 | 1. Never run mysqld as root. 2. ...      |         0.6055193543434143 |
|  6 | When configured properly, MySQL ...      |         0.6055193543434143 |
|  1 | DBMS stands for DataBase ...             | 0.000000001885928302414186 |
|  2 | After you went through a ...             | 0.000000001885928302414186 |
|  3 | In this tutorial, we show ...            | 0.000000001885928302414186 |
|  5 | In the following database comparison ... | 0.000000001885928302414186 |
+----+------------------------------------------+----------------------------+
6 rows in set (0.00 sec)
```

### 12.10.2 ブール全文検索
MySQL では、IN BOOLEAN MODE 修飾子を使用することでブール全文検索を実行できます。
```sql
mysql> SELECT * FROM articles WHERE MATCH (title,body)
    ->     AGAINST ('+MySQL -YourSQL' IN BOOLEAN MODE);
+----+-----------------------+-------------------------------------+
| id | title                 | body                                |
+----+-----------------------+-------------------------------------+
|  6 | MySQL Security        | When configured properly, MySQL ... |
|  1 | MySQL Tutorial        | DBMS stands for DataBase ...        |
|  2 | How To Use MySQL Well | After you went through a ...        |
|  3 | Optimizing MySQL      | In this tutorial, we show ...       |
|  4 | 1001 MySQL Tricks     | 1. Never run mysqld as root. 2. ... |
+----+-----------------------+-------------------------------------+
5 rows in set (0.00 sec)
```
単一単語検索の関連性ランキング
```sql
mysql> CREATE TABLE articles (
    -> id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    -> title VARCHAR(200),
    -> body TEXT,
    -> FULLTEXT (title,body)
    -> ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.19 sec)

mysql> INSERT INTO articles (title,body) VALUES
    -> ('MySQL Tutorial','This database tutorial ...'),
    -> ("How To Use MySQL",'After you went through a ...'),
    -> ('Optimizing Your Database','In this database tutorial ...'),
    -> ('MySQL vs. YourSQL','When comparing databases ...'),
    -> ('MySQL Security','When configured properly, MySQL ...'),
    -> ('Database, Database, Database','database database database'),
    -> ('1001 MySQL Tricks','1. Never run mysqld as root. 2. ...'),
    -> ('MySQL Full-Text Indexes', 'MySQL fulltext indexes use a ..');
Query OK, 8 rows affected (0.01 sec)
Records: 8  Duplicates: 0  Warnings: 0

mysql> SELECT id, title, body, MATCH (title,body)  AGAINST ('database' IN BOOLEAN MODE)
    -> AS score FROM articles ORDER BY score DESC;
+----+------------------------------+-------------------------------------+---------------------+
| id | title                        | body                                | score               |
+----+------------------------------+-------------------------------------+---------------------+
|  6 | Database, Database, Database | database database database          |  1.0886961221694946 |
|  3 | Optimizing Your Database     | In this database tutorial ...       | 0.36289870738983154 |
|  1 | MySQL Tutorial               | This database tutorial ...          | 0.18144935369491577 |
|  2 | How To Use MySQL             | After you went through a ...        |                   0 |
|  4 | MySQL vs. YourSQL            | When comparing databases ...        |                   0 |
|  5 | MySQL Security               | When configured properly, MySQL ... |                   0 |
|  7 | 1001 MySQL Tricks            | 1. Never run mysqld as root. 2. ... |                   0 |
|  8 | MySQL Full-Text Indexes      | MySQL fulltext indexes use a ..     |                   0 |
+----+------------------------------+-------------------------------------+---------------------+
8 rows in set (0.00 sec)
```
複数単語検索の関連性ランキング
```sql
mysql> SELECT id, title, body, MATCH (title,body)  AGAINST ('mysql tutorial' IN BOOLEAN MODE)
    ->     AS score FROM articles ORDER BY score DESC;
+----+------------------------------+-------------------------------------+----------------------+
| id | title                        | body                                | score                |
+----+------------------------------+-------------------------------------+----------------------+
|  1 | MySQL Tutorial               | This database tutorial ...          |   0.7405621409416199 |
|  3 | Optimizing Your Database     | In this database tutorial ...       |   0.3624762296676636 |
|  5 | MySQL Security               | When configured properly, MySQL ... | 0.031219376251101494 |
|  8 | MySQL Full-Text Indexes      | MySQL fulltext indexes use a ..     | 0.031219376251101494 |
|  2 | How To Use MySQL             | After you went through a ...        | 0.015609688125550747 |
|  4 | MySQL vs. YourSQL            | When comparing databases ...        | 0.015609688125550747 |
|  7 | 1001 MySQL Tricks            | 1. Never run mysqld as root. 2. ... | 0.015609688125550747 |
|  6 | Database, Database, Database | database database database          |                    0 |
+----+------------------------------+-------------------------------------+----------------------+
8 rows in set (0.00 sec)
```
### 12.10.3 クエリー拡張を使用した全文検索
全文検索では、クエリー拡張 (特に、そのバリアントの 「ブラインドクエリー拡張」) がサポートされています。
```sql
mysql> SELECT * FROM articles
    ->     WHERE MATCH (title,body)
    ->     AGAINST ('database' IN NATURAL LANGUAGE MODE);
+----+------------------------------+-------------------------------+
| id | title                        | body                          |
+----+------------------------------+-------------------------------+
|  6 | Database, Database, Database | database database database    |
|  3 | Optimizing Your Database     | In this database tutorial ... |
|  1 | MySQL Tutorial               | This database tutorial ...    |
+----+------------------------------+-------------------------------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM articles
    ->     WHERE MATCH (title,body)
    ->     AGAINST ('database' WITH QUERY EXPANSION);
+----+------------------------------+-------------------------------------+
| id | title                        | body                                |
+----+------------------------------+-------------------------------------+
|  3 | Optimizing Your Database     | In this database tutorial ...       |
|  6 | Database, Database, Database | database database database          |
|  1 | MySQL Tutorial               | This database tutorial ...          |
|  5 | MySQL Security               | When configured properly, MySQL ... |
|  8 | MySQL Full-Text Indexes      | MySQL fulltext indexes use a ..     |
|  2 | How To Use MySQL             | After you went through a ...        |
|  4 | MySQL vs. YourSQL            | When comparing databases ...        |
|  7 | 1001 MySQL Tricks            | 1. Never run mysqld as root. 2. ... |
+----+------------------------------+-------------------------------------+
8 rows in set (0.00 sec)
```
### 12.10.4 全文ストップワード
サーバー文字セットおよび照合順序 (character_set_server および collation_server システム変数の値) を使用すると、全文クエリー用のストップワードリストがロードおよび検索されます。
```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_FT_DEFAULT_STOPWORD;
+-------+
| value |
+-------+
| a     |
| about |
| an    |
| are   |
| as    |
| at    |
| be    |
| by    |
| com   |
| de    |
| en    |
| for   |
| from  |
| how   |
| i     |
| in    |
| is    |
| it    |
| la    |
| of    |
| on    |
| or    |
| that  |
| the   |
| this  |
| to    |
| was   |
| what  |
| when  |
| where |
| who   |
| will  |
| with  |
| und   |
| the   |
| www   |
+-------+
36 rows in set (0.01 sec)
```
すべての InnoDB テーブルで独自のストップワードリストを定義するには、INNODB_FT_DEFAULT_STOPWORD テーブルと同じ構造を持つテーブルを定義し、それにストップワードを移入し、innodb_ft_server_stopword_table オプションの値を db_name/table_name 形式の値に設定してから、全文インデックスを作成します。
```sql
mysql> CREATE TABLE my_stopwords(value VARCHAR(30)) ENGINE = INNODB;
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO my_stopwords(value) VALUES ('Ishmael');
Query OK, 1 row affected (0.00 sec)

mysql> CREATE TABLE opening_lines (
    -> id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    -> opening_line TEXT(500),
    -> author VARCHAR(200),
    -> title VARCHAR(200)
    -> ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO opening_lines(opening_line,author,title) VALUES
    -> ('Call me Ishmael.','Herman Melville','Moby-Dick'),
    -> ('A screaming comes across the sky.','Thomas Pynchon','Gravity\'s Rainbow'),
    -> ('I am an invisible man.','Ralph Ellison','Invisible Man'),
    -> ('Where now? Who now? When now?','Samuel Beckett','The Unnamable'),
    -> ('It was love at first sight.','Joseph Heller','Catch-22'),
    -> ('All this happened, more or less.','Kurt Vonnegut','Slaughterhouse-Five'),
    -> ('Mrs. Dalloway said she would buy the flowers herself.','Virginia Woolf','Mrs. Dalloway'),
    -> ('It was a pleasure to burn.','Ray Bradbury','Fahrenheit 451');
Query OK, 8 rows affected (0.01 sec)
Records: 8  Duplicates: 0  Warnings: 0

mysql> SET GLOBAL innodb_ft_server_stopword_table = 'sakila/my_stopwords';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE FULLTEXT INDEX idx ON opening_lines(opening_line);
Query OK, 0 rows affected, 1 warning (0.24 sec)
Records: 0  Duplicates: 0  Warnings: 1

mysql> SET GLOBAL innodb_ft_aux_table='sakila/opening_lines';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT word FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_TABLE LIMIT 15;
+-----------+
| word      |
+-----------+
| across    |
| all       |
| burn      |
| buy       |
| call      |
| comes     |
| dalloway  |
| first     |
| flowers   |
| happened  |
| herself   |
| invisible |
| less      |
| love      |
| man       |
+-----------+
15 rows in set (0.00 sec)
```

### 12.10.6 MySQL の全文検索の微調整
InnoDB 全文インデックスの最適化

全文インデックス付きのテーブル上で OPTIMIZE TABLE を実行すると、全文インデックスが再構築され、削除済みのドキュメント ID が削除され、同じ単語に対応する複数のエントリが連結されます 
```sql
mysql> set GLOBAL innodb_optimize_fulltext_only=ON;
Query OK, 0 rows affected (0.00 sec)

mysql> OPTIMIZE TABLE opening_lines;
+----------------------+----------+----------+----------+
| Table                | Op       | Msg_type | Msg_text |
+----------------------+----------+----------+----------+
| sakila.opening_lines | optimize | status   | OK       |
+----------------------+----------+----------+----------+
1 row in set (0.00 sec)
```
### 12.10.8 ngram 全文パーサー
組込みの MySQL 全文パーサーは、単語間の空白をデリミタとして使用して、単語の開始位置と終了位置を決定します。

既存のテーブルに FULLTEXT インデックスを追加するには、ALTER TABLE または CREATE INDEX を使用できます
```Sql
mysql> CREATE TABLE articles (
    ->       id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->       title VARCHAR(200),
    ->       body TEXT
    ->      ) ENGINE=InnoDB CHARACTER SET utf8;
Query OK, 0 rows affected, 1 warning (0.02 sec)

mysql> ALTER TABLE articles ADD FULLTEXT INDEX ft_index (title,body) WITH PARSER ngram;
Query OK, 0 rows affected, 1 warning (0.24 sec)
Records: 0  Duplicates: 0  Warnings: 1
```