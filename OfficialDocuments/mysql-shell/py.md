### 20.4 Python クイックスタートガイド: ドキュメントストア用の MySQL Shell

MySQL Shell の起動

```
C:\Users\masami>mysqlsh root@127.0.0.1:33060/world_x
MySQL Shell 8.2.1

Copyright (c) 2016, 2023, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its affiliates.
Other names may be trademarks of their respective owners.

Type '\help' or '\?' for help; '\quit' to exit.
Creating a session to 'root@127.0.0.1:33060/world_x'
Fetching schema names for auto-completion... Press ^C to stop.
Your MySQL connection id is 21 (X protocol)
Server version: 8.2.0 MySQL Community Server - GPL
Default schema `world_x` accessible through db.
 MySQL  127.0.0.1:33060+ ssl  world_x  JS >
```

Python モードへ切り替え

```
MySQL  127.0.0.1:33060+ ssl  world_x  JS > \py
Switching to Python mode...
MySQL  127.0.0.1:33060+ ssl  world_x  Py >
```

help

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > \help
The Shell Help is organized in categories and topics. To get help for a
specific category or topic use: \? <pattern>

The <pattern> argument should be the name of a category or a topic.

The pattern is a filter to identify topics for which help is required, it can
use the following wildcards:

- ? matches any single character.
- * matches any character sequence.

The following are the main help categories:

 - AdminAPI       The AdminAPI is an API that enables configuring and managing
                  InnoDB Clusters, ReplicaSets, ClusterSets, among other
                  things.
 - Shell Commands Provides details about the available built-in shell commands.
 - ShellAPI       Contains information about the shell and util global objects
                  as well as the mysql module that enables executing SQL on
                  MySQL Servers.
 - SQL Syntax     Entry point to retrieve syntax help on SQL statements.
 - X DevAPI       Details the mysqlx module as well as the capabilities of the
                  X DevAPI which enable working with MySQL as a Document Store

The available topics include:

- The dba global object and the classes available at the AdminAPI.
- The mysqlx module and the classes available at the X DevAPI.
- The mysql module and the global objects and classes available at the
  ShellAPI.
- The functions and properties of the classes exposed by the APIs.
- The available shell commands.
- Any word that is part of an SQL statement.
- Command Line - invoking built-in shell functions without entering interactive
  mode.

SHELL COMMANDS

The shell commands allow executing specific operations including updating the
shell configuration.

The following shell commands are available:

 - \                   Start multi-line input when in SQL mode.
 - \connect    (\c)    Connects the shell to a MySQL server and assigns the
                       global session.
 - \disconnect         Disconnects the global session.
 - \edit       (\e)    Launch a system editor to edit a command to be executed.
 - \exit               Exits the MySQL Shell, same as \quit.
 - \help       (\?,\h) Prints help information about a specific topic.
 - \history            View and edit command line history.
 - \js                 Switches to JavaScript processing mode.
 - \nopager            Disables the current pager.
 - \nowarnings (\w)    Don't show warnings after every statement.
 - \option             Allows working with the available shell options.
 - \pager      (\P)    Sets the current pager.
 - \py                 Switches to Python processing mode.
 - \quit       (\q)    Exits the MySQL Shell.
 - \reconnect          Reconnects the global session.
 - \rehash             Refresh the autocompletion cache.
 - \show               Executes the given report with provided options and
                       arguments.
 - \source     (\.)    Loads and executes a script from a file.
 - \sql                Executes SQL statement or switches to SQL processing
                       mode when no statement is given.
 - \status     (\s)    Print information about the current global session.
 - \system     (\!)    Execute a system shell command.
 - \use        (\u)    Sets the active schema.
 - \warnings   (\W)    Show warnings after every statement.
 - \watch              Executes the given report with provided options and
                       arguments in a loop.

GLOBAL OBJECTS

The following modules and objects are ready for use when the shell starts:

 - db      Used to work with database schema objects.
 - dba     Used for InnoDB Cluster, ReplicaSet, and ClusterSet administration.
 - mysql   Support for connecting to MySQL servers using the classic MySQL
           protocol.
 - mysqlx  Used to work with X Protocol sessions using the MySQL X DevAPI.
 - plugins Plugin to manage MySQL Shell plugins
 - session Represents the currently open MySQL session.
 - shell   Gives access to general purpose functions and properties.
 - util    Global object that groups miscellaneous tools like upgrade checker
           and JSON import.

