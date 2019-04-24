---
title: Definizione dei tipi di dati - Azure SQL Data Warehouse | Microsoft Docs
description: Raccomandazioni per la definizione dei tipi di dati di tabella in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: aab51c3dc66a1486e8ad7ced55425a2b49c7dea1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60401766"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tipi di dati di tabella in Azure SQL Data Warehouse
Raccomandazioni per la definizione dei tipi di dati di tabella in Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Quali sono i tipi di dati?

SQL Data Warehouse supporta i tipi di dati più diffusi. Per un elenco dei tipi di dati supportati, vedere [tipi di dati](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) nell'istruzione CREATE TABLE. 

## <a name="minimize-row-length"></a>Ridurre al minimo la lunghezza di riga
Ridurre al minimo le dimensioni dei tipi di dati consente di ridurre la lunghezza di riga, con conseguenti prestazioni migliori per le query. Usare il tipo di dati più piccolo adatto ai dati. 

- Evitare di definire le colonne di tipo carattere con una lunghezza predefinita elevata. Ad esempio, se il valore più lungo è 25 caratteri, definire la colonna come VARCHAR(25). 
- Evitare di usare [NVARCHAR][NVARCHAR] quando serve solo VARCHAR.
- Quando possibile, usare NVARCHAR(4000) o VARCHAR(8000) invece di NVARCHAR(MAX) o VARCHAR(MAX).

Se si usano le tabelle esterne PolyBase per caricare le tabelle, la lunghezza definita per la riga della tabella non può essere maggiore di 1 MB. Quando una riga con dati di lunghezza variabile supera 1 MB, è possibile caricare la riga con BCP, ma non con PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificare i tipi di dati non supportati
Se si esegue la migrazione del database da un altro database SQL, durante la migrazione è possibile riscontrare tipi di dati non supportati in SQL Data Warehouse. Usare questa query per individuare i tipi di dati non supportati nello schema SQL esistente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Alternative per i tipi di dati non supportati

L'elenco seguente mostra i tipi di dati non supportati da SQL Data Warehouse e indica le alternative utilizzabili al posto dei tipi di dati non supportati.

| Tipo di dati non supportati | Soluzione alternativa |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Dividere la colonna in più colonne fortemente tipizzate. |
| [tabella](/sql/t-sql/data-types/table-transact-sql) |Convertire in tabelle temporanee. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Rielaborare il codice per usare [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) e la funzione [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql). Solo le costanti sono supportate come valori predefiniti, quindi non è possibile definire current_timestamp come vincolo predefinito. Se è necessario eseguire la migrazione di valori della versione di riga da una colonna di tipo timestamp, usare [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) o [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) per valori della versione di riga NOT NULL o NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [tipo definito dall'utente (UDT)](/sql/relational-databases/native-client/features/using-user-defined-types) |Riconvertire nel tipo di dati nativo, se possibile. |
| valori predefiniti | I valori predefiniti supportano solo valori letterali e costanti. |


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sullo sviluppo di tabelle, vedere [Cenni preliminari sulle tabelle](sql-data-warehouse-tables-overview.md).
