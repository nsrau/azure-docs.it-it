---
title: Tabelle di partizionamento in SQL Data Warehouse | Microsoft Docs
description: Introduzione al partizionamento delle tabelle di SQL Data Warehouse di Azure.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: a28cb1f8a2e48332b344566620dc49b29d9d3c99
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Tabelle di partizionamento in SQL Data Warehouse
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

Il partizionamento è supportato in tutti i tipi di tabella di SQL Data Warehouse, tra cui columnstore cluster, indice cluster e heap.  Il partizionamento è supportato anche in tutti i tipi di distribuzione, sia hash che round robin.  Il partizionamento consente di dividere i dati in gruppi di dati più piccoli e nella maggior parte dei casi il partizionamento viene effettuato su una colonna relativa alla data.

## <a name="benefits-of-partitioning"></a>Vantaggi del partizionamento
Il partizionamento può recare vantaggio alle prestazioni di query e di conservazione dei dati.  Il fatto di recare vantaggio a entrambi i tipi di prestazioni o solo a uno dei due dipende dalla modalità di caricamento dei dati e dalla possibilità di usare la stessa colonna per entrambi gli scopi, poiché il partizionamento può essere eseguito solo su una colonna.

### <a name="benefits-to-loads"></a>Vantaggi in termini di caricamento
Il vantaggio principale del partizionamento in SQL Data Warehouse è aumentare l'efficienza e le prestazioni di caricamento dei dati usando l'eliminazione, il cambio e l'unione delle partizioni.  Nella maggior parte dei casi viene eseguito il partizionamento dei dati in una colonna di date strettamente legata all'ordine in cui i dati vengono caricati nel database.  Uno dei principali vantaggi dell'uso di partizioni per conservare i dati è che evita la registrazione delle transazioni.  Mentre le semplici operazioni di inserimento, aggiornamento o eliminazione dei dati possono rappresentare l'approccio più semplice, con un po' di impegno e di ragionamento, l'uso del partizionamento durante il processo di caricamento può migliorare notevolmente le prestazioni.

Il cambio di partizioni consente di rimuovere o sostituire rapidamente una sezione di tabella.  Ad esempio, una tabella dei fatti delle vendite potrebbe contenere solo i dati relativi agli ultimi 36 mesi.  Alla fine di ogni mese, il mese dei dati di vendita meno recenti viene eliminato dalla tabella.  Questi dati potrebbero essere eliminati tramite un'istruzione delete per eliminare i dati relativi al mese meno recente.  Tuttavia, l'eliminazione di una grande quantità di dati riga per riga con un'istruzione delete può richiedere troppo tempo e inoltre creare il rischio di transazioni di grandi dimensioni il cui rollback può richiedere molto tempo nel caso in cui qualcosa andasse storto.  Un approccio più appropriato consiste nel rilasciare la partizione dei dati meno recente.  Nei casi in cui l'eliminazione delle singole righe arrivasse a richiedere alcune ore, l'eliminazione di un'intera partizione potrebbe richiedere pochi secondi.

### <a name="benefits-to-queries"></a>Vantaggi in termini di query
Il partizionamento può essere usato anche per aumentare le prestazioni delle query.  Una query che applica un filtro ai dati partizionati può limitare l'analisi solo alle partizioni idonee. Questo metodo di filtraggio può evitare un'analisi completa della tabella e analizzare solo un subset di dati più piccolo. Con l'introduzione di indici columnstore cluster, i vantaggi delle prestazioni di eliminazione del predicato sono meno utili, ma in alcuni casi possono esserci vantaggi per le query.  Ad esempio, se la tabella dei fatti delle vendite è suddivisa in 36 mesi tramite il campo della data di vendita, le query che filtrano la data di vendita possono ignorare la ricerca nelle partizioni che non corrispondono al filtro.

## <a name="partition-sizing-guidance"></a>Indicazioni sul dimensionamento delle partizioni
Mentre il partizionamento può essere usato per aumentare le prestazioni di alcuni scenari, in alcune circostanze la creazione di una tabella con **troppe** partizioni può influire negativamente sulle prestazioni.  Questi problemi valgono soprattutto per le tabelle columnstore cluster.  Affinché il partizionamento sia utile, per un amministratore di database è importante capire quando usare il partizionamento e il numero di partizioni da creare.  Non esiste una regola assoluta che chiarisca cosa si intende per troppe partizioni, perché dipende dai dati e dal numero di partizioni caricate contemporaneamente.  In genere, uno schema di partizione corretto ha decine di centinaia di partizioni, non migliaia.