For additional information on these global objects use: <object>.help()

EXAMPLES
\? AdminAPI
      Displays information about the AdminAPI.

\? \connect
      Displays usage details for the \connect command.

\? check_instance_configuration
      Displays usage details for the dba.check_instance_configuration function.

\? sql syntax
      Displays the main SQL help categories.
```

個別の help

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > \help \connect
NAME
      \connect - Connects the shell to a MySQL server and assigns the global
      session.

SYNTAX
      \connect [<TYPE>] <URI>
      \c [<TYPE>] <URI>

DESCRIPTION
      TYPE is an optional parameter to specify the session type. Accepts the
      following values:

      - --mc, --mysql: create a classic MySQL protocol session (default port
        3306)
      - --mx, --mysqlx: create an X protocol session (default port 33060)
      - --ssh <SSHURI>: create an SSH tunnel to use as a gateway for db
        connection. This requires that db port is specified in advance.

      If TYPE is omitted, automatic protocol detection is done, unless the
      protocol is given in the URI.

      URI and SSHURI format is: [user[:password]@]hostname[:port]

EXAMPLE
      \connect --mx root@localhost
            Creates a global session using the X protocol to the indicated URI.
```

MySQL Shell の終了

```
MySQL  127.0.0.1:33060+ ssl  world_x  Py > \quit
Bye!
```

### 20.4.2 world_x データベースのダウンロードおよびインポート

```sql
 MySQL  127.0.0.1:33060+ ssl  world_x  SQL > \source C:/Users/masami/Desktop/world_x-db/world_x-db/world_x.sql
```

### 20.4.3.1 コレクションの作成、リストおよびドロップ

スキーマの確認

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db
<Schema:world_x>
```

コレクションの作成

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.create_collection("flags")
<Collection:flags>
```

コレクションのリスト

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.get_collections()
[
    <Collection:countryinfo>,
    <Collection:flags>
]
```

コレクションのドロップ

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.drop_collection("flags")
```

### 20.4.3.2 コレクションの操作

ドキュメントの追加

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.add(
                                        ->  {
                                        ->     "GNP": .6,
                                        ->     "IndepYear": 1967,
                                        ->     "Name": "Sealand",
                                        ->     "Code:": "SEA",
                                        ->     "demographics": {
                                        ->         "LifeExpectancy": 79,
                                        ->         "Population": 27
                                        ->     },
                                        ->     "geography": {
                                        ->         "Continent": "Europe",
                                        ->         "Region": "British Islands",
                                        ->         "SurfaceArea": 193
                                        ->     },
                                        ->     "government": {
                                        ->         "GovernmentForm": "Monarchy",
                                        ->         "HeadOfState": "Michael Bates"
                                        ->     }
                                        ->   }
                                        -> )
                                        ->
Query OK, 1 item affected (0.0640 sec)
```

このメソッドは、操作のステータスを返します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find("Name = 'Sealand'")
{
    "GNP": 0.6,
    "_id": "00006593a35c0000000000000002",
    "Name": "Sealand",
    "Code:": "SEA",
    "IndepYear": 1967,
    "geography": {
        "Region": "British Islands",
        "Continent": "Europe",
        "SurfaceArea": 193
    },
    "government": {
        "HeadOfState": "Michael Bates",
        "GovernmentForm": "Monarchy"
    },
    "demographics": {
        "Population": 27,
        "LifeExpectancy": 79
    }
}
1 document in set (0.0081 sec)
```

### 20.4.3.3 ドキュメントの検索

