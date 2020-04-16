---
title: Definizione dei tipi di dati
description: Consigli per la definizione dei tipi di dati della tabella in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 312c58a7df09ebe3e0bdf0749f902199723a331b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429005"
---
# <a name="table-data-types-in-synapse-sql"></a>Tipi di dati di tabella in Synapse SQLTable data types in Synapse SQL

Consigli per la definizione dei tipi di dati della tabella in Synapse SQL. 

## <a name="what-are-the-data-types"></a>Quali sono i tipi di dati?

Synapse SQL supporta i tipi di dati più comunemente utilizzati. Per un elenco dei tipi di dati supportati, vedere [tipi di dati](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) nell'istruzione CREATE TABLE. 

## <a name="minimize-row-length"></a>Ridurre al minimo la lunghezza di riga

Ridurre al minimo le dimensioni dei tipi di dati consente di ridurre la lunghezza di riga, con conseguenti prestazioni migliori per le query. Usare il tipo di dati più piccolo adatto ai dati.

- Evitare di definire le colonne di tipo carattere con una lunghezza predefinita elevata. Ad esempio, se il valore più lungo è 25 caratteri, definire la colonna come VARCHAR(25).
- Evitare di usare [NVARCHAR][NVARCHAR] quando serve solo VARCHAR.
- Quando possibile, usare NVARCHAR(4000) o VARCHAR(8000) invece di NVARCHAR(MAX) o VARCHAR(MAX).

> [!NOTE]
> Se si utilizzano tabelle esterne PolyBase per caricare le tabelle del pool SQL, la lunghezza definita della riga della tabella non può superare 1 MB. Quando una riga con dati di lunghezza variabile supera 1 MB, è possibile caricare la riga con BCP, ma non con PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificare i tipi di dati non supportati

Se si esegue la migrazione del database da un altro database SQL, è possibile riscontrare tipi di dati non supportati in Synapse SQL. Usare questa query per individuare i tipi di dati non supportati nello schema SQL esistente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Alternative per i tipi di dati non supportati

Nell'elenco seguente vengono illustrati i tipi di dati non supportati da Synapse SQL e vengono fornite alternative che è possibile utilizzare al posto dei tipi di dati non supportati.

| Tipo di dati non supportati | Soluzione alternativa |
| --- | --- |
| [Geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Geografia](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Dividere la colonna in più colonne fortemente tipizzate. |
| [tabella](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Se si usa il pool SQL, è possibile eseguire la conversione in tabelle temporanee. Se si utilizza SQL (anteprima), è possibile archiviare i dati nell'archiviazione utilizzando [CETAS](../sql/develop-tables-cetas.md). |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Rielaborare il codice per usare [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e la funzione [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Solo le costanti sono supportate come valori predefiniti, quindi non è possibile definire current_timestamp come vincolo predefinito. Se è necessario eseguire la migrazione di valori della versione di riga da una colonna di tipo timestamp, usare [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) o [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) per valori della versione di riga NOT NULL o NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [tipo definito dall'utente (UDT)](/sql/relational-databases/native-client/features/using-user-defined-types) |Riconvertire nel tipo di dati nativo, se possibile. |
| valori predefiniti | I valori predefiniti supportano solo valori letterali e costanti. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di tabelle, vedere [Cenni preliminari sulle tabelle](develop-overview.md).
