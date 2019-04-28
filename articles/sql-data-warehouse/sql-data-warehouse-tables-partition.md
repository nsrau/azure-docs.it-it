---
title: Tabelle di partizionamento in Azure SQL Data Warehouse | Microsoft Docs
description: Raccomandazioni ed esempi per l'uso di partizioni della tabella in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/18/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d3557be2fd8fdb459571d2c792302963e17e4471
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935881"
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Tabelle di partizionamento in SQL Data Warehouse
Raccomandazioni ed esempi per l'uso di partizioni della tabella in Azure SQL Data Warehouse per lo sviluppo di soluzioni.

## <a name="what-are-table-partitions"></a>Introduzione alle partizioni di tabella
Le partizioni di tabella consentono di suddividere i dati in gruppi più piccoli. Nella maggior parte dei casi, le partizioni di tabella vengono create in una colonna data. Il partizionamento è supportato in tutti i tipi di tabella di SQL Data Warehouse, tra cui columnstore cluster, indice cluster e heap. Il partizionamento è supportato anche in tutti i tipi di distribuzione, sia hash che round robin.  

Il partizionamento può recare vantaggio alle prestazioni di query e di conservazione dei dati. Il fatto di recare vantaggio a entrambi i tipi di prestazioni o solo a uno dei due dipende dalla modalità di caricamento dei dati e dalla possibilità di usare la stessa colonna per entrambi gli scopi, poiché il partizionamento può essere eseguito solo su una colonna.

### <a name="benefits-to-loads"></a>Vantaggi in termini di caricamento
Il vantaggio principale del partizionamento in SQL Data Warehouse è aumentare l'efficienza e le prestazioni di caricamento dei dati usando l'eliminazione, il cambio e l'unione delle partizioni. Nella maggior parte dei casi viene eseguito il partizionamento dei dati in una colonna di date strettamente legata all'ordine in cui i dati vengono caricati nel database. Uno dei principali vantaggi dell'uso di partizioni per conservare i dati è che evita la registrazione delle transazioni. Mentre le semplici operazioni di inserimento, aggiornamento o eliminazione dei dati possono rappresentare l'approccio più semplice, con un po' di impegno e di ragionamento, l'uso del partizionamento durante il processo di caricamento può migliorare notevolmente le prestazioni.

Il cambio di partizioni consente di rimuovere o sostituire rapidamente una sezione di tabella.  Ad esempio, una tabella dei fatti delle vendite potrebbe contenere solo i dati relativi agli ultimi 36 mesi. Alla fine di ogni mese, il mese dei dati di vendita meno recenti viene eliminato dalla tabella.  Questi dati potrebbero essere eliminati tramite un'istruzione delete per eliminare i dati relativi al mese meno recente. Tuttavia, l'eliminazione di una grande quantità di dati riga per riga con un'istruzione delete può richiedere troppo tempo e inoltre creare il rischio di transazioni di grandi dimensioni il cui rollback può richiedere molto tempo nel caso in cui qualcosa andasse storto. Un approccio più appropriato consiste nel rilasciare la partizione dei dati meno recente. Nei casi in cui l'eliminazione delle singole righe arrivasse a richiedere alcune ore, l'eliminazione di un'intera partizione potrebbe richiedere pochi secondi.

### <a name="benefits-to-queries"></a>Vantaggi in termini di query
Il partizionamento può essere usato anche per aumentare le prestazioni delle query. Una query che applica un filtro ai dati partizionati può limitare l'analisi solo alle partizioni idonee. Questo metodo di filtraggio può evitare un'analisi completa della tabella e analizzare solo un subset di dati più piccolo. Con l'introduzione di indici columnstore cluster, i vantaggi delle prestazioni di eliminazione del predicato sono meno utili, ma in alcuni casi possono esserci vantaggi per le query. Ad esempio, se la tabella dei fatti delle vendite è suddivisa in 36 mesi tramite il campo della data di vendita, le query che filtrano la data di vendita possono ignorare la ricerca nelle partizioni che non corrispondono al filtro.

## <a name="sizing-partitions"></a>Ridimensionamento delle partizioni
Mentre il partizionamento può essere usato per aumentare le prestazioni di alcuni scenari, in alcune circostanze la creazione di una tabella con **troppe** partizioni può influire negativamente sulle prestazioni.  Questi problemi valgono soprattutto per le tabelle columnstore cluster. Affinché il partizionamento sia utile, per un amministratore di database è importante capire quando usare il partizionamento e il numero di partizioni da creare. Non esiste una regola assoluta che chiarisca cosa si intende per troppe partizioni, perché dipende dai dati e dal numero di partizioni caricate contemporaneamente. In genere, uno schema di partizione corretto ha decine di centinaia di partizioni, non migliaia.

Quando si creano partizioni in tabelle **columnstore cluster**, è importante tenere in considerazione il numero di righe appartenenti a ogni partizione. Per ottenere risultati ottimali in termini di compressione e prestazioni delle tabelle columnstore cluster, è necessario almeno 1 milione di righe per distribuzione e partizione. Prima della creazione delle partizioni, SQL Data Warehouse divide già ogni tabella in 60 database distribuiti. Eventuali partizionamenti aggiunti a una tabella sono in più rispetto alle distribuzioni create in background. Utilizzando questo esempio, se la tabella dei fatti delle vendite contenesse 36 partizioni mensili e dato che SQL Data Warehouse dispone di 60 distribuzioni, la tabella dei fatti delle vendite dovrebbe contenere 60 milioni di righe al mese o 2,1 milioni di righe quando tutti i mesi sono popolati. Se una tabella contiene un numero di righe inferiore a quello minimo consigliato per partizione, è necessario prendere in considerazione l'uso di un minor numero di partizioni per aumentare il numero di righe per partizione. Per altre informazioni, vedere anche l'articolo sull'[indicizzazione](sql-data-warehouse-tables-index.md), che include le query che possono valutare la qualità degli indici columnstore cluster.