フィルタ検索

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find("Name = 'Australia'")
{
    "GNP": 351182,
    "_id": "00005de917d8000000000000000e",
    "Code": "AUS",
    "Name": "Australia",
    "IndepYear": 1901,
    "geography": {
        "Region": "Australia and New Zealand",
        "Continent": "Oceania",
        "SurfaceArea": 7741220
    },
    "government": {
        "HeadOfState": "Elizabeth II",
        "GovernmentForm": "Constitutional Monarchy, Federation"
    },
    "demographics": {
        "Population": 18886000,
        "LifeExpectancy": 79.80000305175781
    }
}
1 document in set (0.0012 sec)
```

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find("GNP > 500000 and demographics.Population < 100000000")
{
    "GNP": 598862,
    "_id": "00005de917d80000000000000025",
    "Code": "CAN",
    "Name": "Canada",
    "IndepYear": 1867,
    "geography": {
        "Region": "North America",
        "Continent": "North America",
        "SurfaceArea": 9970610
    },
    "government": {
        "HeadOfState": "Elizabeth II",
        "GovernmentForm": "Constitutional Monarchy, Federation"
    },
    "demographics": {
        "Population": 31147000,
        "LifeExpectancy": 79.4000015258789
    }
}
{
    "GNP": 2133367,
    "_id": "00005de917d80000000000000038",
    "Code": "DEU",
    "Name": "Germany",
    "IndepYear": 1955,
    "geography": {
        "Region": "Western Europe",
        "Continent": "Europe",
        "SurfaceArea": 357022
    },
    "government": {
        "HeadOfState": "Johannes Rau",
        "GovernmentForm": "Federal Republic"
    },
    "demographics": {
        "Population": 82164700,
        "LifeExpectancy": 77.4000015258789
    }
}
{
    "GNP": 553233,
    "_id": "00005de917d80000000000000042",
    "Code": "ESP",
    "Name": "Spain",
    "IndepYear": 1492,
    "geography": {
        "Region": "Southern Europe",
        "Continent": "Europe",
        "SurfaceArea": 505992
    },
    "government": {
        "HeadOfState": "Juan Carlos I",
        "GovernmentForm": "Constitutional Monarchy"
    },
    "demographics": {
        "Population": 39441700,
        "LifeExpectancy": 78.80000305175781
    }
}
{
    "GNP": 1424285,
    "_id": "00005de917d80000000000000048",
    "Code": "FRA",
    "Name": "France",
    "IndepYear": 843,
    "geography": {
        "Region": "Western Europe",
        "Continent": "Europe",
        "SurfaceArea": 551500
    },
    "government": {
        "HeadOfState": "Jacques Chirac",
        "GovernmentForm": "Republic"
    },
    "demographics": {
        "Population": 59225700,
        "LifeExpectancy": 78.80000305175781
    }
}
{
    "GNP": 1378330,
    "_id": "00005de917d8000000000000004c",
    "Code": "GBR",
    "Name": "United Kingdom",
    "IndepYear": 1066,
    "geography": {
        "Region": "British Islands",
        "Continent": "Europe",
        "SurfaceArea": 242900
    },
    "government": {
        "HeadOfState": "Elizabeth II",
        "GovernmentForm": "Constitutional Monarchy"
    },
    "demographics": {
        "Population": 59623400,
        "LifeExpectancy": 77.69999694824219
    }
}
{
    "GNP": 1161755,
    "_id": "00005de917d8000000000000006a",
    "Code": "ITA",
    "Name": "Italy",
    "IndepYear": 1861,
    "geography": {
        "Region": "Southern Europe",
        "Continent": "Europe",
        "SurfaceArea": 301316
    },
    "government": {
        "HeadOfState": "Carlo Azeglio Ciampi",
        "GovernmentForm": "Republic"
    },
    "demographics": {
        "Population": 57680000,
        "LifeExpectancy": 79
    }
}
6 documents in set (0.0025 sec)
```

bind() メソッドを使用して、値を検索条件から区切ることができます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find("Name = :country").bind("country", "Italy")
{
    "GNP": 1161755,
    "_id": "00005de917d8000000000000006a",
    "Code": "ITA",
    "Name": "Italy",
    "IndepYear": 1861,
    "geography": {
        "Region": "Southern Europe",
        "Continent": "Europe",
        "SurfaceArea": 301316
    },
    "government": {
        "HeadOfState": "Carlo Azeglio Ciampi",
        "GovernmentForm": "Republic"
    },
    "demographics": {
        "Population": 57680000,
        "LifeExpectancy": 79
    }
}
1 document in set (0.0011 sec)
```

プレースホルダおよび bind() メソッドを使用して保存済検索を作成し、別の値でコールできます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > myFind = db.countryinfo.find("Name = :country")
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > myFind.bind('country', 'France')
{
    "GNP": 1424285,
    "_id": "00005de917d80000000000000048",
    "Code": "FRA",
    "Name": "France",
    "IndepYear": 843,
    "geography": {
        "Region": "Western Europe",
        "Continent": "Europe",
        "SurfaceArea": 551500
    },
    "government": {
        "HeadOfState": "Jacques Chirac",
        "GovernmentForm": "Republic"
    },
    "demographics": {
        "Population": 59225700,
        "LifeExpectancy": 78.80000305175781
    }
}
1 document in set (0.0014 sec)

 MySQL  127.0.0.1:33060+ ssl  world_x  Py > myFind.bind('country', 'Germany')
{
    "GNP": 2133367,
    "_id": "00005de917d80000000000000038",
    "Code": "DEU",
    "Name": "Germany",
    "IndepYear": 1955,
    "geography": {
        "Region": "Western Europe",
        "Continent": "Europe",
        "SurfaceArea": 357022
    },
    "government": {
        "HeadOfState": "Johannes Rau",
        "GovernmentForm": "Federal Republic"
    },
    "demographics": {
        "Population": 82164700,
        "LifeExpectancy": 77.4000015258789
    }
}
1 document in set (0.0035 sec)
```

