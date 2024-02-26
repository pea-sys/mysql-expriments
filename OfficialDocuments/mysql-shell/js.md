### 第 19 章 MySQL Shell

MySQL Server 接続

```sh
 MySQL  JS > \connect root@127.0.0.1:33060/test
Creating a session to 'root@127.0.0.1:33060/test'
Please provide the password for 'root@127.0.0.1:33060': **********
Save password for 'root@127.0.0.1:33060'? [Y]es/[N]o/Ne[v]er (default No): y
```

help

```sh
 MySQL  127.0.0.1:33060+ ssl  test  JS >  \help
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
 - os      Gives access to functions which allow to interact with the operating
           system.
 - plugins Plugin to manage MySQL Shell plugins
 - session Represents the currently open MySQL session.
 - shell   Gives access to general purpose functions and properties.
 - sys     Gives access to system specific parameters.
 - util    Global object that groups miscellaneous tools like upgrade checker
           and JSON import.

For additional information on these global objects use: <object>.help()

EXAMPLES
\? AdminAPI
      Displays information about the AdminAPI.

\? \connect
      Displays usage details for the \connect command.

\? checkInstanceConfiguration
      Displays usage details for the dba.checkInstanceConfiguration function.

\? sql syntax
      Displays the main SQL help categories.
```

個別の help

```
MySQL  127.0.0.1:33060+ ssl  test  JS > \help \connect
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
mysql-js> \quit
```

### 20.3.3.1 コレクションの作成、リストおよびドロップ

```
 MySQL  127.0.0.1:33060+ ssl  test  SQL > \js
Switching to JavaScript mode...
 MySQL  127.0.0.1:33060+ ssl  test  JS > db
<Schema:test>
 MySQL  127.0.0.1:33060+ ssl  test  JS > \use world_x
Default schema `world_x` accessible through db.
```

既存のスキーマに新しいコレクションを作成するには、db オブジェクトの createCollection() メソッドを使用します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.createCollection("flags")
<Collection:flags>
```

world_x スキーマ内のすべてのコレクションを表示するには、db オブジェクトの getCollections() メソッドを使用します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.getCollections()
[
    <Collection:countryinfo>,
    <Collection:flags>
]
```

スキーマから既存のコレクションを削除するには、db オブジェクトの dropCollection() メソッドを使用します

```
MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.dropCollection("flags")
```

### 20.3.3.2 コレクションの操作

ドキュメントの追加

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.add(
                                         ->  {
                                         ->     GNP: .6,
                                         ->     IndepYear: 1967,
                                         ->     Name: "Sealand",
                                         ->     Code: "SEA",
                                         ->     demographics: {
                                         ->         LifeExpectancy: 79,
                                         ->         Population: 27
                                         ->     },
                                         ->     geography: {
                                         ->         Continent: "Europe",
                                         ->         Region: "British Islands",
                                         ->         SurfaceArea: 193
                                         ->     },
                                         ->     government: {
                                         ->         GovernmentForm: "Monarchy",
                                         ->         HeadOfState: "Michael Bates"
                                         ->     }
                                         ->   }
                                         -> )
                                         ->
Query OK, 1 item affected (0.0134 sec)
```

このメソッドは、操作のステータスを返します。 ドキュメントを検索して操作を確認できます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find("Name = 'Sealand'")
{
    "GNP": 0.6,
    "_id": "00006593a35c0000000000000001",
    "Code": "SEA",
    "Name": "Sealand",
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
1 document in set (0.0013 sec)
```

### 20.3.3.3 ドキュメントの検索

コレクション内のすべてのドキュメントの検索

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find()
```

フィルタ検索

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find("Name = 'Australia'")
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
1 document in set (0.0015 sec)
```

次のクエリーの「人口」フィールドは、人口統計オブジェクト内に埋め込まれます。 埋込みフィールドにアクセスするには、人口統計と人口間のピリオドを使用して関係を識別します。 ドキュメント名とフィールド名では大文字と小文字が区別されます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find("GNP > 500000 and demographics.Population < 100000000")
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
6 documents in set (0.0013 sec)
```

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find("Name = :country").bind("country", "Italy")
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
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > var myFind = db.countryinfo.find("Name = :country")
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > myFind.bind('country', 'France')
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
1 document in set (0.0011 sec)

 MySQL  127.0.0.1:33060+ ssl  world_x  JS > myFind.bind('country', 'Germany')
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
1 document in set (0.0020 sec)
```

