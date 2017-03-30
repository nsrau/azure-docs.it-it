---
title: Tipi di dati per le tabelle in SQL Data Warehouse | Microsoft Docs
description: Introduzione ai tipi di dati per le tabelle di Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7757de96601c426ff07e94cfa0c12d4dec8f06f5
ms.lasthandoff: 03/22/2017


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Tipi di dati per le tabelle in SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Panoramica][Overview]
> * [Tipi di dati][Data Types]
> * [Distribuzione][Distribute]
> * [Indice][Index]
> * [Partizione][Partition]
> * [Statistiche][Statistics]
> * [Temporanee][Temporary]
> 
> 

SQL Data Warehouse supporta i tipi di dati più diffusi.  Di seguito è riportato un elenco dei tipi di dati supportati da SQL Data Warehouse.  Per altre informazioni sul supporto dei tipi di dati, vedere [Creare una tabella][create table].

| **Tipi di dati supportati** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>Procedure consigliate per i tipi di dati
 Quando si definiscono i tipi delle colonne, per migliorare le prestazioni di query è consigliabile usare il tipo di dati più piccolo capace di supportare i dati. Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR. Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25). Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata. Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare [NVARCHAR][NVARCHAR].  Usare NVARCHAR(4000) o VARCHAR(8000), quando è possibile, anziché usare NVARCHAR(MAX) o VARCHAR(MAX).

## <a name="polybase-limitation"></a>Limitazione PolyBase
Se si usa Polybase per caricare le tabelle, assicurarsi che la lunghezza dei dati non superare 1 MB.  Anche se è possibile definire una riga con dati a lunghezza variabile che possono superare questa larghezza e caricare righe con BCP, non è possibile usare PolyBase per caricare i dati.  

## <a name="unsupported-data-types"></a>Tipi di dati non supportati
Se si esegue la migrazione del database da un'altra piattaforma SQL come il database SQL di Azure, durante la migrazione è possibile incontrare tipi di dati non supportati in SQL Data Warehouse.  Di seguito sono riportati i tipi di dati non supportati e alcune alternative che è possibile usare al loro posto.

| Tipo di dati | Soluzione alternativa |
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
| [tipi definiti dall'utente][user defined types] |Riconvertirli nei tipi nativi corrispondenti, se possibile. |
| valori predefiniti |I valori predefiniti supportano solo valori letterali e costanti.  Le espressioni o le funzioni non deterministiche, ad esempio `GETDATE()` o `CURRENT_TIMESTAMP`, non sono supportate. |

Il codice SQL riportato di seguito può essere eseguito nel database SQL corrente per identificare le colonne non supportate da Azure SQL Data Warehouse:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere gli articoli [Overview of tables in SQL Data Warehouse][Overview] (Panoramica sulle tabelle in SQL Data Warehouse), [Distribuzione di tabelle in SQL Data Warehouse][Distribute], [Indicizzazione di tabelle in SQL Data Warehouse][Index], [Partitioning tables in SQL Data Warehouse][Partition] (Partizionamento di tabelle in SQL Data Warehouse), [Managing statistics on tables in SQL Data Warehouse][Statistics] (Gestione delle statistiche nelle tabelle in SQL Data Warehouse) e [Temporary tables in SQL Data Warehouse][Temporary] (Tabelle temporanee in SQL Data Warehouse).  Per altre informazioni sulle procedure consigliate, vedere [Procedure consigliate per SQL Data Warehouse][SQL Data Warehouse Best Practices].

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