すべてのフィールドを返すかわりに、ドキュメントの特定のフィールドを返すことができます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find("GNP > 5000000").fields(["GNP", "Name"])
{
    "GNP": 8510700,
    "Name": "United States"
}
1 document in set (0.0024 sec)
```

また、返されるドキュメントを記述する式を使用して、返されるドキュメント (追加、名前変更、ネストおよび新しいフィールド値の計算) を変更できます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find().fields(
                                         -> mysqlx.expr('{"Name": upper(Name), "GNPPerCapita": GNP*1000000/demographics.Population}')).limit(2)
                                         ->
{
    "Name": "ARUBA",
    "GNPPerCapita": 8038.834951456311
}
{
    "Name": "AFGHANISTAN",
    "GNPPerCapita": 263.0281690140845
}
2 documents in set (0.0034 sec)
```

結果の制限、ソートおよびスキップ

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find().limit(1)
{
    "GNP": 828,
    "_id": "00005de917d80000000000000000",
    "Code": "ABW",
    "Name": "Aruba",
    "IndepYear": null,
    "geography": {
        "Region": "Caribbean",
        "Continent": "North America",
        "SurfaceArea": 193
    },
    "government": {
        "HeadOfState": "Beatrix",
        "GovernmentForm": "Nonmetropolitan Territory of The Netherlands"
    },
    "demographics": {
        "Population": 103000,
        "LifeExpectancy": 78.4000015258789
    }
}
1 document in set (0.0008 sec)
```

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find().sort(["IndepYear desc"]).limit(1).skip(1)
{
    "GNP": 55017,
    "_id": "00005de917d80000000000000037",
    "Code": "CZE",
    "Name": "Czech Republic",
    "IndepYear": 1993,
    "geography": {
        "Region": "Eastern Europe",
        "Continent": "Europe",
        "SurfaceArea": 78866
    },
    "government": {
        "HeadOfState": "Václav Havel",
        "GovernmentForm": "Republic"
    },
    "demographics": {
        "Population": 10278100,
        "LifeExpectancy": 74.5
    }
}
1 document in set (0.0041 sec)
```

### 20.4.3.4 ドキュメントの変更

ドキュメントフィールドの設定および設定解除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.modify("Code = 'SEA'").set(
                                         -> "demographics", {"LifeExpectancy": 78, "Population": 28})
                                         ->
Query OK, 0 items affected (0.0060 sec)

Rows matched: 0  Changed: 0  Warnings: 0
```

次のクエリーは、検索条件に一致するドキュメントから GNP を削除します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.modify("Name = 'Sealand'").unset("GNP")
Query OK, 1 item affected (0.0097 sec)

Rows matched: 1  Changed: 1  Warnings: 0
```

find() メソッドを使用して変更を確認します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find("Name = 'Sealand'")
{
    "_id": "00006593a35c0000000000000002",
    "Name": "Sealand",
    "Code:": "SEA",
    "IndepYear": 1967,
    "geography": {
        "Region": "British Islands",
        "Continent": "Europe",
        "SurfaceArea": 193
    },
    "government": {
        "HeadOfState": "Michael Bates",
        "GovernmentForm": "Monarchy"
    },
    "demographics": {
        "Population": 27,
        "LifeExpectancy": 79
    }
}
1 document in set (0.0012 sec)
```

配列の追加、挿入および削除  
最初の例では、modify() および set() メソッドを使用して、すべてのドキュメントに新しい空港フィールドを作成します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.modify("true").set("Airports", [])
Query OK, 240 items affected (0.0469 sec)

Rows matched: 240  Changed: 240  Warnings: 0
```

