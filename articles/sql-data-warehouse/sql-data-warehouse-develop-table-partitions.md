<properties
   pageTitle="Partizioni della tabella in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di partizioni della tabella in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Partizioni della tabella in SQL Data Warehouse

Per eseguire la migrazione delle definizioni delle partizioni di SQL Server a SQL Data Warehouse:

- Rimuovere le funzioni e gli schemi di partizione di SQL Server, perché questa operazione viene eseguita automaticamente quando si crea la tabella.
- Definire le partizioni quando si crea la tabella. Specificare semplicemente le delimitazioni della partizione e se si vuole che la delimitazione abbia effetto come `RANGE RIGHT` o `RANGE LEFT`.

### Dimensionamento della partizione
Il dimensionamento della partizione è un aspetto importante da considerare per SQL Data Warehouse. In genere le operazioni di gestione dati e le routine di caricamento dei dati sono destinate a singole partizioni, piuttosto che a un'intera tabella contemporaneamente. Questo aspetto è particolarmente importante per gli indici columnstore cluster, che possono utilizzare quantità di memoria significative. Di conseguenza, mentre potrebbe essere utile esaminare la granularità del piano di partizionamento, non è consigliabile dimensionare le partizioni su valori tali da causare un utilizzo elevato di memoria quando si eseguono attività di gestione.

Nel decidere la granularità delle partizioni, è importante ricordare che SQL Data Warehouse suddividerà automaticamente i dati in distribuzioni. Di conseguenza, i dati che sarebbero stati normalmente disponibili in una tabella in una partizione di un database di SQL Server, ora sono disponibili in una partizione in molte tabelle di un database di SQL Data Warehouse. Per mantenere un numero significativo di righe in ogni partizione, si modificano in genere le dimensioni dei limiti della partizione. Se ad esempio è stato usato il partizionamento a livello giornaliero per il data warehouse, è consigliabile considerare qualcosa di meno granulare, ad esempio mensile o trimestrale.

Per dimensionare il database corrente a livello di partizione, usare una query simile alla seguente:

```
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
JOIN        sys.allocation_units a          ON      a.[container_id]        = p.[partition_id]
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

Il numero totale di distribuzioni è uguale al numero di posizioni di archiviazione usate quando si crea una tabella. È un modo complesso per dire che ogni tabella viene creata una volta per ogni distribuzione.

È anche possibile prevedere approssimativamente quante righe conterrà e quindi quanto sarà grande una partizione. La partizione nel data warehouse di origine verrà suddivisa in ogni distribuzione.

Usare il calcolo seguente per determinare più facilmente le dimensioni della partizione:

Dimensioni della partizione MPP = Dimensioni della partizione SMP / Numero di distribuzioni

Per scoprire il numero di distribuzioni contenute nel database di SQL Data Warehouse, usare la query seguente:

```
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

Dopo avere stabilito le dimensioni di ogni partizione nel sistema di origine e le dimensioni previste per SQL Data Warehouse, è possibile decidere il limite della partizione.

### Gestione del carico di lavoro
Un'ultima informazione che occorre considerare nella decisione relativa alla partizione della tabella riguarda la gestione del carico di lavoro. In SQL Data Warehouse la memoria massima allocata a ogni distribuzione durante l'esecuzione della query è controllata da questa funzionalità. Per altre informazioni, vedere l'articolo seguente relativo alla [gestione del carico di lavoro]. Idealmente, nel definire le dimensioni della partizione è opportuno considerare le operazioni in memoria, come le ricompilazioni degli indici columnstore. La ricompilazione di un indice è un'operazione a elevato utilizzo di memoria. È quindi opportuno verificare che la ricompilazione dell'indice della partizione non esaurisca la memoria. Per ottenere l'aumento della memoria disponibile per la query, è possibile passare dal ruolo predefinito a uno degli altri ruoli disponibili.

Le informazioni sull'allocazione di memoria per ogni distribuzione sono disponibili mediante l'esecuzione di una query sulle viste a gestione dinamica di Resource Governor. In realtà la concessione di memoria sarà inferiore rispetto alle cifre seguenti. Fornisce tuttavia un livello di indicazioni che è possibile usare durante il dimensionamento delle partizioni per le operazioni di gestione dati.

```
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
```

> [AZURE.NOTE]Evitare se possibile di dimensionare le partizioni oltre la concessione di memoria fornita dalla classe di risorse molto grande. Se le partizioni aumentano oltre questa cifra, si rischia un utilizzo elevato di memoria che a sua volta determina una compressione non ottimale.

