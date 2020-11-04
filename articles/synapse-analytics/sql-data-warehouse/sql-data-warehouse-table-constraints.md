---
title: Chiavi primarie, esterne e univoche
description: I vincoli di tabella supportano l'uso di un pool SQL dedicato in Azure sinapsi Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: dd1d92dd6be47b2bdf6b8ca2f9a99c62e35eb12a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313065"
---
# <a name="primary-key-foreign-key-and-unique-key-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Chiave primaria, chiave esterna e chiave univoca usando un pool SQL dedicato in Azure sinapsi Analytics

Informazioni sui vincoli di tabella nel pool SQL dedicato, inclusi chiave primaria, chiave esterna e chiave univoca.

## <a name="table-constraints"></a>Vincoli di tabella

Il pool SQL dedicato supporta i vincoli di tabella seguenti: 
- La chiave primaria è supportata solo se vengono usati entrambi non CLUSTER e non applicati.    
- Il vincolo UNIQUE è supportato solo se viene usato non applicato.

Per la sintassi, controllare [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) e [Create Table](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse). 

Il vincolo FOREIGN KEY non è supportato nel pool SQL dedicato.  


## <a name="remarks"></a>Osservazioni

La presenza di chiave primaria e/o chiave univoca consente al motore del pool SQL dedicato di generare un piano di esecuzione ottimale per una query.  Tutti i valori in una colonna chiave primaria o in una colonna vincolo univoca devono essere univoci.

Dopo aver creato una tabella con una chiave primaria o un vincolo UNIQUE nel pool SQL dedicato, gli utenti devono assicurarsi che tutti i valori di tali colonne siano univoci.  Una violazione di che può causare la restituzione di risultati non accurati nella query.  In questo esempio viene illustrato come una query può restituire risultati non accurati se la colonna PRIMARY KEY o unique constraint include valori duplicati.  

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

Creare una tabella del pool SQL dedicata con una chiave primaria: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```

Creare una tabella del pool SQL dedicata con un vincolo UNIQUE:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato le tabelle per il pool SQL dedicato, il passaggio successivo consiste nel caricare i dati nella tabella. Per un'esercitazione sul caricamento, vedere [caricamento di dati in un pool SQL dedicato](load-data-wideworldimportersdw.md).