空港フィールドが追加された状態で、次の例では、array_append() メソッドを使用して新しい空港をいずれかのドキュメントに追加します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.modify("Name = 'France'").array_append("$.Airports", "ORY")
Query OK, 1 item affected (0.0062 sec)

Rows matched: 1  Changed: 1  Warnings: 0
```

find() を使用して変更を確認します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find("Name = 'France'")
{
    "GNP": 1424285,
    "_id": "00005de917d80000000000000048",
    "Code": "FRA",
    "Name": "France",
    "Airports": [
        "ORY"
    ],
    "IndepYear": 843,
    "geography": {
        "Region": "Western Europe",
        "Continent": "Europe",
        "SurfaceArea": 551500
    },
    "government": {
        "HeadOfState": "Jacques Chirac",
        "GovernmentForm": "Republic"
    },
    "demographics": {
        "Population": 59225700,
        "LifeExpectancy": 78.80000305175781
    }
}
1 document in set (0.0011 sec)
```

配列内の別の位置に要素を挿入するには、array_insert() メソッドを使用して、パス式に挿入するインデックスを指定します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.modify("Name = 'France'").array_insert("$.Airports[0]", "CDG")
Query OK, 1 item affected (0.0060 sec)

Rows matched: 1  Changed: 1  Warnings: 0
```

### 20.4.3.5 ドキュメントの削除

条件を使用したドキュメントの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.remove("Code = 'SEA'")
Query OK, 0 items affected (0.0036 sec)
```

最初のドキュメントの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.remove("true").limit(1)
Query OK, 1 item affected (0.0038 sec)
```

オーダー内の最後の文書の削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.remove("true").sort(["Name desc"]).limit(1)
Query OK, 1 item affected (0.0054 sec)
```

### 20.4.3.6 インデックスの作成および削除

一意インデックスの追加

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.create_index("name",
                                         -> {"fields": [{"field": "$.Name", "type": "TEXT(40)", "required": True}], "unique": True})
                                         ->
Query OK, 0 rows affected (0.1665 sec)
```

インデックスの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.drop_index("popul")
```

### 20.4.4 リレーショナルテーブル

すべてのテーブルの表示

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.get_tables()
[
    <Table:city>,
    <Table:country>,
    <Table:countrylanguage>
]
```

### 20.4.4.1 テーブルへのレコードの挿入

完全なレコードの挿入

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.insert("ID", "Name", "CountryCode", "District", "Info").values(
                                         -> None, "Olympia", "USA", "Washington", '{"Population": 5000}')
                                         ->
Query OK, 1 item affected (0.0048 sec)
```

部分レコードの挿入

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.insert("ID", "Name", "CountryCode").values(
                                         -> None, "Little Falls", "USA").values(None, "Happy Valley", "USA")
                                         ->
Query OK, 2 items affected (0.0046 sec)

Records: 2  Duplicates: 0  Warnings: 0

```

### 20.4.4.2 テーブルの選択

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.select().limit(5)
+----+----------------+-------------+---------------+-------------------------+
| ID | Name           | CountryCode | District      | Info                    |
+----+----------------+-------------+---------------+-------------------------+
|  1 | Kabul          | AFG         | Kabol         | {"Population": 1780000} |
|  2 | Qandahar       | AFG         | Qandahar      | {"Population": 237500}  |
|  3 | Herat          | AFG         | Herat         | {"Population": 186800}  |
|  4 | Mazar-e-Sharif | AFG         | Balkh         | {"Population": 127800}  |
|  5 | Amsterdam      | NLD         | Noord-Holland | {"Population": 731200}  |
+----+----------------+-------------+---------------+-------------------------+
5 rows in set (0.0009 sec)
```

フィルタ検索

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.select(["Name", "CountryCode"]).limit(5)
+----------------+-------------+
| Name           | CountryCode |
+----------------+-------------+
| Kabul          | AFG         |
| Qandahar       | AFG         |
| Herat          | AFG         |
| Mazar-e-Sharif | AFG         |
| Amsterdam      | NLD         |
+----------------+-------------+
5 rows in set (0.0007 sec)
```

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.select(["Name", "CountryCode"]).where(
                                         -> "Name like :name").bind("name", "Z%").limit(5)
                                         ->
+------------+-------------+
| Name       | CountryCode |
+------------+-------------+
| Zaanstad   | NLD         |
| Zoetermeer | NLD         |
| Zwolle     | NLD         |
| Zenica     | BIH         |
| Zagazig    | EGY         |
+------------+-------------+
5 rows in set (0.0010 sec)
```

