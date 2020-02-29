---
title: Chiavi primarie, esterne e univoche
description: Supporto di vincoli di tabella in analisi SQL in Azure sinapsi Analytics
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0379bed08c3ee6931e931a78a2d2c91664535250
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198134"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Chiave primaria, chiave esterna e chiave univoca in SQL Analytics

Informazioni sui vincoli di tabella in analisi SQL, inclusi chiave primaria, chiave esterna e chiave univoca.

## <a name="table-constraints"></a>Vincoli di tabella 
SQL Analytics supporta i vincoli di tabella seguenti: 
- La chiave primaria è supportata solo se vengono usati entrambi non CLUSTER e non applicati.    
- Il vincolo UNIQUE è supportato solo se viene usato non applicato.   

Il vincolo FOREIGN KEY non è supportato in analisi SQL.  

## <a name="remarks"></a>Osservazioni
Con chiave primaria e/o chiave univoca, il motore di analisi SQL consente di generare un piano di esecuzione ottimale per una query.  Tutti i valori in una colonna chiave primaria o in una colonna vincolo univoca devono essere univoci. 

Dopo aver creato una tabella con una chiave primaria o un vincolo UNIQUE in SQL Analytics, gli utenti devono assicurarsi che tutti i valori di tali colonne siano univoci.  Una violazione di che può causare la restituzione di risultati non accurati nella query.  In questo esempio viene illustrato come una query può restituire risultati non accurati se la colonna PRIMARY KEY o unique constraint include valori duplicati.  

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
Creare una tabella di analisi SQL con una chiave primaria: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Creare una tabella di analisi SQL con un vincolo UNIQUE:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato le tabelle per il database di analisi SQL, il passaggio successivo consiste nel caricare i dati nella tabella. Per un'esercitazione sul caricamento, vedere [caricamento di dati in database di analisi SQL](load-data-wideworldimportersdw.md).
