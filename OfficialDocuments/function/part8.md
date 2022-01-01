### 12.18 JSON 関数

### 12.18.2 JSON 値を作成する関数
* JSON_ARRAY([val[, val] ...])
```sql
mysql> SELECT JSON_ARRAY(1, "abc", NULL, TRUE, CURTIME());
+---------------------------------------------+
| JSON_ARRAY(1, "abc", NULL, TRUE, CURTIME()) |
+---------------------------------------------+
| [1, "abc", null, true, "07:31:39.000000"]   |
+---------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_OBJECT([key, val[, key, val] ...])
```Sql
mysql> SELECT JSON_OBJECT('id', 87, 'name', 'carrot');
+-----------------------------------------+
| JSON_OBJECT('id', 87, 'name', 'carrot') |
+-----------------------------------------+
| {"id": 87, "name": "carrot"}            |
+-----------------------------------------+
1 row in set (0.00 sec)
```
* JSON_QUOTE(string)
```sql
mysql> SELECT JSON_QUOTE('[1, 2, 3]');
+-------------------------+
| JSON_QUOTE('[1, 2, 3]') |
+-------------------------+
| "[1, 2, 3]"             |
+-------------------------+
1 row in set (0.00 sec)
```
### 12.18.3 JSON 値を検索する関数
* JSON_CONTAINS(target, candidate[, path])
```sql
mysql> SET @j = '{"a": 1, "b": 2, "c": {"d": 4}}';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @j2 = '1';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_CONTAINS(@j, @j2, '$.a');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.a') |
+-------------------------------+
|                             1 |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_CONTAINS(@j, @j2, '$.b');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.b') |
+-------------------------------+
|                             0 |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SET @j2 = '{"d": 4}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_CONTAINS(@j, @j2, '$.a');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.a') |
+-------------------------------+
|                             0 |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_CONTAINS(@j, @j2, '$.c');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.c') |
+-------------------------------+
|                             1 |
+-------------------------------+
1 row in set (0.00 sec)
```
* JSON_CONTAINS_PATH(json_doc, one_or_all, path[, path] ...)
```sql
mysql> SET @j = '{"a": 1, "b": 2, "c": {"d": 4}}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.a', '$.e');
+---------------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.a', '$.e') |
+---------------------------------------------+
|                                           1 |
+---------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_CONTAINS_PATH(@j, 'all', '$.a', '$.e');
+---------------------------------------------+
| JSON_CONTAINS_PATH(@j, 'all', '$.a', '$.e') |
+---------------------------------------------+
|                                           0 |
+---------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.c.d');
+----------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.c.d') |
+----------------------------------------+
|                                      1 |
+----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.a.d');
+----------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.a.d') |
+----------------------------------------+
|                                      0 |
+----------------------------------------+
1 row in set (0.00 sec)
```
* JSON_EXTRACT(json_doc, path[, path] ...)
```Sql
mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]');
+--------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]') |
+--------------------------------------------+
| 20                                         |
+--------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]', '$[0]');
+----------------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]', '$[0]') |
+----------------------------------------------------+
| [20, 10]                                           |
+----------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[2][*]');
+-----------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[2][*]') |
+-----------------------------------------------+
| [30, 40]                                      |
+-----------------------------------------------+
1 row in set (0.00 sec)
```
* column->path
```sql
mysql> CREATE TABLE tj10 (a JSON, b INT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO tj10
    ->       VALUES ("[3,10,5,17,44]", 33), ("[3,10,5,17,[22,44,66]]", 0);
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT a->"$[4]" FROM tj10;
+--------------+
| a->"$[4]"    |
+--------------+
| 44           |
| [22, 44, 66] |
+--------------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM tj10 WHERE a->"$[0]" = 3;
+------------------------------+------+
| a                            | b    |
+------------------------------+------+
| [3, 10, 5, 17, 44]           |   33 |
| [3, 10, 5, 17, [22, 44, 66]] |    0 |
+------------------------------+------+
2 rows in set (0.00 sec)
```
* column->>path
```sql
mysql> CREATE TABLE tj10 (a JSON, b INT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO tj10 VALUES
    ->          ('[3,10,5,"x",44]', 33),
    ->          ('[3,10,5,17,[22,"y",66]]', 0);
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT a->"$[3]", a->"$[4][1]" FROM tj10;
+-----------+--------------+
| a->"$[3]" | a->"$[4][1]" |
+-----------+--------------+
| "x"       | NULL         |
| 17        | "y"          |
+-----------+--------------+
2 rows in set (0.00 sec)

mysql> SELECT a->>"$[3]", a->>"$[4][1]" FROM tj10;
+------------+---------------+
| a->>"$[3]" | a->>"$[4][1]" |
+------------+---------------+
| x          | NULL          |
| 17         | y             |
+------------+---------------+
2 rows in set (0.00 sec)
```
* JSON_KEYS(json_doc[, path])
```sql
mysql> SELECT JSON_KEYS('{"a": 1, "b": {"c": 30}}');
+---------------------------------------+
| JSON_KEYS('{"a": 1, "b": {"c": 30}}') |
+---------------------------------------+
| ["a", "b"]                            |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_KEYS('{"a": 1, "b": {"c": 30}}', '$.b');
+----------------------------------------------+
| JSON_KEYS('{"a": 1, "b": {"c": 30}}', '$.b') |
+----------------------------------------------+
| ["c"]                                        |
+----------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_OVERLAPS(json_doc1, json_doc2)
```Sql
mysql> SELECT JSON_OVERLAPS("[1,3,5,7]", "[2,5,7]");
+---------------------------------------+
| JSON_OVERLAPS("[1,3,5,7]", "[2,5,7]") |
+---------------------------------------+
|                                     1 |
+---------------------------------------+
1 row in set (0.00 sec)

mysql>  SELECT JSON_OVERLAPS("[1,3,5,7]", "[2,6,8]");
+---------------------------------------+
| JSON_OVERLAPS("[1,3,5,7]", "[2,6,8]") |
+---------------------------------------+
|                                     0 |
+---------------------------------------+
1 row in set (0.00 sec)
```
* JSON_SEARCH(json_doc, one_or_all, search_str[, escape_char[, path] ...])
```sql
mysql> SET @j = '["abc", [{"k": "10"}, "def"], {"x":"abc"}, {"y":"bcd"}]';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_SEARCH(@j, 'one', 'abc');
+-------------------------------+
| JSON_SEARCH(@j, 'one', 'abc') |
+-------------------------------+
| "$[0]"                        |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_SEARCH(@j, 'all', 'abc');
+-------------------------------+
| JSON_SEARCH(@j, 'all', 'abc') |
+-------------------------------+
| ["$[0]", "$[2].x"]            |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_SEARCH(@j, 'all', 'ghi');
+-------------------------------+
| JSON_SEARCH(@j, 'all', 'ghi') |
+-------------------------------+
| NULL                          |
+-------------------------------+
1 row in set (0.00 sec)
```
* JSON_VALUE(json_doc, path)
```sql
mysql> SELECT JSON_VALUE('{"fname": "Joe", "lname": "Palmer"}', '$.fname');
+--------------------------------------------------------------+
| JSON_VALUE('{"fname": "Joe", "lname": "Palmer"}', '$.fname') |
+--------------------------------------------------------------+
| Joe                                                          |
+--------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_VALUE('{"item": "shoes", "price": "49.95"}', '$.price'
    ->      RETURNING DECIMAL(4,2)) AS price;
+-------+
| price |
+-------+
| 49.95 |
+-------+
1 row in set (0.00 sec)
```
* value MEMBER OF(json_array)
```sql
mysql> SELECT 17 MEMBER OF('[23, "abc", 17, "ab", 10]');
+-------------------------------------------+
| 17 MEMBER OF('[23, "abc", 17, "ab", 10]') |
+-------------------------------------------+
|                                         1 |
+-------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT 'ab' MEMBER OF('[23, "abc", 17, "ab", 10]');
+---------------------------------------------+
| 'ab' MEMBER OF('[23, "abc", 17, "ab", 10]') |
+---------------------------------------------+
|                                           1 |
+---------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT 7 MEMBER OF('[23, "abc", 17, "ab", 10]');
+------------------------------------------+
| 7 MEMBER OF('[23, "abc", 17, "ab", 10]') |
+------------------------------------------+
|                                        0 |
+------------------------------------------+
1 row in set (0.00 sec)
```
### 12.18.4 JSON 値を変更する関数
* JSON_ARRAY_APPEND(json_doc, path, val[, path, val] ...)
```sql
mysql> SET @j = '["a", ["b", "c"], "d"]';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_ARRAY_APPEND(@j, '$[1]', 1);
+----------------------------------+
| JSON_ARRAY_APPEND(@j, '$[1]', 1) |
+----------------------------------+
| ["a", ["b", "c", 1], "d"]        |
+----------------------------------+
1 row in set (0.01 sec)

mysql> SELECT JSON_ARRAY_APPEND(@j, '$[0]', 2);
+----------------------------------+
| JSON_ARRAY_APPEND(@j, '$[0]', 2) |
+----------------------------------+
| [["a", 2], ["b", "c"], "d"]      |
+----------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_ARRAY_APPEND(@j, '$[1][0]', 3);
+-------------------------------------+
| JSON_ARRAY_APPEND(@j, '$[1][0]', 3) |
+-------------------------------------+
| ["a", [["b", 3], "c"], "d"]         |
+-------------------------------------+
1 row in set (0.00 sec)
```
* JSON_ARRAY_INSERT(json_doc, path, val[, path, val] ...)
```sql
mysql> SET @j = '["a", {"b": [1, 2]}, [3, 4]]';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[1]', 'x');
+------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[1]', 'x') |
+------------------------------------+
| ["a", "x", {"b": [1, 2]}, [3, 4]]  |
+------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[100]', 'x');
+--------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[100]', 'x') |
+--------------------------------------+
| ["a", {"b": [1, 2]}, [3, 4], "x"]    |
+--------------------------------------+
1 row in set (0.00 sec)
```
* JSON_INSERT(json_doc, path, val[, path, val] ...)
```sql
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]');
+----------------------------------------------------+
| JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]') |
+----------------------------------------------------+
| {"a": 1, "b": [2, 3], "c": "[true, false]"}        |
+----------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_INSERT(@j, '$.a', 10, '$.c', CAST('[true, false]' AS JSON));
+------------------------------------------------------------------+
| JSON_INSERT(@j, '$.a', 10, '$.c', CAST('[true, false]' AS JSON)) |
+------------------------------------------------------------------+
| {"a": 1, "b": [2, 3], "c": [true, false]}                        |
+------------------------------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_MERGE(json_doc, json_doc[, json_doc] ...)
```Sql
mysql> SELECT JSON_MERGE('[1, 2]', '[true, false]');
+---------------------------------------+
| JSON_MERGE('[1, 2]', '[true, false]') |
+---------------------------------------+
| [1, 2, true, false]                   |
+---------------------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
  Level: Warning
   Code: 1287
Message: 'JSON_MERGE' is deprecated and will be removed in a future release. Please use JSON_MERGE_PRESERVE/JSON_MERGE_PATCH instead
1 row in set (0.00 sec)
```
* JSON_MERGE_PATCH(json_doc, json_doc[, json_doc] ...)
```sql
mysql> SELECT JSON_MERGE_PATCH('[1, 2]', '[true, false]');
+---------------------------------------------+
| JSON_MERGE_PATCH('[1, 2]', '[true, false]') |
+---------------------------------------------+
| [true, false]                               |
+---------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_MERGE_PATCH('{"name": "x"}', '{"id": 47}');
+-------------------------------------------------+
| JSON_MERGE_PATCH('{"name": "x"}', '{"id": 47}') |
+-------------------------------------------------+
| {"id": 47, "name": "x"}                         |
+-------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_MERGE_PATCH('1', 'true');
+-------------------------------+
| JSON_MERGE_PATCH('1', 'true') |
+-------------------------------+
| true                          |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_MERGE_PATCH('{"a":1, "b":2}', '{"b":null}');
+--------------------------------------------------+
| JSON_MERGE_PATCH('{"a":1, "b":2}', '{"b":null}') |
+--------------------------------------------------+
| {"a": 1}                                         |
+--------------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_MERGE_PRESERVE(json_doc, json_doc[, json_doc] ...)
```Sql
mysql> SELECT JSON_MERGE_PRESERVE('[1, 2]', '[true, false]');
+------------------------------------------------+
| JSON_MERGE_PRESERVE('[1, 2]', '[true, false]') |
+------------------------------------------------+
| [1, 2, true, false]                            |
+------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_MERGE_PRESERVE('{"name": "x"}', '{"id": 47}');
+----------------------------------------------------+
| JSON_MERGE_PRESERVE('{"name": "x"}', '{"id": 47}') |
+----------------------------------------------------+
| {"id": 47, "name": "x"}                            |
+----------------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_REMOVE(json_doc, path[, path] ...)
```sql
mysql> SET @j = '["a", ["b", "c"], "d"]';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_REMOVE(@j, '$[1]');
+-------------------------+
| JSON_REMOVE(@j, '$[1]') |
+-------------------------+
| ["a", "d"]              |
+-------------------------+
1 row in set (0.00 sec)
```
* JSON_REPLACE(json_doc, path, val[, path, val] ...)
```sql
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]');
+-----------------------------------------------------+
| JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]') |
+-----------------------------------------------------+
| {"a": 10, "b": [2, 3]}                              |
+-----------------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_SET(json_doc, path, val[, path, val] ...)
```sql
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_SET(@j, '$.a', 10, '$.c', '[true, false]');
+-------------------------------------------------+
| JSON_SET(@j, '$.a', 10, '$.c', '[true, false]') |
+-------------------------------------------------+
| {"a": 10, "b": [2, 3], "c": "[true, false]"}    |
+-------------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_UNQUOTE(json_val)
```sql
mysql> SET @j = '"abc"';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @j, JSON_UNQUOTE(@j);
+-------+------------------+
| @j    | JSON_UNQUOTE(@j) |
+-------+------------------+
| "abc" | abc              |
+-------+------------------+
1 row in set (0.00 sec)
```

### 12.18.5 JSON 値属性を返す関数
* JSON_DEPTH(json_doc)
```sql
mysql> SELECT JSON_DEPTH('{}'), JSON_DEPTH('[]'), JSON_DEPTH('true');
+------------------+------------------+--------------------+
| JSON_DEPTH('{}') | JSON_DEPTH('[]') | JSON_DEPTH('true') |
+------------------+------------------+--------------------+
|                1 |                1 |                  1 |
+------------------+------------------+--------------------+
1 row in set (0.01 sec)

mysql> SELECT JSON_DEPTH('[10, 20]'), JSON_DEPTH('[[], {}]');
+------------------------+------------------------+
| JSON_DEPTH('[10, 20]') | JSON_DEPTH('[[], {}]') |
+------------------------+------------------------+
|                      2 |                      2 |
+------------------------+------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_DEPTH('[10, {"a": 20}]');
+-------------------------------+
| JSON_DEPTH('[10, {"a": 20}]') |
+-------------------------------+
|                             3 |
+-------------------------------+
1 row in set (0.00 sec)

```
* JSON_LENGTH(json_doc[, path])
```sql
mysql> SELECT JSON_LENGTH('[1, 2, {"a": 3}]');
+---------------------------------+
| JSON_LENGTH('[1, 2, {"a": 3}]') |
+---------------------------------+
|                               3 |
+---------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30}}');
+-----------------------------------------+
| JSON_LENGTH('{"a": 1, "b": {"c": 30}}') |
+-----------------------------------------+
|                                       2 |
+-----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30}}', '$.b');
+------------------------------------------------+
| JSON_LENGTH('{"a": 1, "b": {"c": 30}}', '$.b') |
+------------------------------------------------+
|                                              1 |
+------------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_TYPE(json_val)
```sql
mysql> SET @j = '{"a": [10, true]}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_TYPE(@j);
+---------------+
| JSON_TYPE(@j) |
+---------------+
| OBJECT        |
+---------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_TYPE(JSON_EXTRACT(@j, '$.a'));
+------------------------------------+
| JSON_TYPE(JSON_EXTRACT(@j, '$.a')) |
+------------------------------------+
| ARRAY                              |
+------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_TYPE(JSON_EXTRACT(@j, '$.a[0]'));
+---------------------------------------+
| JSON_TYPE(JSON_EXTRACT(@j, '$.a[0]')) |
+---------------------------------------+
| INTEGER                               |
+---------------------------------------+
1 row in set (0.00 sec)
```
* JSON_VALID(val)
```sql

mysql> SELECT JSON_VALID('{"a": 1}');
+------------------------+
| JSON_VALID('{"a": 1}') |
+------------------------+
|                      1 |
+------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_VALID('hello'), JSON_VALID('"hello"');
+---------------------+-----------------------+
| JSON_VALID('hello') | JSON_VALID('"hello"') |
+---------------------+-----------------------+
|                   0 |                     1 |
+---------------------+-----------------------+
1 row in set (0.00 sec)
```
### 12.18.6 JSON テーブル関数
* JSON_TABLE(expr, path COLUMNS (column_list) [AS] alias)
```sql
mysql> SELECT *
    ->        FROM
    ->          JSON_TABLE(
    ->            '[ {"c1": null} ]',
    ->            '$[*]' COLUMNS( c1 INT PATH '$.c1' ERROR ON ERROR )
    ->          ) as jt;
+------+
| c1   |
+------+
| NULL |
+------+
1 row in set (0.01 sec)

mysql> SELECT *
    ->      FROM
    ->        JSON_TABLE(
    ->          '[{"a":"3"},{"a":2},{"b":1},{"a":0},{"a":[1,2]}]',
    ->          "$[*]"
    ->          COLUMNS(
    ->            rowid FOR ORDINALITY,
    ->            ac VARCHAR(100) PATH "$.a" DEFAULT '111' ON EMPTY DEFAULT '999' ON ERROR,
    ->            aj JSON PATH "$.a" DEFAULT '{"x": 333}' ON EMPTY,
    ->            bx INT EXISTS PATH "$.b"
    ->          )
    ->        ) AS tt;
+-------+------+------------+------+
| rowid | ac   | aj         | bx   |
+-------+------+------------+------+
|     1 | 3    | "3"        |    0 |
|     2 | 2    | 2          |    0 |
|     3 | 111  | {"x": 333} |    1 |
|     4 | 0    | 0          |    0 |
|     5 | 999  | [1, 2]     |    0 |
+-------+------+------------+------+
5 rows in set (0.00 sec)

mysql> SELECT *
    ->      FROM
    ->        JSON_TABLE(
    ->          '[{"x":2,"y":"8"},{"x":"3","y":"7"},{"x":"4","y":6}]',
    ->          "$[*]" COLUMNS(
    ->            xval VARCHAR(100) PATH "$.x",
    ->            yval VARCHAR(100) PATH "$.y"
    ->          )
    ->        ) AS  jt1;
+------+------+
| xval | yval |
+------+------+
| 2    | 8    |
| 3    | 7    |
| 4    | 6    |
+------+------+
3 rows in set (0.00 sec)

mysql> SELECT *
    ->      FROM
    ->        JSON_TABLE(
    ->          '[{"a": 1, "b": [11,111]}, {"a": 2, "b": [22,222]}]',
    ->          '$[*]' COLUMNS(
    ->              a INT PATH '$.a',
    ->              NESTED PATH '$.b[*]' COLUMNS (b1 INT PATH '$'),
    ->              NESTED PATH '$.b[*]' COLUMNS (b2 INT PATH '$')
    ->          )
    ->      ) AS jt;
+------+------+------+
| a    | b1   | b2   |
+------+------+------+
|    1 |   11 | NULL |
|    1 |  111 | NULL |
|    1 | NULL |   11 |
|    1 | NULL |  111 |
|    2 |   22 | NULL |
|    2 |  222 | NULL |
|    2 | NULL |   22 |
|    2 | NULL |  222 |
+------+------+------+
8 rows in set (0.00 sec)
```

### 12.18.7 JSON スキーマ検証関数
* JSON_SCHEMA_VALID(schema,document)
```sql
mysql> SET @schema = '{
    '>     '>  "id": "http://json-schema.org/geo",
    ->     '> "$schema": "http://json-schema.org/draft-04/schema#",
    '>     '> "description": "A geographical coordinate",
    ->     '> "type": "object",
    '>     '> "properties": {
    ->     '>   "latitude": {
    '>     '>     "type": "number",
    ->     '>     "minimum": -90,
    '>     '>     "maximum": 90
    ->     '>   },
    '>     '>   "longitude": {
    ->     '>     "type": "number",
    '>     '>     "minimum": -180,
    ->     '>     "maximum": 180
    '>     '>   }
    ->     '> },
    '>     '> "required": ["latitude", "longitude"]
    ->     '>}';^C
mysql> SET @schema = '{
    '>       "id": "http://json-schema.org/geo",
    '>      "$schema": "http://json-schema.org/draft-04/schema#",
    '>      "description": "A geographical coordinate",
    '>      "type": "object",
    '>      "properties": {
    '>        "latitude": {
    '>          "type": "number",
    '>          "minimum": -90,
    '>          "maximum": 90
    '>        },
    '>        "longitude": {
    '>          "type": "number",
    '>          "minimum": -180,
    '>          "maximum": 180
    '>        }
    '>      },
    '>      "required": ["latitude", "longitude"]
    '>     }';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @document = '{
    '>      "latitude": 63.444697,
    '>      "longitude": 10.445118
    '>     }';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_SCHEMA_VALID(@schema, @document);
+---------------------------------------+
| JSON_SCHEMA_VALID(@schema, @document) |
+---------------------------------------+
|                                     1 |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> SET @document = '{}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_SCHEMA_VALID(@schema, @document);
+---------------------------------------+
| JSON_SCHEMA_VALID(@schema, @document) |
+---------------------------------------+
|                                     0 |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> CREATE TABLE geo (
    ->          coordinate JSON,
    ->          CHECK(
    ->              JSON_SCHEMA_VALID(
    ->                  '{
    '>                      "type":"object",
    '>                      "properties":{
    '>                            "latitude":{"type":"number", "minimum":-90, "maximum":90},
    '>                            "longitude":{"type":"number", "minimum":-180, "maximum":180}
    '>                      },
    '>                      "required": ["latitude", "longitude"]
    '>                  }',
    ->                  coordinate
    ->              )
    ->          )
    ->      );
Query OK, 0 rows affected (0.04 sec)

mysql> SET @point1 = '{"latitude":59, "longitude":18}';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @point2 = '{"latitude":91, "longitude":0}';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @point3 = '{"longitude":120}';
Query OK, 0 rows affected (0.00 sec)

mysql>  INSERT INTO geo VALUES(@point1);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO geo VALUES(@point2);
ERROR 3819 (HY000): Check constraint 'geo_chk_1' is violated.

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
  Level: Error
   Code: 3934
Message: The JSON document location '#/latitude' failed requirement 'maximum' at JSON Schema location '#/properties/latitude'.
*************************** 2. row ***************************
  Level: Error
   Code: 3819
Message: Check constraint 'geo_chk_1' is violated.
2 rows in set (0.00 sec)
```
* JSON_SCHEMA_VALIDATION_REPORT(schema,document)
```sql
mysql> SET @schema = '{
    '>       "id": "http://json-schema.org/geo",
    '>      "$schema": "http://json-schema.org/draft-04/schema#",
    '>      "description": "A geographical coordinate",
    '>      "type": "object",
    '>      "properties": {
    '>        "latitude": {
    '>          "type": "number",
    '>          "minimum": -90,
    '>          "maximum": 90
    '>        },
    '>        "longitude": {
    '>          "type": "number",
    '>          "minimum": -180,
    '>          "maximum": 180
    '>        }
    '>      },
    '>      "required": ["latitude", "longitude"]
    '>     }';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @document = '{
    '>      "latitude": 63.444697,
    '>      "longitude": 10.445118
    '>     }';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_SCHEMA_VALIDATION_REPORT(@schema, @document);
+---------------------------------------------------+
| JSON_SCHEMA_VALIDATION_REPORT(@schema, @document) |
+---------------------------------------------------+
| {"valid": true}                                   |
+---------------------------------------------------+
1 row in set (0.00 sec)

mysql> SET @document = '{
    '>      "latitude": 63.444697,
    '>      "longitude": 310.445118
    '>      }';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT JSON_PRETTY(JSON_SCHEMA_VALIDATION_REPORT(@schema, @document))\G
*************************** 1. row ***************************
JSON_PRETTY(JSON_SCHEMA_VALIDATION_REPORT(@schema, @document)): {
  "valid": false,
  "reason": "The JSON document location '#/longitude' failed requirement 'maximum' at JSON Schema location '#/properties/longitude'",
  "schema-location": "#/properties/longitude",
  "document-location": "#/longitude",
  "schema-failed-keyword": "maximum"
}
1 row in set (0.00 sec)
```
### 12.18.8 JSON ユーティリティ関数
* JSON_PRETTY(json_val)
```sql
mysql> SELECT JSON_PRETTY('123');
+--------------------+
| JSON_PRETTY('123') |
+--------------------+
| 123                |
+--------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_PRETTY("[1,3,5]");
+------------------------+
| JSON_PRETTY("[1,3,5]") |
+------------------------+
| [
  1,
  3,
  5
]      |
+------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_PRETTY('{"a":"10","b":"15","x":"25"}');
+---------------------------------------------+
| JSON_PRETTY('{"a":"10","b":"15","x":"25"}') |
+---------------------------------------------+
| {
  "a": "10",
  "b": "15",
  "x": "25"
}   |
+---------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_PRETTY('["a",1,{"key1":
    '>         "value1"},"5",     "77" ,
    '>            {"key2":["value3","valueX",
    '>      "valueY"]},"j", "2"   ]');
+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| JSON_PRETTY('["a",1,{"key1":
        "value1"},"5",     "77" ,
           {"key2":["value3","valueX",
     "valueY"]},"j", "2"   ]')                  |
+-------------------------------------------------------------------------------------------------------------------------------------------------------+
| [
  "a",
  1,
  {
    "key1": "value1"
  },
  "5",
  "77",
  {
    "key2": [
      "value3",
      "valueX",
      "valueY"
    ]
  },
  "j",
  "2"
] |
+-------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
* JSON_STORAGE_FREE(json_val)
```sql
mysql> CREATE TABLE jtable (jcol JSON);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO jtable VALUES ('{"a": 10, "b": "wxyz", "c": "[true, false]"}');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM jtable;
+----------------------------------------------+
| jcol                                         |
+----------------------------------------------+
| {"a": 10, "b": "wxyz", "c": "[true, false]"} |
+----------------------------------------------+
1 row in set (0.00 sec)

mysql> UPDATE jtable SET jcol = JSON_SET(jcol, "$.a", 10, "$.b", "wxyz", "$.c", 1);
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> SELECT * FROM jtable;
+--------------------------------+
| jcol                           |
+--------------------------------+
| {"a": 10, "b": "wxyz", "c": 1} |
+--------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_STORAGE_FREE(jcol) FROM jtable;
+-------------------------+
| JSON_STORAGE_FREE(jcol) |
+-------------------------+
|                      14 |
+-------------------------+
1 row in set (0.00 sec)

mysql> UPDATE jtable SET jcol = JSON_SET(jcol, "$.a", 10, "$.b", "wx", "$.c", 1);
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> SELECT JSON_STORAGE_FREE(jcol) FROM jtable;
+-------------------------+
| JSON_STORAGE_FREE(jcol) |
+-------------------------+
|                      16 |
+-------------------------+
1 row in set (0.00 sec)
```
* JSON_STORAGE_SIZE(json_val)
```sql
mysql> INSERT INTO jtable VALUES ('{"a": 1000, "b": "wxyz", "c": "[1, 3, 5, 7]"}');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT
    ->          jcol,
    ->          JSON_STORAGE_SIZE(jcol) AS Size,
    ->          JSON_STORAGE_FREE(jcol) AS Free
    ->      FROM jtable;
+-----------------------------------------------+------+------+
| jcol                                          | Size | Free |
+-----------------------------------------------+------+------+
| {"a": 1000, "b": "wxyz", "c": "[1, 3, 5, 7]"} |   47 |    0 |
+-----------------------------------------------+------+------+
1 row in set (0.00 sec)

mysql> UPDATE jtable SET jcol = JSON_SET(jcol, "$.b", "a");
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> SELECT
    ->          jcol,
    ->          JSON_STORAGE_SIZE(jcol) AS Size,
    ->          JSON_STORAGE_FREE(jcol) AS Free
    ->      FROM jtable;
+--------------------------------------------+------+------+
| jcol                                       | Size | Free |
+--------------------------------------------+------+------+
| {"a": 1000, "b": "a", "c": "[1, 3, 5, 7]"} |   47 |    3 |
+--------------------------------------------+------+------+
1 row in set (0.00 sec)
```