AND 演算子を使用してクエリーを発行するには、where() メソッドの検索条件の間に演算子を追加します

````
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.select(["Name", "CountryCode"]).where(
                                         -> "Name like 'Z%' and CountryCode = 'CHN'").limit(2)
                                         ->
+-----------+-------------+
| Name      | CountryCode |
+-----------+-------------+
| Zhengzhou | CHN         |
| Zibo      | CHN         |
+-----------+-------------+
2 rows in set (0.0021 sec)```
````

複数の条件演算子を指定するには、検索条件をカッコで囲んで演算子の優先順位を変更します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.select(["Name", "CountryCode"]).where(
                                         -> "Name like 'Z%' and (CountryCode = 'CHN' or CountryCode = 'RUS')").limit(2)
                                         ->
+-----------+-------------+
| Name      | CountryCode |
+-----------+-------------+
| Zhengzhou | CHN         |
| Zibo      | CHN         |
+-----------+-------------+
2 rows in set (0.0017 sec)
```

制限、順序およびオフセットの結果

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.country.select(["Code", "Name"]).limit(3)
+------+-------------+
| Code | Name        |
+------+-------------+
| ABW  | Aruba       |
| AFG  | Afghanistan |
| AGO  | Angola      |
+------+-------------+
3 rows in set (0.0026 sec)
```

次のクエリーでは、すべてのレコードが名前カラムでソートされ、最初の 3 つのレコードが降順で返されます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.country.select(["Code", "Name"]).order_by(["Name desc"]).limit(3)
+------+------------+
| Code | Name       |
+------+------------+
| ZWE  | Zimbabwe   |
| ZMB  | Zambia     |
| YUG  | Yugoslavia |
+------+------------+
3 rows in set (0.0009 sec)
```

### 20.4.4.3 テーブルの更新

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.update().set("Name", "Beijing").where("Name = 'Peking'")
Query OK, 1 item affected (0.0146 sec)

Rows matched: 1  Changed: 1  Warnings: 0
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.select(["ID", "Name", "CountryCode", "District", "Info"]).where("Name = 'Beijing'")
+------+---------+-------------+----------+-------------------------+
| ID   | Name    | CountryCode | District | Info                    |
+------+---------+-------------+----------+-------------------------+
| 1891 | Beijing | CHN         | Peking   | {"Population": 7472000} |
+------+---------+-------------+----------+-------------------------+
1 row in set (0.0082 sec)
```

### 20.4.4.4 テーブルの削除

条件を使用したレコードの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.delete().where("Name = 'Olympia'")
Query OK, 1 item affected (0.0137 sec)
```

最初のレコードの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.delete().limit(1)
Query OK, 1 item affected (0.0030 sec)
```

### 20.4.5 テーブル内のドキュメント

レコードの挿入

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.insert().values(
                                         -> None, "San Francisco", "USA", "California", '{"Population":830000}')
                                         ->
Query OK, 1 item affected (0.0039 sec)

```

レコードの選択

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.city.select(["ID", "Name", "CountryCode", "District", "Info"]).where(
                                         -> "CountryCode = :country and Info->'$.Population' > 1000000").bind(
                                         -> 'country', 'USA')
                                         ->
+------+--------------+-------------+--------------+-------------------------+
| ID   | Name         | CountryCode | District     | Info                    |
+------+--------------+-------------+--------------+-------------------------+
| 3793 | New York     | USA         | New York     | {"Population": 8008278} |
| 3794 | Los Angeles  | USA         | California   | {"Population": 3694820} |
| 3795 | Chicago      | USA         | Illinois     | {"Population": 2896016} |
| 3796 | Houston      | USA         | Texas        | {"Population": 1953631} |
| 3797 | Philadelphia | USA         | Pennsylvania | {"Population": 1517550} |
| 3798 | Phoenix      | USA         | Arizona      | {"Population": 1321045} |
| 3799 | San Diego    | USA         | California   | {"Population": 1223400} |
| 3800 | Dallas       | USA         | Texas        | {"Population": 1188580} |
| 3801 | San Antonio  | USA         | Texas        | {"Population": 1144646} |
+------+--------------+-------------+--------------+-------------------------+
9 rows in set (0.0089 sec)
```