すべてのフィールドを返すかわりに、ドキュメントの特定のフィールドを返すことができます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find("GNP > 5000000").fields(["GNP", "Name"])
{
    "GNP": 8510700,
    "Name": "United States"
}
1 document in set (0.0015 sec)
```

返されるドキュメントを記述する式を使用して、返されるドキュメント (追加、名前変更、ネストおよび新しいフィールド値の計算) を変更できます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find().fields(
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
2 documents in set (0.0025 sec)
```

limit()、sort() および skip() メソッドを適用して、find() メソッドによって返されるドキュメントの数と順序を管理できます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find().limit(2)
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
{
    "GNP": 5976,
    "_id": "00005de917d80000000000000001",
    "Code": "AFG",
    "Name": "Afghanistan",
    "IndepYear": 1919,
    "geography": {
        "Region": "Southern and Central Asia",
        "Continent": "Asia",
        "SurfaceArea": 652090
    },
    "government": {
        "HeadOfState": "Mohammad Omar",
        "GovernmentForm": "Islamic Emirate"
    },
    "demographics": {
        "Population": 22720000,
        "LifeExpectancy": 45.900001525878906
    }
}
2 documents in set (0.0006 sec)
```

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find().sort(["IndepYear desc"]).limit(2)
{
    "GNP": 105,
    "_id": "00005de917d800000000000000a9",
    "Code": "PLW",
    "Name": "Palau",
    "IndepYear": 1994,
    "geography": {
        "Region": "Micronesia",
        "Continent": "Oceania",
        "SurfaceArea": 459
    },
    "government": {
        "HeadOfState": "Kuniwo Nakamura",
        "GovernmentForm": "Republic"
    },
    "demographics": {
        "Population": 19000,
        "LifeExpectancy": 68.5999984741211
    }
}
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
2 documents in set (0.0025 sec)
```

skip() メソッドを使用して、開始ドキュメントを変更できます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find().sort(["IndepYear desc"]).limit(2).skip(1)
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
{
   "GNP": 650,
   "_id": "00005de917d80000000000000040",
   "Code": "ERI",
   "Name": "Eritrea",
   "IndepYear": 1993,
   "geography": {
       "Region": "Eastern Africa",
       "Continent": "Africa",
       "SurfaceArea": 117600
   },
   "government": {
       "HeadOfState": "Isayas Afewerki [Isaias Afwerki]",
       "GovernmentForm": "Republic"
   },
   "demographics": {
       "Population": 3850000,
       "LifeExpectancy": 55.79999923706055
   }
}
2 documents in set (0.0015 sec)
```

### 20.3.3.4 ドキュメントの変更

ドキュメントフィールドの設定および設定解除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.modify("Code = 'SEA'").set(
                                         -> "demographics", {"LifeExpectancy": 78, "Population": 28})
                                         ->
Query OK, 1 item affected (0.0099 sec)

Rows matched: 1  Changed: 1  Warnings: 0
```

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.modify("Name = 'Sealand'").unset("GNP")
Query OK, 1 item affected (0.0063 sec)

Rows matched: 1  Changed: 1  Warnings: 0
```

find() メソッドを使用して変更を確認します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find("Name = 'Sealand'")
{
    "_id": "00006593a35c0000000000000001",
    "Code": "SEA",
    "Name": "Sealand",
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
        "Population": 28,
        "LifeExpectancy": 78
    }
}
1 document in set (0.0012 sec)
```

配列の追加、挿入および削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.modify("true").set("Airports", [])
Query OK, 240 items affected (0.0333 sec)

Rows matched: 240  Changed: 240  Warnings: 0
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.modify("Name = 'France'").arrayAppend("$.Airports", "ORY")
Query OK, 1 item affected (0.0057 sec)

Rows matched: 1  Changed: 1  Warnings: 0
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.find("Name = 'France'")
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

配列内の別の位置に要素を挿入するには、arrayInsert() メソッドを使用して、パス式に挿入するインデックスを指定します。 この場合、インデックスは 0、または配列の最初の要素です

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.modify("Name = 'France'").arrayInsert("$.Airports[0]", "CDG")

Query OK, 1 item affected (0.0056 sec)