Quando si creano partizioni in tabelle **columnstore cluster**, è importante tenere in considerazione il numero di righe appartenenti a ogni partizione.  Per ottenere risultati ottimali in termini di compressione e prestazioni delle tabelle columnstore cluster, è necessario almeno 1 milione di righe per distribuzione e partizione.  Prima della creazione delle partizioni, SQL Data Warehouse divide già ogni tabella in 60 database distribuiti.  Eventuali partizionamenti aggiunti a una tabella sono in più rispetto alle distribuzioni create in background.  Utilizzando questo esempio, se la tabella dei fatti delle vendite contenesse 36 partizioni mensili e dato che SQL Data Warehouse dispone di 60 distribuzioni, la tabella dei fatti delle vendite dovrebbe contenere 60 milioni di righe al mese o 2,1 milioni di righe quando tutti i mesi sono popolati.  Se una tabella contiene un numero di righe significativamente inferiore a quello minimo consigliato per partizione, è necessario prendere in considerazione l'uso di un minor numero di partizioni per aumentare il numero di righe per partizione.  Vedere anche l'articolo sull'[indicizzazione][Index], che include le query che possono essere eseguite in SQL Data Warehouse per valutare la qualità degli indici columnstore cluster.

## <a name="syntax-difference-from-sql-server"></a>Differenze di sintassi rispetto a SQL Server
SQL Data Warehouse introduce un metodo di definizione semplificata delle partizioni, leggermente diversa da SQL Server.  Le funzioni e gli schemi di partizionamento non vengono usati in SQL Data Warehouse come in SQL Server.  Piuttosto, è necessario identificare la colonna partizionata e le delimitazioni.  Mentre la sintassi del partizionamento può essere leggermente diversa da quella di SQL Server, i concetti di base sono gli stessi.  SQL Server e SQL Data Warehouse supportano una colonna di partizione per tabella, che può essere il partizionamento con intervallo.  Per altre informazioni sul partizionamento, vedere [Tabelle e indici partizionati][Partitioned Tables and Indexes].

L'esempio seguente di istruzione [CREATE TABLE][CREATE TABLE] con partizionamento di SQL Data Warehouse esegue il partizionamento della tabella FactInternetSales nella colonna OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrazione del partizionamento da SQL Server
Per eseguire la migrazione delle definizioni delle partizioni di SQL Server a SQL Data Warehouse, è necessario semplicemente:

* Eliminare lo [schema di partizione][partition scheme] di SQL Server.
* Aggiungere la definizione di [funzione di partizione][partition function] all'istruzione CREATE TABLE.

Se si sta migrando una tabella con partizionamento da un'istanza di SQL Server, l'SQL di seguito è utile per calcolare il numero di righe in ogni partizione.  È necessario tenere presente che se viene usata la stessa granularità di partizionamento in SQL Data Warehouse, il numero di righe per partizione diminuisce di un fattore pari a 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gestione del carico di lavoro
Un'ultima considerazione da tenere presente nella decisione relativa alla partizione della tabella riguarda la [gestione del carico di lavoro][workload management].  La gestione del carico di lavoro in SQL Data Warehouse è principalmente la gestione di memoria e concorrenza.  In SQL Data Warehouse la memoria massima allocata a ogni distribuzione durante l'esecuzione della query è controllata dalle classi di risorse.  In teoria le dimensioni delle partizioni devono tenere in considerazione altri fattori come la memoria necessaria per creare indici columnstore cluster.  Gli indici columnstore cluster traggono importanti vantaggi quando la memoria allocata è maggiore.  È quindi opportuno verificare che la ricompilazione dell'indice della partizione non esaurisca la memoria. Per ottenere l'aumento della memoria disponibile per la query, è possibile passare dal ruolo predefinito, smallrc, a uno degli altri ruoli, ad esempio largerc.

Le informazioni sull'allocazione di memoria per ogni distribuzione sono disponibili mediante l'esecuzione di una query sulle viste a gestione dinamica di Resource Governor. In realtà la concessione di memoria è inferiore rispetto alle cifre seguenti. Fornisce tuttavia un livello di indicazioni che è possibile usare durante il dimensionamento delle partizioni per le operazioni di gestione dati.  Evitare se possibile di dimensionare le partizioni oltre la concessione di memoria fornita dalla classe di risorse molto grande. Se le partizioni aumentano oltre questa cifra, si rischia un utilizzo elevato di memoria che a sua volta determina una compressione non ottimale.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Cambio di partizione
SQL Data Warehouse supporta la suddivisione, l'unione e il cambio di partizioni. Ognuna di queste funzioni viene eseguita usando l'istruzione [ALTER TABLE][ALTER TABLE].

Per il cambio di partizione tra due tabelle, è necessario verificare che le partizioni siano allineate sui rispettivi limiti e che le definizioni delle tabelle corrispondano. Poiché non sono disponibili vincoli CHECK per imporre l'intervallo di valori in una tabella, la tabella di origine deve contenere gli stessi limiti di partizione della tabella di destinazione. In caso contrario, il cambio di partizione non riuscirà, perché i metadati della partizione non verranno sincronizzati.

### <a name="how-to-split-a-partition-that-contains-data"></a>Come suddividere una partizione che contiene dati
Il metodo più efficiente per suddividere una partizione che contiene già dati, consiste nell'usare un'istruzione `CTAS` . Se la tabella partizionata è un columnstore cluster, la partizione della tabella deve essere vuota per poterla suddividere.

Di seguito è riportato un esempio di tabella columnstore contenente una riga in ogni partizione:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> Con la creazione dell'oggetto Statistic, si assicura una maggiore accuratezza dei metadati della tabella. Se si omette la creazione di statistiche, SQL Data Warehouse userà i valori predefiniti. Per altre informazioni, vedere l'articolo relativo alla gestione delle [statistiche][statistics].
> 
> 

È quindi possibile eseguire una query per il conteggio delle righe usando la vista del catalogo `sys.partitions` :

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Se si tenta di suddividere questa tabella, verrà restituito un errore:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Messaggio 35346, livello 15, stato 1, riga 44 Clausola SPLIT dell'istruzione ALTER PARTITION non riuscita perché la partizione non è vuota.  Solo le partizioni vuote possono essere suddivise quando nella tabella è presente un indice columnstore. Provare a disabilitare l'indice columnstore prima di eseguire l'istruzione ALTER PARTITION, quindi ricompilare l'indice columnstore dopo il completamento di ALTER PARTITION.

È tuttavia possibile usare `CTAS` per creare una nuova tabella per contenere i dati.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Poiché i limiti della partizione sono allineati, il cambio è consentito. In questo modo la tabella di origine avrà una partizione vuota che in seguito si potrà suddividere.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

A questo punto è sufficiente allineare i dati ai nuovi limiti di partizione usando `CTAS` e ritrasferire i dati nella tabella principale.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Dopo aver completato lo spostamento dei dati, è consigliabile aggiornare le statistiche nella tabella di destinazione per assicurare che riflettano accuratamente la nuova distribuzione dei dati nelle rispettive partizioni:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Controllo del codice sorgente del partizionamento della tabella
Per evitare che la definizione della tabella si **stabilisca** nel sistema di controllo del codice sorgente, è possibile considerare l'approccio seguente:

1. Creare la tabella come tabella partizionata, ma senza valori di partizione.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. `SPLIT` per suddividere la tabella come parte del processo di distribuzione:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Con questo approccio, il codice nel controllo del codice sorgente rimane statico, mentre i valori dei limiti del partizionamento possono essere dinamici, evolvendo con il warehouse nel tempo.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere gli articoli su [panoramica delle tabelle][Overview], [tipi di dati delle tabelle][Data Types], [distribuzione di una tabella][Distribute], [indicizzazione di una tabella][Index], [conservazione delle statistiche delle tabelle][Statistics] e [tabelle temporanee][Temporary].  Per altre informazioni sulle procedure consigliate, vedere [Procedure consigliate per SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
