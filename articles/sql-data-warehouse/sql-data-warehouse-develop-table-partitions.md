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
   ms.date="03/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Partizioni della tabella in SQL Data Warehouse

Per eseguire la migrazione delle definizioni delle partizioni di SQL Server a SQL Data Warehouse:

- Rimuovere le funzioni e gli schemi di partizione di SQL Server, perché questa operazione viene eseguita automaticamente quando si crea la tabella.
- Definire le partizioni quando si crea la tabella. Specificare semplicemente le delimitazioni della partizione e se si vuole che la delimitazione abbia effetto come `RANGE RIGHT` o `RANGE LEFT`.

NOTA: per altre informazioni su SQL Server, vedere [Tabelle e indici partizionati](https://msdn.microsoft.com/library/ms190787.aspx).


### Dimensionamento della partizione
SQL DW offre a un amministratore di database diverse opzioni per i tipi di tabella: heap, indice cluster (CI) e indice archivio colonne in cluster (ICC). Per ognuno di questi tipi di tabella, l'amministratore di database può inoltre partizionare la tabella, ovvero suddividerla in più sezioni per migliorare le prestazioni. Tuttavia, la creazione di una tabella con un numero eccessivo di partizioni in realtà potrebbe provocare un calo delle prestazioni o errori di query in alcune circostanze. Questi problemi valgono soprattutto per le tabelle ICC. Affinché il partizionamento sia utile, per un amministratore di database è importante capire quando utilizzare il partizionamento e il numero di partizioni da creare. Queste linee guida consentono agli amministratori di database di prendere le decisioni migliori per i loro scenari.

In genere le partizioni della tabella sono utili in due modi principali:

1. L’utilizzo del cambio di partizione per troncare rapidamente una sezione di una tabella. Un design di uso comune prevede una tabella dei fatti che contiene righe solo per un periodo limitato predeterminato. Ad esempio, una tabella dei fatti delle vendite potrebbe contenere dati solo per gli ultimi 36 mesi. Alla fine di ogni mese, il mese dei dati di vendita meno recenti viene eliminato dalla tabella. Questa operazione può essere effettuata semplicemente eliminando tutte le righe per il mese meno recente, ma l'eliminazione di una grande quantità di dati riga per riga può richiedere molto tempo. Per ottimizzare questo scenario, SQL DW supporta lo scambio di partizione, che consente di eliminare l'intero set di righe in una partizione in un'unica operazione veloce.   

2. Il partizionamento consente alle query di escludere facilmente l’elaborazione di un elevato numero di righe (ad esempio una partizione) se le query inseriscono un predicato nella colonna di partizionamento. Ad esempio, se la tabella dei fatti delle vendite è suddivisa in 36 mesi tramite il campo della data di vendita, le query che filtrano la data di vendita possono ignorare l’elaborazione delle partizioni che non corrispondono al filtro. In effetti, il partizionamento utilizzato in questo modo è un’indicizzazione con granularità grossolana.

Durante la creazione di indici dell'archivio colonne in cluster in SQL DW, un amministratore di database deve considerare un fattore aggiuntivo: il numero di riga. Le tabelle CCI possono raggiungere un elevato livello di compressione e consentono di accelerare le prestazioni delle query. A causa del funzionamento interno della compressione in SQL DW, ogni partizione in una tabella CCI deve avere un notevole numero di righe prima che i dati vengono compressi. Inoltre, SQL DW distribuisce i dati tra un numero elevato di distribuzioni e ogni distribuzione è ulteriormente suddivisa in partizioni. Per prestazioni e compressione ottimali, è necessario un minimo di 100.000 righe per partizione e distribuzione. Utilizzando l'esempio precedente, se la tabella dei fatti delle vendite contenesse 36 partizioni mensili e dato che SQL DW dispone di 60 distribuzioni, la tabella dei fatti delle vendite dovrebbe contenere 6 milioni di righe al mese o 216 milioni di righe quando tutti i mesi sono popolati. Se una tabella contiene un numero di righe significativamente inferiore a quello minimo consigliato, l'amministratore di database dovrebbe considerare la creazione della tabella con un minor numero di partizioni per aumentare il numero di righe per distribuzione.


Per dimensionare il database SQL Server corrente a livello di partizione, usare una query simile alla seguente:

```sql
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

Il numero totale di distribuzioni è uguale al numero di posizioni di archiviazione usate quando si crea una tabella. È un modo complesso per dire che ogni tabella viene creata una volta per ogni distribuzione.

È anche possibile prevedere approssimativamente quante righe conterrà e quindi quanto sarà grande una partizione. La partizione nel data warehouse di origine verrà suddivisa in ogni distribuzione.

Usare il calcolo seguente per determinare più facilmente le dimensioni della partizione:

Dimensioni della partizione MPP = Dimensioni della partizione SMP / Numero di distribuzioni

Per scoprire il numero di distribuzioni contenute nel database di SQL Data Warehouse, usare la query seguente:

```sql
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

Dopo avere stabilito le dimensioni di ogni partizione nel sistema di origine e le dimensioni previste per SQL Data Warehouse, è possibile decidere il limite della partizione.

### Gestione del carico di lavoro
Un'ultima informazione che occorre considerare nella decisione relativa alla partizione della tabella riguarda la gestione del carico di lavoro. In SQL Data Warehouse la memoria massima allocata a ogni distribuzione durante l'esecuzione della query è controllata da questa funzionalità. Per altre informazioni, vedere l'articolo seguente relativo alla [gestione del carico di lavoro]. Idealmente, nel definire le dimensioni della partizione è opportuno considerare le operazioni in memoria, come le ricompilazioni degli indici columnstore. La ricompilazione di un indice è un'operazione a elevato utilizzo di memoria. È quindi opportuno verificare che la ricompilazione dell'indice della partizione non esaurisca la memoria. Per ottenere l'aumento della memoria disponibile per la query, è possibile passare dal ruolo predefinito a uno degli altri ruoli disponibili.

Le informazioni sull'allocazione di memoria per ogni distribuzione sono disponibili mediante l'esecuzione di una query sulle viste a gestione dinamica di Resource Governor. In realtà la concessione di memoria sarà inferiore rispetto alle cifre seguenti. Fornisce tuttavia un livello di indicazioni che è possibile usare durante il dimensionamento delle partizioni per le operazioni di gestione dati.

```sql
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
;
```

> [AZURE.NOTE] Evitare se possibile di dimensionare le partizioni oltre la concessione di memoria fornita dalla classe di risorse molto grande. Se le partizioni aumentano oltre questa cifra, si rischia un utilizzo elevato di memoria che a sua volta determina una compressione non ottimale.

## Cambio di partizione
Per il cambio di partizione tra due tabelle, è necessario verificare che le partizioni siano allineate sui rispettivi limiti e che le definizioni delle tabelle corrispondano. Poiché non sono disponibili vincoli CHECK per imporre l'intervallo di valori in una tabella, la tabella di origine deve contenere gli stessi limiti di partizione della tabella di destinazione. In caso contrario, il cambio di partizione non riuscirà, perché i metadati della partizione non vengono sincronizzati.

### Come suddividere una partizione che contiene dati
Il metodo più efficiente per suddividere una partizione che contiene già dati, consiste nell'usare un'istruzione `CTAS`. Se la tabella partizionata è un columnstore cluster, la partizione della tabella deve essere vuota per poterla suddividere.

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

> [AZURE.NOTE] Con la creazione dell'oggetto Statistic, si assicura una maggiore accuratezza dei metadati della tabella. Se si omette la creazione di statistiche, SQL Data Warehouse userà i valori predefiniti. Per altre informazioni, vedere l'articolo relativo alla gestione delle [statistiche][].

È quindi possibile eseguire una query per il conteggio delle righe usando la vista del catalogo `sys.partitions`:

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

Dopo aver completato lo spostamento dei dati, è consigliabile aggiornare le statistiche nella tabella di destinazione per assicurare che riflettano accuratamente la nuova distribuzione dei dati nelle rispettive partizioni:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### Controllo del codice sorgente del partizionamento della tabella
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

2. Usare `SPLIT` per suddividere la tabella come parte del processo di distribuzione:

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

>[AZURE.NOTE] Il cambio di partizione presenta alcune differenze rispetto a SQL Server. Per altre informazioni su questo argomento, vedere [Eseguire la migrazione del codice][].


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

<!---HONumber=AcomDC_0330_2016-->