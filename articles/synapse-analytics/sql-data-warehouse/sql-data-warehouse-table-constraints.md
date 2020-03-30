---
title: Chiavi primarie, esterne e univoche
description: Supporto dei vincoli di tabella in SQL Analytics in Azure Synapse AnalyticsTable constraints support in SQL Analytics in Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b9336a5e230e90e1abd7f2d40d431b988385c009
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350025"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Chiave primaria, chiave esterna e chiave univoca in SQL AnalyticsPrimary key, foreign key, and unique key in SQL Analytics

Informazioni sui vincoli di tabella in SQL Analytics, tra cui chiave primaria, chiave esterna e chiave univoca.

## <a name="table-constraints"></a>Vincoli di tabella 
SQL Analytics supporta questi vincoli di tabella:SQL Analytics supports these table constraints: 
- PRIMARY KEY è supportato solo quando vengono utilizzati non CLUSTERED e NOT ENFORCED.    
- Il vincolo UNIQUE è supportato solo con NON ENFORCED.   

Il vincolo FOREIGN KEY non è supportato in SQL Analytics.  

## <a name="remarks"></a>Osservazioni
La presenza di chiave primaria e/o chiave univoca consente al motore di SQL Analytics di generare un piano di esecuzione ottimale per una query.  Tutti i valori in una colonna chiave primaria o in una colonna di vincolo univoco devono essere univoci. 

Dopo aver creato una tabella con chiave primaria o vincolo univoco in SQL Analytics, gli utenti devono assicurarsi che tutti i valori in tali colonne siano univoci.  Una violazione di ciò può causare la query per restituire un risultato impreciso.  In questo esempio viene illustrato come una query può restituire un risultato impreciso se la chiave primaria o la colonna del vincolo univoco include valori duplicati.  

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
Creare una tabella di Analisi SQL con una chiave primaria:Create a SQL Analytics table with a primary key: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Creare una tabella di Analisi SQL con un vincolo univoco:Create a SQL Analytics table with a unique constraint:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato le tabelle per il database di SQL Analytics, il passaggio successivo consiste nel caricare i dati nella tabella. Per un'esercitazione sul caricamento, vedere [Caricamento di dati in database SQL Analytics](load-data-wideworldimportersdw.md).
