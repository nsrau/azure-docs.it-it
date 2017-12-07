---
title: Linee guida per i tipi di dati - Azure SQL Data Warehouse | Microsoft Docs
description: Raccomandazioni per definire tipi di dati che siano compatibili con SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 2bde5da8593c559e2afb33c9c4842695dc273ac3
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="guidance-for-defining-data-types-for-tables-in-sql-data-warehouse"></a>Linee guida per la definizione dei tipi di dati per le tabelle in SQL Data Warehouse
Usare queste raccomandazioni per definire tipi di dati per le tabelle che siano compatibili con SQL Data Warehouse. Oltre a garantire la compatibilità, ridurre al minimo le dimensioni dei tipi di dati consente di migliorare le prestazioni delle query.

SQL Data Warehouse supporta i tipi di dati più diffusi. Per un elenco dei tipi di dati supportati, vedere [tipi di dati](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) nell'istruzione CREATE TABLE. 


## <a name="minimize-row-length"></a>Ridurre al minimo la lunghezza di riga
Ridurre al minimo le dimensioni dei tipi di dati consente di ridurre la lunghezza di riga, con conseguenti prestazioni migliori per le query. Usare il tipo di dati più piccolo adatto ai dati. 

- Evitare di definire le colonne di tipo carattere con una lunghezza predefinita elevata. Ad esempio, se il valore più lungo è 25 caratteri, definire la colonna come VARCHAR(25). 
- Evitare di usare [NVARCHAR][NVARCHAR] quando serve solo VARCHAR.
- Quando possibile, usare NVARCHAR(4000) o VARCHAR(8000) invece di NVARCHAR(MAX) o VARCHAR(MAX).

Se si usa Polybase per caricare le tabelle, la lunghezza definita per la riga della tabella non può essere maggiore di 1 MB. Quando una riga con dati di lunghezza variabile supera 1 MB, è possibile caricare la riga con BCP, ma non con PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificare i tipi di dati non supportati
Se si esegue la migrazione del database da un altro database SQL, durante la migrazione è possibile riscontrare tipi di dati non supportati in SQL Data Warehouse. Usare questa query per individuare i tipi di dati non supportati nello schema SQL esistente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Usare alternative per i tipi di dati non supportati

L'elenco seguente mostra i tipi di dati non supportati da SQL Data Warehouse e indica le alternative utilizzabili al posto dei tipi di dati non supportati.

| Tipo di dati non supportati | Soluzione alternativa |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Dividere la colonna in più colonne fortemente tipizzate. |
| [table][table] |Convertire in tabelle temporanee. |
| [timestamp][timestamp] |Rielaborare il codice per l'uso di [datetime2][datetime2] e della funzione `CURRENT_TIMESTAMP`.  Solo le costanti sono supportate come valori predefiniti, quindi non è possibile definire current_timestamp come vincolo predefinito. Se è necessario eseguire la migrazione di valori della versione di riga da una colonna di tipo timestamp, usare [BINARY][BINARY](8) o [VARBINARY][BINARY](8) per valori della versione di riga NOT NULL o NULL. |
| [xml][xml] |[varchar][varchar] |
| [tipo definito dall'utente (UDT)][user defined types] |Riconvertire nel tipo di dati nativo, se possibile. |
| valori predefiniti | I valori predefiniti supportano solo valori letterali e costanti.  Le espressioni o le funzioni non deterministiche, ad esempio `GETDATE()` o `CURRENT_TIMESTAMP`, non sono supportate. |


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere:

- [Procedure consigliate per SQL Data Warehouse][SQL Data Warehouse Best Practices]
- [Panoramica delle tabelle][Overview]
- [Distribuzione di una tabella][Distribute]
- [Indicizzazione di una tabella][Index]
- [Partizionamento di una tabella][Partition]
- [Gestire le statistiche delle tabelle][Statistics]
- [Tabelle temporanee][Temporary]

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx
