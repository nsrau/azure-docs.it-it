<properties
   pageTitle="Tipi di dati per le tabelle in SQL Data Warehouse | Microsoft Azure"
   description="Introduzione ai tipi di dati per le tabelle di Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Tipi di dati per le tabelle in SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuzione][]
- [Index][]
- [Partition][]
- [Statistiche][]
- [Temporanee][]

SQL Data Warehouse supporta i tipi di dati più diffusi. Di seguito è riportato un elenco dei tipi di dati supportati da SQL Data Warehouse. Per altre informazioni sul supporto dei tipi di dati, vedere l'argomento relativo a [CREATE TABLE][].

|**Tipi di dati supportati**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binary][]|[float][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[char][]|[money][]|[time][]|
[date][]|[nchar][]|[tinyint][]|
[datetime][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[real][]|[varbinary][]|
[datetimeoffset][]|[smalldatetime][]|[varchar][]|


## Procedure consigliate per i tipi di dati

 Quando si definiscono i tipi delle colonne, per migliorare le prestazioni di query è consigliabile usare il tipo di dati più piccolo capace di supportare i dati. Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR. Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25). Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata. Definire le colonne come VARCHAR quando è sufficiente, anziché usare [NVARCHAR][]. Usare NVARCHAR(4000) o VARCHAR(8000), quando è possibile, anziché usare NVARCHAR(MAX) o VARCHAR(MAX).

## Limitazione PolyBase

Se si usa Polybase per caricare le tabelle, definire le tabelle in modo che le dimensioni massime possibili della riga, inclusa la lunghezza totale delle colonne a lunghezza variabile, non superino 32.767 byte. Anche se è possibile definire una riga con dati a lunghezza variabile che possono superare questa larghezza e caricare righe con BCP, non è possibile usare PolyBase per caricare i dati. Presto verrà ampliato il supporto di PolyBase per righe ampie.

## Tipi di dati non supportati

Se si esegue la migrazione del database da un'altra piattaforma SQL come il database SQL di Azure, durante la migrazione è possibile incontrare tipi di dati non supportati in SQL Data Warehouse. Di seguito sono riportati i tipi di dati non supportati e alcune alternative che è possibile usare al loro posto.

|Tipo di dati|Soluzione alternativa|
|---|---|
|[geometry][]|[varbinary][]|
|[geography][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][](4000)|
|[immagine][ntext,text,image]|[varbinary][]|
|[text][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql\_variant][]|Dividere la colonna in più colonne fortemente tipizzate.|
|[tabella][]|Convertire in tabelle temporanee.|
|[timestamp][]|Rielaborare il codice per l'uso di [datetime2][] e della funzione `CURRENT_TIMESTAMP`. Solo le costanti sono supportate come valori predefiniti, quindi non è possibile definire current\_timestamp come vincolo predefinito. Se è necessario eseguire la migrazione di valori della versione di riga da una colonna di tipo timestamp, usare [BINARY][](8) o [VARBINARY][BINARY](8) per valori della versione di riga NOT NULL o NULL.|
|[xml][]|[varchar][]|
|[tipi definiti dall'utente][]|Riconvertirli nei tipi nativi corrispondenti, se possibile.|
|valori predefiniti|I valori predefiniti supportano solo valori letterali e costanti. Le espressioni o le funzioni non deterministiche, ad esempio `GETDATE()` o `CURRENT_TIMESTAMP`, non sono supportate.|

Il codice SQL riportato di seguito può essere eseguito nel database SQL corrente per identificare le colonne non supportate da Azure SQL Data Warehouse:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## Passaggi successivi

Per altre informazioni, vedere gli articoli [Overview of tables in SQL Data Warehouse][Overview] (Panoramica sulle tabelle in SQL Data Warehouse), [Distribuzione di tabelle in SQL Data Warehouse][Distribute], [Indicizzazione di tabelle in SQL Data Warehouse][Index], [Partitioning tables in SQL Data Warehouse][Partition] (Partizionamento di tabelle in SQL Data Warehouse), [Managing statistics on tables in SQL Data Warehouse][Statistics] (Gestione delle statistiche nelle tabelle in SQL Data Warehouse) e [Temporary tables in SQL Data Warehouse][Temporary] (Tabelle temporanee in SQL Data Warehouse). Per altre informazioni sulle procedure consigliate, vedere [Procedure consigliate per Azure SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Panoramica]: ./sql-data-warehouse-tables-overview.md
[Tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribuzione]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporanee]: ./sql-data-warehouse-tables-temporary.md
[Procedure consigliate per Azure SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md

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
[sql\_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[tabella]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[tipi definiti dall'utente]: https://msdn.microsoft.com/library/ms131694.aspx

<!---HONumber=AcomDC_0706_2016-->