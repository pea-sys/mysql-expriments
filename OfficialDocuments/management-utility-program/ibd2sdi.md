## 4.6.1 ibd2sdi — InnoDB テーブルスペース SDI 抽出ユーティリティ
ibd2sdi は、InnoDB テーブルスペースファイルから serialized dictionary information (SDI) を抽出するためのユーティリティです。
※今のところ使い道は分かっていないが、メタ情報を引っ張ってくるのに便利そう

* --dump-file=, -d:シリアル化されたディクショナリ情報 (SDI) を指定されたダンプファイルにダンプします。 
```
C:\Users\masami> ibd2sdi --dump-file=sample.sdi "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
```
ファイルの中身はjsonっぽい
```
["ibd2sdi"
,
{
	"type": 1,
	"id": 618,
	"object":
		{
    "mysqld_version_id": 80200,
    "dd_version": 80200,
    "sdi_version": 80019,
    "dd_object_type": "Table",
    "dd_object": {
・・・
```
* --skip-data, -s:シリアライズされたディクショナリ情報 (SDI) からの data フィールド値の取得をスキップし、SDI レコードの主キーである id および type フィールド値のみを取得します。
```
C:\Users\masami> ibd2sdi --skip-data "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
["ibd2sdi"
,
{
        "type": 1,
        "id": 618
}
,
{
        "type": 2,
        "id": 244
}
]
```
* --id=#, -i #:指定されたテーブルまたはテーブルスペースオブジェクト ID に一致するシリアライズディクショナリ情報 (SDI) を取得します。
```
C:\Users\masami> ibd2sdi --id=618 --skip-data "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
["ibd2sdi"
,
{
        "type": 1,
        "id": 618
}
]
```
* --type=#, -t #:指定されたオブジェクト型に一致するシリアライズディクショナリ情報 (SDI) を取得します。 
```
C:\Users\masami> ibd2sdi --type=2 --skip-data "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
["ibd2sdi"
,
{
        "type": 2,
        "id": 244
}
]

```
* --pretty, -p:
SDI データを JSON プリティ印刷形式で出力します。 デフォルトで有効。 無効にすると、SDI は人間が読めるようになりませんが、サイズは小さくなります。 --skip-pretty を使用して無効にします。
```
C:\Users\masami> ibd2sdi --skip-pretty "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
["ibd2sdi"
,
{
        "type": 1,
        "id": 618,
        "object":
                {"mysqld_version_id":80200,"dd_version":80200,"sdi_version":80019,"dd_object_type":"Table","dd_object":{"name":"imptest","mysql_version_id":80200,"created":20231124234359,"last_altered":20231124234359,"hidden":1,"options":"avg_row_length=0;encrypt_type=N;key_block_size=0;keys_disabled=0;pack_record=1;stats_auto_recalc=0;stats_sample_pages=0;","columns":
・・・
```