## <a name="syntax-differences-from-sql-server"></a>Differenze di sintassi rispetto a SQL Server
SQL Data Warehouse introduce una modalità per definire le partizioni più semplice rispetto a SQL Server. Le funzioni e gli schemi di partizionamento non vengono usati in SQL Data Warehouse come in SQL Server. Piuttosto, è necessario identificare la colonna partizionata e le delimitazioni. Mentre la sintassi del partizionamento può essere leggermente diversa da quella di SQL Server, i concetti di base sono gli stessi. SQL Server e SQL Data Warehouse supportano una colonna di partizione per tabella, che può essere il partizionamento con intervallo. Per altre informazioni sul partizionamento, vedere [Tabelle e indici partizionati](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

L'esempio seguente usa l'istruzione [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) per eseguire il partizionamento della tabella FactInternetSales nella colonna OrderDateKey:

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

- Eliminare lo [schema di partizione](/sql/t-sql/statements/create-partition-scheme-transact-sql)di SQL Server.
- Aggiungere la definizione di [funzione di partizione](/sql/t-sql/statements/create-partition-function-transact-sql) all'istruzione CREATE TABLE.

Se si sta migrando una tabella con partizionamento da un'istanza di SQL Server, l'SQL di seguito è utile per calcolare il numero di righe in ogni partizione. È necessario tenere presente che se viene usata la stessa granularità di partizionamento in SQL Data Warehouse, il numero di righe per partizione diminuisce di un fattore pari a 60.  

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

## <a name="partition-switching"></a>Cambio di partizione
SQL Data Warehouse supporta la suddivisione, l'unione e il cambio di partizioni. Ognuna di queste funzioni viene eseguita usando l'istruzione [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

Per il cambio di partizione tra due tabelle, è necessario verificare che le partizioni siano allineate sui rispettivi limiti e che le definizioni delle tabelle corrispondano. Poiché non sono disponibili vincoli CHECK per imporre l'intervallo di valori in una tabella, la tabella di origine deve contenere gli stessi limiti di partizione della tabella di destinazione. Se i limiti di partizione non sono uguali, il cambio di partizione non riuscirà, perché i metadati della partizione non verranno sincronizzati.

### <a name="how-to-split-a-partition-that-contains-data"></a>Come suddividere una partizione che contiene dati
Il metodo più efficiente per suddividere una partizione che contiene già dati, consiste nell'usare un'istruzione `CTAS` . Se la tabella partizionata è un columnstore cluster, la partizione della tabella deve essere vuota per poterla suddividere.

L'esempio seguente crea una tabella columnstore partizionata. Inserisce una riga in ogni partizione:

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
```

La query seguente consente di trovare il numero delle righe usando la vista del catalogo `sys.partitions`:

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

Il comando di divisione seguente riceve un messaggio di errore:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Messaggio 35346, livello 15, stato 1, riga 44 Clausola SPLIT dell'istruzione ALTER PARTITION non riuscita perché la partizione non è vuota. Solo le partizioni vuote possono essere suddivise quando nella tabella è presente un indice columnstore. Provare a disabilitare l'indice columnstore prima di eseguire l'istruzione ALTER PARTITION, quindi ricompilare l'indice columnstore dopo il completamento di ALTER PARTITION.

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

Dopo aver completato lo spostamento dei dati, è consigliabile aggiornare le statistiche nella tabella di destinazione. L'aggiornamento delle statistiche assicura che le statistiche rispecchino in modo accurato la nuova distribuzione dei dati nelle rispettive partizioni.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Caricare nuovi dati in partizioni che contengono dati in un unico passaggio
Il caricamento dei dati nelle partizioni con cambio della partizione è un modo pratico la gestione temporanea nuovi dati in una tabella che non è visibile agli utenti l'opzione nei nuovi dati.  Può risultare difficile in sistemi molto sfruttati per affrontare la contesa tra blocchi associata con cambio della partizione.  Per cancellare i dati esistenti in una partizione, un `ALTER TABLE` erano necessarie per passare i dati.  Quindi un'altra `ALTER TABLE` era necessario passare i nuovi dati.  In SQL Data Warehouse, il `TRUNCATE_TARGET` opzione è supportata nel `ALTER TABLE` comando.  Con `TRUNCATE_TARGET` il `ALTER TABLE` comando sovrascrive dati esistenti nella partizione con i nuovi dati.  Di seguito è riportato un esempio che usa `CTAS` per creare una nuova tabella con i dati esistenti, consente di inserire nuovi dati, quindi torna di tutti i dati nella tabella di destinazione, sovrascrivendo i dati esistenti.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Controllo del codice sorgente del partizionamento della tabella
Per evitare che la definizione della tabella si **fossilizzi** nel sistema di controllo del codice sorgente, è possibile considerare l'approccio seguente:

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
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
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
Per altre informazioni sullo sviluppo di tabelle, vedere gli articoli sui [cenni preliminari sulle tabelle](sql-data-warehouse-tables-overview.md).