## Cambio di partizione
Per il cambio di partizione tra due tabelle, è necessario verificare che le partizioni siano allineate sui rispettivi limiti e che le definizioni delle tabelle corrispondano. Poiché non sono disponibili vincoli CHECK per imporre l'intervallo di valori in una tabella, la tabella di origine deve contenere gli stessi limiti di partizione della tabella di destinazione. In caso contrario, il cambio di partizione non riuscirà, perché i metadati della partizione non vengono sincronizzati.

### Come suddividere una partizione che contiene dati
Il metodo più efficiente per suddividere una partizione che contiene già dati, consiste nell'usare un'istruzione `CTAS`. Se la tabella partizionata è un columnstore cluster, la partizione della tabella deve essere vuota per poterla suddividere.

Di seguito è riportato un esempio di tabella columnstore contenente una riga nella partizione finale:

```
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
VALUES (1,20010101,1,1,1,1,1,1)

CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey)
```

> [AZURE.NOTE]Con la creazione dell'oggetto Statistic, si assicura una maggiore accuratezza dei metadati della tabella. Se si omette la creazione di statistiche, SQL Data Warehouse userà i valori predefiniti. Per altre informazioni, vedere l'articolo relativo alla gestione delle [statistiche][].

È quindi possibile eseguire una query per il conteggio delle righe usando la vista del catalogo `sys.partitions`:

```
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
```

Se si tenta di suddividere questa tabella, verrà restituito un errore:

```
ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

Messaggio 35346, livello 15, stato 1, riga 44 Clausola SPLIT dell'istruzione ALTER PARTITION non riuscita perché la partizione non è vuota. Solo le partizioni vuote possono essere suddivise quando nella tabella è presente un indice columnstore. Provare a disabilitare l'indice columnstore prima di eseguire l'istruzione ALTER PARTITION, quindi ricompilare l'indice columnstore dopo il completamento di ALTER PARTITION.

È tuttavia possibile usare `CTAS` per creare una nuova tabella per contenere i dati.

```
CREATE TABLE dbo.FactInternetSales_20010101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101
                                )
                            )
            )
AS
SELECT *
FROM	FactInternetSales
WHERE	1=2
```

Poiché i limiti della partizione sono allineati, il cambio è consentito. In questo modo la tabella di origine avrà una partizione vuota che in seguito si potrà suddividere.

```
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20010101 PARTITION 2

ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

A questo punto è sufficiente allineare i dati ai nuovi limiti di partizione usando `CTAS` e ritrasferire i dati nella tabella principale.

```
CREATE TABLE [dbo].[FactInternetSales_20010101_20020101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101,20020101
                                )
                            )
            )
AS
SELECT  *
FROM	[dbo].[FactInternetSales_20010101]
WHERE	[OrderDateKey] >= 20010101
AND     [OrderDateKey] <  20020101

ALTER TABLE FactInternetSales_20010101_20020101 SWITCH PARTITION 3 TO  FactInternetSales PARTITION 3
```

Dopo aver completato lo spostamento dei dati, è consigliabile aggiornare le statistiche nella tabella di destinazione per assicurare che riflettano accuratamente la nuova distribuzione dei dati nelle rispettive partizioni:

```
UPDATE STATISTICS [dbo].[FactInternetSales]
```

### Controllo del codice sorgente del partizionamento della tabella
Per evitare che la definizione della tabella si **stabilisca** nel sistema di controllo del codice sorgente, è possibile considerare l'approccio seguente:

1. Creare la tabella come tabella partizionata, ma senza valori di partizione.

```
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

2. Usare `SPLIT` per suddividere la tabella come parte del processo di distribuzione:

```
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
,       @i INT = 1                     --iterator for while loop
,       @q NVARCHAR(4000)              --query
,       @p NVARCHAR(20)     = N''      --partition_number
,       @s NVARCHAR(128)    = N'dbo'   --schema
,       @t NVARCHAR(128)    = N'table' --table
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

>[AZURE.NOTE]Il cambio di partizione presenta alcune differenze rispetto a SQL Server. Per altre informazioni su questo argomento, vedere [Eseguire la migrazione del codice][].


## Passaggi successivi
Dopo la migrazione dello schema del database a SQL Data Warehouse, è possibile passare a uno degli articoli seguenti:

- [Eseguire la migrazione dei dati][]
- [Eseguire la migrazione del codice][]

<!--Image references-->

<!-- Article references -->
[Eseguire la migrazione del codice]: sql-data-warehouse-migrate-code.md
[Eseguire la migrazione dei dati]: sql-data-warehouse-migrate-data.md
[statistiche]: sql-data-warehouse-develop-statistics.md
[gestione del carico di lavoro]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=July15_HO1-->