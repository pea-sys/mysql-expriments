### 12.17.3 WKT 値からジオメトリ値を作成する関数

* ST_GeomCollFromText(wkt [, srid [, options]]), ST_GeometryCollectionFromText(wkt [, srid [, options]]), ST_GeomCollFromTxt(wkt [, srid [, options]])
```sql
mysql> SET @g = "MULTILINESTRING((10 10, 11 11), (9 9, 10 10))";
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_GeomCollFromText(@g));
+--------------------------------------------+
| ST_AsText(ST_GeomCollFromText(@g))         |
+--------------------------------------------+
| MULTILINESTRING((10 10,11 11),(9 9,10 10)) |
+--------------------------------------------+
1 row in set (0.00 sec)
```

### 12.17.6 ジオメトリ形式変換関数
* ST_AsBinary(g [, options]), ST_AsWKB(g [, options])
```sql
mysql> SET @g = ST_LineFromText('LINESTRING(0 5,5 10,10 15)', 4326);
Query OK, 0 rows affected (0.03 sec)

mysql> SELECT ST_AsText(ST_GeomFromWKB(ST_AsWKB(@g)));
+-----------------------------------------+
| ST_AsText(ST_GeomFromWKB(ST_AsWKB(@g))) |
+-----------------------------------------+
| LINESTRING(0 5,5 10,10 15)              |
+-----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_GeomFromWKB(ST_AsWKB(@g, 'axis-order=long-lat')));
+----------------------------------------------------------------+
| ST_AsText(ST_GeomFromWKB(ST_AsWKB(@g, 'axis-order=long-lat'))) |
+----------------------------------------------------------------+
| LINESTRING(5 0,10 5,15 10)                                     |
+----------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_GeomFromWKB(ST_AsWKB(@g, 'axis-order=lat-long')));
+----------------------------------------------------------------+
| ST_AsText(ST_GeomFromWKB(ST_AsWKB(@g, 'axis-order=lat-long'))) |
+----------------------------------------------------------------+
| LINESTRING(0 5,5 10,10 15)                                     |
+----------------------------------------------------------------+
1 row in set (0.00 sec)
```

