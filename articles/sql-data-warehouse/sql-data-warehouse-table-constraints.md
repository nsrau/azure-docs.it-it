---
title: Chiave primaria, chiave esterna e chiave univoca in Azure SQL Data Warehouse | Microsoft Docs
description: Supporto di vincoli di tabella in Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: b02f219e549f2206f71c08c9d465b2bc05a6d526
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310295"
---
# <a name="primary-key-foreign-key-and-unique-key-in-azure-sql-data-warehouse"></a>Chiave primaria, chiave esterna e chiave univoca in Azure SQL Data Warehouse

Informazioni sui vincoli di tabella in Azure SQL Data Warehouse, inclusi chiave primaria, chiave esterna e chiave univoca.

## <a name="table-constraints"></a>Vincoli di tabella 
Azure SQL Data Warehouse supporta i vincoli di tabella seguenti: 
- La chiave primaria è supportata solo se vengono usati entrambi non CLUSTER e non applicati.    
- Il vincolo UNIQUE è supportato solo se viene usato non applicato.   

Il vincolo FOREIGN KEY non è supportato in Azure SQL Data Warehouse.  

## <a name="remarks"></a>Note
La presenza di chiavi primarie e/o chiavi univoche consente data warehouse motore di generare un piano di esecuzione ottimale per una query.  Tutti i valori in una colonna chiave primaria o in una colonna vincolo univoca devono essere univoci. 

Dopo aver creato una tabella con una chiave primaria o un vincolo UNIQUE in Azure data warehouse, gli utenti devono assicurarsi che tutti i valori di tali colonne siano univoci.  Una violazione di che può causare la restituzione di risultati non accurati nella query.  In questo esempio viene illustrato come una query può restituire risultati non accurati se la colonna PRIMARY KEY o unique constraint include valori duplicati.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Esempi
Creare una tabella data warehouse con una chiave primaria: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Creare una tabella di data warehouse con un vincolo UNIQUE:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Passaggi successivi

Dopo avere creato le tabelle per il data warehouse, il passaggio successivo consiste nel caricare i dati nella tabella. Per un'esercitazione sul caricamento, vedere [Caricare dati in SQL Data Warehouse](load-data-wideworldimportersdw.md).
