## 4.5.4 mysqldump — データベースバックアッププログラム
mysqldump クライアントユーティリティは logical backups を実行し、元のデータベースオブジェクト定義およびテーブルデータを再現するために実行できる一連の SQL ステートメントを生成します。

* 起動構文
```
C:\Users\masami>mysqldump sakila actor -u root -p
Enter password: **********
-- MySQL dump 10.13  Distrib 8.2.0, for Win64 (x86_64)
--
-- Host: localhost    Database: sakila
-- ------------------------------------------------------
-- Server version       8.2.0

・・・

--
-- Table structure for table `actor`
--

DROP TABLE IF EXISTS `actor`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `actor` (
  `actor_id` smallint unsigned NOT NULL AUTO_INCREMENT,
  `first_name` varchar(45) NOT NULL,
  `last_name` varchar(45) NOT NULL,
  `last_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`actor_id`),
  KEY `idx_actor_last_name` (`last_name`)
) ENGINE=InnoDB AUTO_INCREMENT=201 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `actor`
--

・・・


-- Dump completed on 2023-11-24 21:19:48
```
* --add-drop-database:DROP DATABASE ステートメントを各 CREATE DATABASE ステートメントの前に追加
```
C:\Users\masami>mysqldump --add-drop-database sakila actor -u root -p
Enter password: **********
-- MySQL dump 10.13  Distrib 8.2.0, for Win64 (x86_64)
--
-- Host: localhost    Database: sakila
-- ------------------------------------------------------
-- Server version       8.2.0

・・・

--
-- Table structure for table `actor`
--

・・・

--
-- Dumping data for table `actor`
--

・・・

-- Dump completed on 2023-11-24 21:22:30
```
* --compact：よりコンパクトな出力を生成
```
C:\Users\masami>mysqldump --compact sakila actor -u root -p
Enter password: **********
・・・
```

* --compatible:古い MySQL サーバーやほかのデータベースシステムとの互換性がより高い出力を生成
```
C:\Users\masami>mysqldump --compatible="ansi" sakila actor -u root -p
Enter password: **********


-- Dump completed on 2023-11-24 21:42:16
```

* --ignore-table:指定されたテーブルをダンプしない
```
C:\Users\masami>mysqldump --ignore-table=world.city --ignore-table=world.country world -u root -p
Enter password: **********
・・・
--
-- Dumping data for table `countrylanguage`
--
・・・
-- Dump completed on 2023-11-24 21:48:47
```

* --where:指定された WHERE 条件で選択された行のみダンプ
```
C:\Users\masami>mysqldump --where="first_name like '%A'" sakila actor -u root -p
Enter password: **********
-- MySQL dump 10.13  Distrib 8.2.0, for Win64 (x86_64)
--
-- Host: localhost    Database: sakila
-- ------------------------------------------------------
-- Server version       8.2.0

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!50503 SET NAMES utf8mb4 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `actor`
--

DROP TABLE IF EXISTS `actor`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `actor` (
  `actor_id` smallint unsigned NOT NULL AUTO_INCREMENT,
  `first_name` varchar(45) NOT NULL,
  `last_name` varchar(45) NOT NULL,
  `last_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`actor_id`),
  KEY `idx_actor_last_name` (`last_name`)
) ENGINE=InnoDB AUTO_INCREMENT=201 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `actor`
--
-- WHERE:  first_name like '%A'

LOCK TABLES `actor` WRITE;
/*!40000 ALTER TABLE `actor` DISABLE KEYS */;
INSERT INTO `actor` VALUES (13,'UMA','test','2023-11-22 23:48:11'),(15,'CUBA','test','2023-11-22 23:48:11'),(23,'SANDRA','test','2023-11-22 23:48:11'),(27,'JULIA','test','2023-11-22 23:48:11'),(30,'SANDRA','test','2023-11-22 23:48:11'),(33,'MILLA','test','2023-11-22 23:48:11'),(47,'JULIA','test','2023-11-22 23:48:11'),(53,'MENA','test','2023-11-22 23:48:11'),(65,'ANGELA','test','2023-11-22 23:48:11'),(67,'JESSICA','test','2023-11-22 23:48:11'),(74,'MILLA','test','2023-11-22 23:48:11'),(76,'ANGELINA','test','2023-11-22 23:48:11'),(107,'GINA','test','2023-11-22 23:48:11'),(118,'CUBA','test','2023-11-22 23:48:11'),(121,'LIZA','test','2023-11-22 23:48:11'),(122,'SALMA','test','2023-11-22 23:48:11'),(130,'GRETA','test','2023-11-22 23:48:11'),(135,'RITA','test','2023-11-22 23:48:11'),(142,'JADA','test','2023-11-22 23:48:11'),(144,'ANGELA','test','2023-11-22 23:48:11'),(157,'GRETA','test','2023-11-22 23:48:11'),(159,'LAURA','test','2023-11-22 23:48:11'),(170,'MENA','test','2023-11-22 23:48:11'),(171,'OLYMPIA','test','2023-11-22 23:48:11'),(178,'LISA','test','2023-11-22 23:48:11'),(186,'JULIA','test','2023-11-22 23:48:11'),(189,'CUBA','test','2023-11-22 23:48:11'),(196,'BELA','test','2023-11-22 23:48:11'),(199,'JULIA','test','2023-11-22 23:48:11'),(200,'THORA','test','2023-11-22 23:48:11');
/*!40000 ALTER TABLE `actor` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2023-11-24 22:32:45
```