* ST_AsText(g [, options]), ST_AsWKT(g [, options])
```sql
mysql> SET @g = 'LineString(1 1,2 2,3 3)';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_GeomFromText(@g));
+--------------------------------+
| ST_AsText(ST_GeomFromText(@g)) |
+--------------------------------+
| LINESTRING(1 1,2 2,3 3)        |
+--------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_GeomFromText(@mp));
+---------------------------------+
| ST_AsText(ST_GeomFromText(@mp)) |
+---------------------------------+
| NULL                            |
+---------------------------------+
1 row in set (0.00 sec)
```
* ST_SwapXY(g)
```sql
mysql> SET @g = ST_LineFromText('LINESTRING(0 5,5 10,10 15)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(@g);
+----------------------------+
| ST_AsText(@g)              |
+----------------------------+
| LINESTRING(0 5,5 10,10 15) |
+----------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_SwapXY(@g));
+----------------------------+
| ST_AsText(ST_SwapXY(@g))   |
+----------------------------+
| LINESTRING(5 0,10 5,15 10) |
+----------------------------+
1 row in set (0.00 sec)
```
### 12.17.7.1 一般的なジオメトリプロパティー関数
* ST_Dimension(g)
```sql
mysql> SELECT ST_Dimension(ST_GeomFromText('LineString(1 1,2 2)'));
+------------------------------------------------------+
| ST_Dimension(ST_GeomFromText('LineString(1 1,2 2)')) |
+------------------------------------------------------+
|                                                    1 |
+------------------------------------------------------+
1 row in set (0.00 sec)
```
* ST_Envelope(g)
```sql
mysql> SELECT ST_AsText(ST_Envelope(ST_GeomFromText('LineString(1 1,2 2)')));
+----------------------------------------------------------------+
| ST_AsText(ST_Envelope(ST_GeomFromText('LineString(1 1,2 2)'))) |
+----------------------------------------------------------------+
| POLYGON((1 1,2 1,2 2,1 2,1 1))                                 |
+----------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_Envelope(ST_GeomFromText('LineString(1 1,1 2)')));
+----------------------------------------------------------------+
| ST_AsText(ST_Envelope(ST_GeomFromText('LineString(1 1,1 2)'))) |
+----------------------------------------------------------------+
| LINESTRING(1 1,1 2)                                            |
+----------------------------------------------------------------+
1 row in set (0.00 sec)
```
* ST_GeometryType(g)
```sql
mysql> SELECT ST_GeometryType(ST_GeomFromText('POINT(1 1)'));
+------------------------------------------------+
| ST_GeometryType(ST_GeomFromText('POINT(1 1)')) |
+------------------------------------------------+
| POINT                                          |
+------------------------------------------------+
1 row in set (0.00 sec)
```
* ST_SRID(g [, srid])
```sql
mysql> SET @g = ST_GeomFromText('LineString(1 1,2 2)', 0);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_SRID(@g);
+-------------+
| ST_SRID(@g) |
+-------------+
|           0 |
+-------------+
1 row in set (0.00 sec)

mysql> SET @g = ST_SRID(@g, 4326);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_SRID(@g);
+-------------+
| ST_SRID(@g) |
+-------------+
|        4326 |
+-------------+
1 row in set (0.00 sec)
```
### 12.17.7.2 Pointプロパティー関数
* ST_Latitude(p [, new_latitude_val])
```sql
mysql> SET @pt = ST_GeomFromText('POINT(45 90)', 4326);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_Latitude(@pt);
+------------------+
| ST_Latitude(@pt) |
+------------------+
|               45 |
+------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_Latitude(@pt, 10));
+---------------------------------+
| ST_AsText(ST_Latitude(@pt, 10)) |
+---------------------------------+
| POINT(10 90)                    |
+---------------------------------+
1 row in set (0.00 sec)
```
* ST_Longitude(p [, new_longitude_val])
```sql
mysql> SET @pt = ST_GeomFromText('POINT(45 90)', 4326);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_Longitude(@pt);
+-------------------+
| ST_Longitude(@pt) |
+-------------------+
|                90 |
+-------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_Longitude(@pt, 10));
+----------------------------------+
| ST_AsText(ST_Longitude(@pt, 10)) |
+----------------------------------+
| POINT(45 10)                     |
+----------------------------------+
1 row in set (0.00 sec)
```
* ST_X(p [, new_x_val])
```sql
mysql> SELECT ST_X(Point(56.7, 53.34));
+--------------------------+
| ST_X(Point(56.7, 53.34)) |
+--------------------------+
|                     56.7 |
+--------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_X(Point(56.7, 53.34), 10.5));
+-------------------------------------------+
| ST_AsText(ST_X(Point(56.7, 53.34), 10.5)) |
+-------------------------------------------+
| POINT(10.5 53.34)                         |
+-------------------------------------------+
1 row in set (0.00 sec)
```
* ST_Y(p [, new_y_val])
```sql
mysql> SELECT ST_Y(Point(56.7, 53.34));
+--------------------------+
| ST_Y(Point(56.7, 53.34)) |
+--------------------------+
|                    53.34 |
+--------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_Y(Point(56.7, 53.34), 10.5));
+-------------------------------------------+
| ST_AsText(ST_Y(Point(56.7, 53.34), 10.5)) |
+-------------------------------------------+
| POINT(56.7 10.5)                          |
+-------------------------------------------+
1 row in set (0.00 sec)
```
### 12.17.7.3 LineString および MultiLineString のプロパティー関数
* ST_EndPoint(ls)
```sql
mysql> SET @ls = 'LineString(1 1,2 2,3 3)';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_EndPoint(ST_GeomFromText(@ls)));
+----------------------------------------------+
| ST_AsText(ST_EndPoint(ST_GeomFromText(@ls))) |
+----------------------------------------------+
| POINT(3 3)                                   |
+----------------------------------------------+
1 row in set (0.00 sec)
```
* ST_IsClosed(ls)
```sql
mysql> SET @ls1 = 'LineString(1 1,2 2,3 3,2 2)';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @ls2 = 'LineString(1 1,2 2,3 3,1 1)';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_IsClosed(ST_GeomFromText(@ls1));
+------------------------------------+
| ST_IsClosed(ST_GeomFromText(@ls1)) |
+------------------------------------+
|                                  0 |
+------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_IsClosed(ST_GeomFromText(@ls2));
+------------------------------------+
| ST_IsClosed(ST_GeomFromText(@ls2)) |
+------------------------------------+
|                                  1 |
+------------------------------------+
1 row in set (0.00 sec)
```
* ST_Length(ls [, unit])
```sql
mysql> SET @ls = ST_GeomFromText('LineString(1 1,2 2,3 3)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_Length(@ls);
+--------------------+
| ST_Length(@ls)     |
+--------------------+
| 2.8284271247461903 |
+--------------------+
1 row in set (0.00 sec)

mysql> SET @mls = ST_GeomFromText('MultiLineString((1 1,2 2,3 3),(4 4,5 5))');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_Length(@mls);
+-------------------+
| ST_Length(@mls)   |
+-------------------+
| 4.242640687119286 |
+-------------------+
1 row in set (0.00 sec)
```
* ST_NumPoints(ls)
```sql
mysql> SET @ls = 'LineString(1 1,2 2,3 3)';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_NumPoints(ST_GeomFromText(@ls));
+------------------------------------+
| ST_NumPoints(ST_GeomFromText(@ls)) |
+------------------------------------+
|                                  3 |
+------------------------------------+
1 row in set (0.00 sec)
```
* ST_PointN(ls, N)
```sql
mysql> SET @ls = 'LineString(1 1,2 2,3 3)';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_PointN(ST_GeomFromText(@ls),2));
+----------------------------------------------+
| ST_AsText(ST_PointN(ST_GeomFromText(@ls),2)) |
+----------------------------------------------+
| POINT(2 2)                                   |
+----------------------------------------------+
1 row in set (0.00 sec)
```
* ST_StartPoint(ls)
```sql
mysql> SET @ls = 'LineString(1 1,2 2,3 3)';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_StartPoint(ST_GeomFromText(@ls)));
+------------------------------------------------+
| ST_AsText(ST_StartPoint(ST_GeomFromText(@ls))) |
+------------------------------------------------+
| POINT(1 1)                                     |
+------------------------------------------------+
1 row in set (0.00 sec)
```
* ST_Centroid({poly|mpoly})
```sql
mysql> SET @poly =
    ->        ST_GeomFromText('POLYGON((0 0,10 0,10 10,0 10,0 0),(5 5,7 5,7 7,5 7,5 5))');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_GeometryType(@poly),ST_AsText(ST_Centroid(@poly));
+------------------------+--------------------------------------------+
| ST_GeometryType(@poly) | ST_AsText(ST_Centroid(@poly))              |
+------------------------+--------------------------------------------+
| POLYGON                | POINT(4.958333333333333 4.958333333333333) |
+------------------------+--------------------------------------------+
1 row in set (0.00 sec)
```
* ST_ExteriorRing(poly)
```sql
mysql> SET @poly =
    ->        'Polygon((0 0,0 3,3 3,3 0,0 0),(1 1,1 2,2 2,2 1,1 1))';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_ExteriorRing(ST_GeomFromText(@poly)));
+----------------------------------------------------+
| ST_AsText(ST_ExteriorRing(ST_GeomFromText(@poly))) |
+----------------------------------------------------+
| LINESTRING(0 0,0 3,3 3,3 0,0 0)                    |
+----------------------------------------------------+
1 row in set (0.00 sec)
```
* ST_InteriorRingN(poly, N)
```sql
mysql> SET @poly =
    ->        'Polygon((0 0,0 3,3 3,3 0,0 0),(1 1,1 2,2 2,2 1,1 1))';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_InteriorRingN(ST_GeomFromText(@poly),1));
+-------------------------------------------------------+
| ST_AsText(ST_InteriorRingN(ST_GeomFromText(@poly),1)) |
+-------------------------------------------------------+
| LINESTRING(1 1,1 2,2 2,2 1,1 1)                       |
+-------------------------------------------------------+
1 row in set (0.00 sec)
```
* ST_NumInteriorRing(poly), ST_NumInteriorRings(poly)
```sql
mysql> SET @poly =
    ->        'Polygon((0 0,0 3,3 3,3 0,0 0),(1 1,1 2,2 2,2 1,1 1))';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_NumInteriorRings(ST_GeomFromText(@poly));
+---------------------------------------------+
| ST_NumInteriorRings(ST_GeomFromText(@poly)) |
+---------------------------------------------+
|                                           1 |
+---------------------------------------------+
1 row in set (0.00 sec)
```
### 12.17.7.5 GeometryCollection プロパティー関数
* ST_GeometryN(gc, N)
```sql
mysql> SET @gc = 'GeometryCollection(Point(1 1),LineString(2 2, 3 3))';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_GeometryN(ST_GeomFromText(@gc),1));
+-------------------------------------------------+
| ST_AsText(ST_GeometryN(ST_GeomFromText(@gc),1)) |
+-------------------------------------------------+
| POINT(1 1)                                      |
+-------------------------------------------------+
1 row in set (0.00 sec)
```
* ST_NumGeometries(gc)
```sql
mysql> SET @gc = 'GeometryCollection(Point(1 1),LineString(2 2, 3 3))';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_NumGeometries(ST_GeomFromText(@gc));
+----------------------------------------+
| ST_NumGeometries(ST_GeomFromText(@gc)) |
+----------------------------------------+
|                                      2 |
+----------------------------------------+
1 row in set (0.00 sec)
```
### 12.17.8 空間演算子関数
* ST_Buffer(g, d [, strategy1 [, strategy2 [, strategy3]]])
```sql
mysql> SET @pt = ST_GeomFromText('POINT(0 0)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_Buffer(@pt, 0));
+------------------------------+
| ST_AsText(ST_Buffer(@pt, 0)) |
+------------------------------+
| POINT(0 0)                   |
+------------------------------+
1 row in set (0.00 sec)
```
* ST_ConvexHull(g)
```sql
mysql> SET @g = 'MULTIPOINT(5 0,25 0,15 10,15 25)';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_ConvexHull(ST_GeomFromText(@g)));
+-----------------------------------------------+
| ST_AsText(ST_ConvexHull(ST_GeomFromText(@g))) |
+-----------------------------------------------+
| POLYGON((5 0,25 0,15 25,5 0))                 |
+-----------------------------------------------+
1 row in set (0.00 sec)
```
* ST_Difference(g1, g2)
```sql
mysql> SET @g1 = Point(1,1), @g2 = Point(2,2);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_Difference(@g1, @g2));
+------------------------------------+
| ST_AsText(ST_Difference(@g1, @g2)) |
+------------------------------------+
| POINT(1 1)                         |
+------------------------------------+
1 row in set (0.01 sec)
```
* ST_Intersection(g1, g2)
```sql
mysql> SET @g1 = ST_GeomFromText('LineString(1 1, 3 3)');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @g2 = ST_GeomFromText('LineString(1 3, 3 1)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_Intersection(@g1, @g2));
+--------------------------------------+
| ST_AsText(ST_Intersection(@g1, @g2)) |
+--------------------------------------+
| POINT(2 2)                           |
+--------------------------------------+
1 row in set (0.01 sec)

```
* ST_LineInterpolatePoints(ls, fractional_distance)
```sql
mysql> SET @ls1 = ST_GeomFromText('LINESTRING(0 0,0 5,5 5)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_LineInterpolatePoint(@ls1, .5));
+----------------------------------------------+
| ST_AsText(ST_LineInterpolatePoint(@ls1, .5)) |
+----------------------------------------------+
| POINT(0 5)                                   |
+----------------------------------------------+
1 row in set (0.00 sec)
mysql> SELECT ST_AsText(ST_LineInterpolatePoint(@ls1, .75));
+-----------------------------------------------+
| ST_AsText(ST_LineInterpolatePoint(@ls1, .75)) |
+-----------------------------------------------+
| POINT(2.5 5)                                  |
+-----------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_LineInterpolatePoint(@ls1, 1));
+---------------------------------------------+
| ST_AsText(ST_LineInterpolatePoint(@ls1, 1)) |
+---------------------------------------------+
| POINT(5 5)                                  |
+---------------------------------------------+
1 row in set (0.00 sec)
```
* ST_SymDifference(g1, g2)
```sql
mysql> SET @g1 = Point(1,1), @g2 = Point(2,2);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_SymDifference(@g1, @g2));
+---------------------------------------+
| ST_AsText(ST_SymDifference(@g1, @g2)) |
+---------------------------------------+
| MULTIPOINT((1 1),(2 2))               |
+---------------------------------------+
1 row in set (0.00 sec)
```
* ST_Union(g1, g2)
```sql
mysql> SET @g1 = ST_GeomFromText('LineString(1 1, 3 3)');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @g2 = ST_GeomFromText('LineString(1 3, 3 1)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_Union(@g1, @g2));
+--------------------------------------+
| ST_AsText(ST_Union(@g1, @g2))        |
+--------------------------------------+
| MULTILINESTRING((1 1,3 3),(1 3,3 1)) |
+--------------------------------------+
1 row in set (0.01 sec)
```
### 12.17.9.1 オブジェクト形状を使用する空間関係関数
* ST_Distance(g1, g2 [, unit])
```sql
mysql> SET @g1 = ST_GeomFromText('POINT(1 1)');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @g2 = ST_GeomFromText('POINT(2 2)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_Distance(@g1, @g2);
+-----------------------+
| ST_Distance(@g1, @g2) |
+-----------------------+
|    1.4142135623730951 |
+-----------------------+
1 row in set (0.00 sec)

mysql> SET @g1 = ST_GeomFromText('POINT(1 1)', 4326);
Query OK, 0 rows affected (0.00 sec)

mysql> SET @g2 = ST_GeomFromText('POINT(2 2)', 4326);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_Distance(@g1, @g2);
+-----------------------+
| ST_Distance(@g1, @g2) |
+-----------------------+
|     156874.3859490455 |
+-----------------------+
1 row in set (0.00 sec)
```
* ST_Equals(g1, g2)
```sql
mysql> SET @g1 = Point(1,1), @g2 = Point(2,2);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_Equals(@g1, @g1), ST_Equals(@g1, @g2);
+---------------------+---------------------+
| ST_Equals(@g1, @g1) | ST_Equals(@g1, @g2) |
+---------------------+---------------------+
|                   1 |                   0 |
+---------------------+---------------------+
1 row in set (0.00 sec)
```
* ST_FrechetDistance(g1, g2 [, unit])
```sql
mysql> SET @ls1 = ST_GeomFromText('LINESTRING(0 0,0 5,5 5)');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @ls2 = ST_GeomFromText('LINESTRING(0 1,0 6,3 3,5 6)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_FrechetDistance(@ls1, @ls2);
+--------------------------------+
| ST_FrechetDistance(@ls1, @ls2) |
+--------------------------------+
|             2.8284271247461903 |
+--------------------------------+
1 row in set (0.00 sec)

mysql> SET @ls1 = ST_GeomFromText('LINESTRING(0 0,0 5,5 5)', 4326);
Query OK, 0 rows affected (0.00 sec)

mysql> SET @ls2 = ST_GeomFromText('LINESTRING(0 1,0 6,3 3,5 6)', 4326);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_FrechetDistance(@ls1, @ls2);
+--------------------------------+
| ST_FrechetDistance(@ls1, @ls2) |
+--------------------------------+
|              313421.1999416798 |
+--------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_FrechetDistance(@ls1, @ls2, 'foot');
+----------------------------------------+
| ST_FrechetDistance(@ls1, @ls2, 'foot') |
+----------------------------------------+
|                     1028284.7767115477 |
+----------------------------------------+
1 row in set (0.00 sec)
```
* ST_HausdorffDistance(g1, g2 [, unit])
```sql
mysql> SET @ls1 = ST_GeomFromText('LINESTRING(0 0,0 5,5 5)');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @ls2 = ST_GeomFromText('LINESTRING(0 1,0 6,3 3,5 6)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_HausdorffDistance(@ls1, @ls2);
+----------------------------------+
| ST_HausdorffDistance(@ls1, @ls2) |
+----------------------------------+
|                                1 |
+----------------------------------+
1 row in set (0.00 sec)
```
### 12.17.9.2 最小境界矩形を使用する空間リレーション関数
* MBRContains(g1, g2)
```sql
mysql> SET @g1 = ST_GeomFromText('Polygon((0 0,0 3,3 3,3 0,0 0))');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @g2 = ST_GeomFromText('Point(1 1)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT MBRContains(@g1,@g2), MBRWithin(@g2,@g1);
+----------------------+--------------------+
| MBRContains(@g1,@g2) | MBRWithin(@g2,@g1) |
+----------------------+--------------------+
|                    1 |                  1 |
+----------------------+--------------------+
1 row in set (0.01 sec)
```
* MBRCoveredBy(g1, g2)
```Sql
mysql> SET @g1 = ST_GeomFromText('Polygon((0 0,0 3,3 3,3 0,0 0))');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @g2 = ST_GeomFromText('Point(1 1)');
Query OK, 0 rows affected (0.00 sec)

mysql>  SELECT MBRCovers(@g1,@g2), MBRCoveredby(@g1,@g2);
+--------------------+-----------------------+
| MBRCovers(@g1,@g2) | MBRCoveredby(@g1,@g2) |
+--------------------+-----------------------+
|                  1 |                     0 |
+--------------------+-----------------------+
1 row in set (0.00 sec)

mysql> SELECT MBRCovers(@g2,@g1), MBRCoveredby(@g2,@g1);
+--------------------+-----------------------+
| MBRCovers(@g2,@g1) | MBRCoveredby(@g2,@g1) |
+--------------------+-----------------------+
|                  0 |                     1 |
+--------------------+-----------------------+
1 row in set (0.00 sec)
```
* MBRWithin(g1, g2)
```Sql
mysql> SET @g1 = ST_GeomFromText('Polygon((0 0,0 3,3 3,3 0,0 0))');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @g2 = ST_GeomFromText('Polygon((0 0,0 5,5 5,5 0,0 0))');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT MBRWithin(@g1,@g2), MBRWithin(@g2,@g1);
+--------------------+--------------------+
| MBRWithin(@g1,@g2) | MBRWithin(@g2,@g1) |
+--------------------+--------------------+
|                  1 |                  0 |
+--------------------+--------------------+
1 row in set (0.00 sec)
```
### 12.17.10 空間 Geohash 関数
* ST_GeoHash(longitude, latitude, max_length), ST_GeoHash(point, max_length)
```sql
mysql> SELECT ST_GeoHash(180,0,10), ST_GeoHash(-180,-90,15);
+----------------------+-------------------------+
| ST_GeoHash(180,0,10) | ST_GeoHash(-180,-90,15) |
+----------------------+-------------------------+
| xbpbpbpbpb           | 000000000000000         |
+----------------------+-------------------------+
1 row in set (0.01 sec)
```
* ST_LatFromGeoHash(geohash_str)
```sql
mysql> SELECT ST_LatFromGeoHash(ST_GeoHash(45,-20,10));
+------------------------------------------+
| ST_LatFromGeoHash(ST_GeoHash(45,-20,10)) |
+------------------------------------------+
|                                      -20 |
+------------------------------------------+
1 row in set (0.00 sec)
```
* ST_LongFromGeoHash(geohash_str)
```sql
mysql> SELECT ST_LongFromGeoHash(ST_GeoHash(45,-20,10));
+-------------------------------------------+
| ST_LongFromGeoHash(ST_GeoHash(45,-20,10)) |
+-------------------------------------------+
|                                        45 |
+-------------------------------------------+
1 row in set (0.00 sec)
```
* ST_PointFromGeoHash(geohash_str, srid)
```sql
mysql> SET @gh = ST_GeoHash(45,-20,10);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_PointFromGeoHash(@gh,0));
+---------------------------------------+
| ST_AsText(ST_PointFromGeoHash(@gh,0)) |
+---------------------------------------+
| POINT(45 -20)                         |
+---------------------------------------+
1 row in set (0.01 sec)
```
### 12.17.11 空間 GeoJSON 関数
* ST_AsGeoJSON(g [, max_dec_digits [, options]])
```sql
mysql> SELECT ST_AsGeoJSON(ST_GeomFromText('POINT(11.11111 12.22222)'),2);
+-------------------------------------------------------------+
| ST_AsGeoJSON(ST_GeomFromText('POINT(11.11111 12.22222)'),2) |
+-------------------------------------------------------------+
| {"type": "Point", "coordinates": [11.11, 12.22]}            |
+-------------------------------------------------------------+
1 row in set (0.00 sec)
```
* ST_GeomFromGeoJSON(str [, options [, srid]])
```Sql
mysql> SET @json = '{ "type": "Point", "coordinates": [102.0, 0.0]}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_GeomFromGeoJSON(@json));
+--------------------------------------+
| ST_AsText(ST_GeomFromGeoJSON(@json)) |
+--------------------------------------+
| POINT(0 102)                         |
+--------------------------------------+
1 row in set (0.03 sec)

mysql> SELECT ST_SRID(ST_GeomFromGeoJSON(@json));
+------------------------------------+
| ST_SRID(ST_GeomFromGeoJSON(@json)) |
+------------------------------------+
|                               4326 |
+------------------------------------+
1 row in set (0.00 sec)
```
### 12.17.12 空間集計関数
* ST_Collect([DISTINCT] g) [over_clause]
```Sql
mysql> CREATE TABLE product (
    ->   year INTEGER,
    ->   product VARCHAR(256),
    ->   location Geometry
    -> );
Query OK, 0 rows affected (0.06 sec)

mysql>
mysql> INSERT INTO product
    -> (year,  product,     location) VALUES
    -> (2000, "Calculator", ST_GeomFromText('point(60 -24)',4326)),
    -> (2000, "Computer"  , ST_GeomFromText('point(28 -77)',4326)),
    -> (2000, "Abacus"    , ST_GeomFromText('point(28 -77)',4326)),
    -> (2000, "TV"        , ST_GeomFromText('point(38  60)',4326)),
    -> (2001, "Calculator", ST_GeomFromText('point(60 -24)',4326)),
    -> (2001, "Computer"  , ST_GeomFromText('point(28 -77)',4326));
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> SELECT ST_AsText(ST_Collect(location)) AS result
    ->        FROM product;
+------------------------------------------------------------------+
| result                                                           |
+------------------------------------------------------------------+
| MULTIPOINT((60 -24),(28 -77),(28 -77),(38 60),(60 -24),(28 -77)) |
+------------------------------------------------------------------+
1 row in set (0.01 sec)

mysql> SELECT ST_AsText(ST_Collect(DISTINCT location)) AS result
    ->        FROM product;
+---------------------------------------+
| result                                |
+---------------------------------------+
| MULTIPOINT((60 -24),(28 -77),(38 60)) |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_Collect(location)
    ->          OVER (ORDER BY year, product ROWS BETWEEN 1 PRECEDING AND CURRENT ROW))
    ->          AS result
    ->        FROM product;
+-------------------------------+
| result                        |
+-------------------------------+
| MULTIPOINT((28 -77))          |
| MULTIPOINT((28 -77),(60 -24)) |
| MULTIPOINT((60 -24),(28 -77)) |
| MULTIPOINT((28 -77),(38 60))  |
| MULTIPOINT((38 60),(60 -24))  |
| MULTIPOINT((60 -24),(28 -77)) |
+-------------------------------+
6 rows in set (0.01 sec)
```
### 12.17.13 空間の便利な関数
* ST_Distance_Sphere(g1, g2 [, radius])
```sql
mysql> SET @pt1 = ST_GeomFromText('POINT(0 0)');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @pt2 = ST_GeomFromText('POINT(180 0)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_Distance_Sphere(@pt1, @pt2);
+--------------------------------+
| ST_Distance_Sphere(@pt1, @pt2) |
+--------------------------------+
|             20015042.813723423 |
+--------------------------------+
1 row in set (0.00 sec)
```
* ST_IsValid(g)
```sql
mysql> SET @ls1 = ST_GeomFromText('LINESTRING(0 0,-0.00 0,0.0 0)');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @ls2 = ST_GeomFromText('LINESTRING(0 0, 1 1)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_IsValid(@ls1);
+------------------+
| ST_IsValid(@ls1) |
+------------------+
|                0 |
+------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_IsValid(@ls2);
+------------------+
| ST_IsValid(@ls2) |
+------------------+
|                1 |
+------------------+
1 row in set (0.00 sec)
```
* ST_MakeEnvelope(pt1, pt2)
```Sql
mysql> SET @pt1 = ST_GeomFromText('POINT(0 0)');
Query OK, 0 rows affected (0.00 sec)

mysql> SET @pt2 = ST_GeomFromText('POINT(1 1)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_MakeEnvelope(@pt1, @pt2));
+----------------------------------------+
| ST_AsText(ST_MakeEnvelope(@pt1, @pt2)) |
+----------------------------------------+
| POLYGON((0 0,1 0,1 1,0 1,0 0))         |
+----------------------------------------+
1 row in set (0.00 sec)
```
* ST_Simplify(g, max_distance)
```sql
mysql> SET @g = ST_GeomFromText('LINESTRING(0 0,0 1,1 1,1 2,2 2,2 3,3 3)');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ST_AsText(ST_Simplify(@g, 0.5));
+---------------------------------+
| ST_AsText(ST_Simplify(@g, 0.5)) |
+---------------------------------+
| LINESTRING(0 0,0 1,1 1,2 3,3 3) |
+---------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ST_AsText(ST_Simplify(@g, 1.0));
+---------------------------------+
| ST_AsText(ST_Simplify(@g, 1.0)) |
+---------------------------------+
| LINESTRING(0 0,3 3)             |
+---------------------------------+
1 row in set (0.00 sec)
```