Rows matched: 1  Changed: 1  Warnings: 0
```

配列から要素を削除するには、削除する要素のインデックスを arrayDelete() メソッドに渡す必要があります

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.modify("Name = 'France'").arrayDelete("$.Airports[1]")
Query OK, 1 item affected (0.0054 sec)

Rows matched: 1  Changed: 1  Warnings: 0
```

### 20.3.3.5 ドキュメントの削除

条件を使用したドキュメントの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.remove("Code = 'SEA'")
Query OK, 1 item affected (0.0071 sec)
```

最初のドキュメントの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.remove("true").limit(1)
Query OK, 1 item affected (0.0038 sec)
```

オーダー内の最後の文書の削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.remove("true").sort(["Name desc"]).limit(1)
Query OK, 1 item affected (0.0056 sec)
```

### 20.3.3.6 インデックスの作成および削除

一意でないインデックスの追加

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.createIndex("popul", {fields:
                                         -> [{field: '$.demographics.Population', type: 'INTEGER'}]})
                                         ->
Query OK, 0 rows affected (0.0666 sec)
```

一意インデックスの追加

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.createIndex("name",
                                         -> {"fields": [{"field": "$.Name", "type": "TEXT(40)", "required": true}], "unique": true})
                                         ->
Query OK, 0 rows affected (0.1010 sec)
```

インデックスの削除

```
MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.countryinfo.dropIndex("popul")
```

### 20.3.4 リレーショナルテーブル

スキーマの確認

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db
<Schema:world_x>
```

すべてのテーブルの表示

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.getTables()
[
    <Table:city>,
    <Table:country>,
    <Table:countrylanguage>
]
```

### 20.3.4.1 テーブルへのレコードの挿入

完全なレコードの挿入

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.insert("ID", "Name", "CountryCode", "District", "Info").values(
                                         -> null, "Olympia", "USA", "Washington", '{"Population": 5000}')
                                         ->
Query OK, 1 item affected (0.0051 sec)

 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.insert("ID", "Name", "CountryCode").values(
                                         -> null, "Little Falls", "USA").values(null, "Happy Valley", "USA")
                                         ->
Query OK, 2 items affected (0.0027 sec)

Records: 2  Duplicates: 0  Warnings: 0
```

### 20.3.4.2 テーブルの選択

レコードの選択

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.select().limit(2)
+----+----------+-------------+----------+-------------------------+
| ID | Name     | CountryCode | District | Info                    |
+----+----------+-------------+----------+-------------------------+
|  1 | Kabul    | AFG         | Kabol    | {"Population": 1780000} |
|  2 | Qandahar | AFG         | Qandahar | {"Population": 237500}  |
+----+----------+-------------+----------+-------------------------+
2 rows in set (0.0009 sec)
```

フィルタ検索

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.select(["Name", "CountryCode"]).limit(2)
+----------+-------------+
| Name     | CountryCode |
+----------+-------------+
| Kabul    | AFG         |
| Qandahar | AFG         |
+----------+-------------+
2 rows in set (0.0007 sec)

 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.select(["Name", "CountryCode"]).where("Name like 'Z%'").limit(3)
+------------+-------------+
| Name       | CountryCode |
+------------+-------------+
| Zaanstad   | NLD         |
| Zoetermeer | NLD         |
| Zwolle     | NLD         |
+------------+-------------+
3 rows in set (0.0007 sec)
```

bind() メソッドを使用して、値を検索条件から区切ることができます

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.select(["Name", "CountryCode"]).
                                         ->               where("Name like :name").bind("name", "Z%")
                                         ->
+-----------------+-------------+
| Name            | CountryCode |
+-----------------+-------------+
| Zaanstad        | NLD         |
| Zoetermeer      | NLD         |
| Zwolle          | NLD         |
| Zenica          | BIH         |
| Zagazig         | EGY         |
| Zaragoza        | ESP         |
| Zamboanga       | PHL         |
| Zahedan         | IRN         |
| Zanjan          | IRN         |
| Zabol           | IRN         |
| Zama            | JPN         |
| Zhezqazghan     | KAZ         |
| Zhengzhou       | CHN         |
| Zibo            | CHN         |
| Zhangjiakou     | CHN         |
| Zhuzhou         | CHN         |
| Zhangjiang      | CHN         |
| Zigong          | CHN         |
| Zaozhuang       | CHN         |
| Zhenjiang       | CHN         |
| Zhongshan       | CHN         |
| Zunyi           | CHN         |
| Zhaoqing        | CHN         |
| Zhangzhou       | CHN         |
| Zhaodong        | CHN         |
| Zhuhai          | CHN         |
| Zaoyang         | CHN         |
| Zhoushan        | CHN         |
| Zhoukou         | CHN         |
| Zalantun        | CHN         |
| Zhumadian       | CHN         |
| Zixing          | CHN         |
| Zhucheng        | CHN         |
| Zhangjiagang    | CHN         |
| Zagreb          | HRV         |
| Zapopan         | MEX         |
| Zamora          | MEX         |
| Zitácuaro       | MEX         |
| Zacatecas       | MEX         |
| Zinacantepec    | MEX         |
| Zumpango        | MEX         |
| Zinder          | NER         |
| Zaria           | NGA         |
| Zabrze          | POL         |
| Zielona Góra    | POL         |
| Zwickau         | DEU         |
| Ziguinchor      | SEN         |
| Zürich          | CHE         |
| Zanzibar        | TZA         |
| Zonguldak       | TUR         |
| Zaporizzja      | UKR         |
| Zytomyr         | UKR         |
| Zelenograd      | RUS         |
| Zlatoust        | RUS         |
| Zelenodolsk     | RUS         |
| Zeleznodoroznyi | RUS         |
| Zeleznogorsk    | RUS         |
| Zukovski        | RUS         |
| Zeleznogorsk    | RUS         |
+-----------------+-------------+
59 rows in set (0.0026 sec)
```

複数の条件演算子を指定するには、検索条件をカッコで囲んで演算子の優先順位を変更します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.select(["Name", "CountryCode"]).
                                         -> where("Name like 'Z%' and (CountryCode = 'CHN' or CountryCode = 'RUS')")
                                         ->
+-----------------+-------------+
| Name            | CountryCode |
+-----------------+-------------+
| Zhengzhou       | CHN         |
| Zibo            | CHN         |
| Zhangjiakou     | CHN         |
| Zhuzhou         | CHN         |
| Zhangjiang      | CHN         |
| Zigong          | CHN         |
| Zaozhuang       | CHN         |
| Zhenjiang       | CHN         |
| Zhongshan       | CHN         |
| Zunyi           | CHN         |
| Zhaoqing        | CHN         |
| Zhangzhou       | CHN         |
| Zhaodong        | CHN         |
| Zhuhai          | CHN         |
| Zaoyang         | CHN         |
| Zhoushan        | CHN         |
| Zhoukou         | CHN         |
| Zalantun        | CHN         |
| Zhumadian       | CHN         |
| Zixing          | CHN         |
| Zhucheng        | CHN         |
| Zhangjiagang    | CHN         |
| Zelenograd      | RUS         |
| Zlatoust        | RUS         |
| Zelenodolsk     | RUS         |
| Zeleznodoroznyi | RUS         |
| Zeleznogorsk    | RUS         |
| Zukovski        | RUS         |
| Zeleznogorsk    | RUS         |
+-----------------+-------------+
29 rows in set (0.0027 sec)
```

制限、順序およびオフセットの結果

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.country.select(["Code", "Name"]).orderBy(["Name desc"]).limit(3).offset(1)
+------+------------+
| Code | Name       |
+------+------------+
| ZMB  | Zambia     |
| YUG  | Yugoslavia |
| YEM  | Yemen      |
+------+------------+
3 rows in set (0.0013 sec)
```

### 20.3.4.3 テーブルの更新

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.update().set("Name", "Beijing").where("Name = 'Peking'")
Query OK, 1 item affected (0.0135 sec)

Rows matched: 1  Changed: 1  Warnings: 0
```

select() メソッドを使用して変更を確認します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.select(["ID", "Name", "CountryCode", "District", "Info"]).where("Name = 'Beijing'")
+------+---------+-------------+----------+-------------------------+
| ID   | Name    | CountryCode | District | Info                    |
+------+---------+-------------+----------+-------------------------+
| 1891 | Beijing | CHN         | Peking   | {"Population": 7472000} |
+------+---------+-------------+----------+-------------------------+
1 row in set (0.0070 sec)
```

### 20.3.4.4 テーブルの削除

条件を使用したレコードの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.delete().where("Name = 'Olympia'")
Query OK, 1 item affected (0.0129 sec)
```

テーブル内のすべてのレコードの削除

```
 MySQL  127.0.0.1:33060+ ssl  world_x  JS > db.city.delete().limit(1)
Query OK, 1 item affected (0.0042 sec)
```
