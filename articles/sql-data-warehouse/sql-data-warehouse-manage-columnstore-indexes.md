<properties
   pageTitle="Gestire gli indici columnstore in Azure SQL Data Warehouse | Microsoft Azure"
   description="Esercitazione per la gestione degli indici columnstore per migliorare le prestazioni di compressione e query in Azure SQL Data Warehouse."
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
   ms.date="04/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestire gli indici columnstore in Azure SQL Data Warehouse

In questa esercitazione viene illustrato come gestire gli indici columnstore per migliorare le prestazioni delle query.

Le query sugli indici columnstore vengono ottimizzate quando l'indice comprime le righe in "rowgroup" di un milione di righe (1.048.576, per la precisione). In questo modo vengono ottimizzate sia la compressione che le prestazioni delle query. Possono verificarsi tuttavia condizioni per cui i rowgroup contengono un numero di righe molto inferiore al milione. Quando i rowgroup non sono densamente popolati di righe, è consigliabile eseguire delle rettifiche.

In questa esercitazione si apprenderà come:

- Usare i metadati per determinare il numero medio di righe per ogni rowgroup
- Analizzare la causa radice della scarsa densità di righe nei rowgroup
- Ricompilare un indice columnstore per comprimere nuovamente tutte le righe in nuovi rowgroup 

Per apprendere le nozioni di base sugli indici columnstore, vedere la [guida agli indici columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## Passaggio 1: Creare una vista contenente i metadati dei rowgroup

Questa vista calcola la media di righe per rowgroup. Visualizza anche altre informazioni sui rowgroup.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

## Passaggio 2: Eseguire una query sulla vista

Dopo aver creato la vista, eseguire questa query di esempio per visualizzare i metadati del rowgroup per l'indice columnstore.

```sql
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## Passaggio 3: Analizzare i risultati

Dopo avere eseguito la query, è possibile iniziare a esaminare i dati e analizzare i risultati. Questa tabella illustra gli elementi da cercare nell'analisi dei rowgroup.


| Colonna | Come usare questi dati |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Se la tabella è partizionata, è prevedibile che vengano visualizzati conteggi più alti nei rowgroup Open. In teoria, a ogni partizione nella distribuzione può essere associato un rowgroup Open. Includere questo fattore nell'analisi. Una tabella di piccole dimensioni partizionata può essere ottimizzata rimuovendo completamente il partizionamento, perché questa operazione contribuisce a migliorare la compressione. |
| [row\_count\_total] | Numero totale di righe per la tabella. Ad esempio, è possibile usare questo valore per calcolare la percentuale di righe nello stato compresso. |
| [row\_count\_per\_distribution\_MAX] | Se tutte le righe sono distribuite uniformemente, questo valore sarà il numero di righe per ogni distribuzione. Confrontare questo valore con compressed\_rowgroup\_count. |
| [COMPRESSED\_rowgroup\_rows] | Numero totale di righe nel formato columnstore per la tabella. |
| [COMPRESSED\_rowgroup\_rows\_AVG] | Se il numero medio di righe è significativamente inferiore al numero massimo di righe per un gruppo di righe, è consigliabile usare CTAS o ALTER INDEX REBUILD per ricomprimere i dati. |
| [COMPRESSED\_rowgroup\_count] | Numero di gruppi di righe nel formato columnstore. Se questo numero è molto elevato in relazione la tabella, è un indicatore che la densità del columnstore è bassa. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | Le righe vengono eliminate in modo logico nel formato columnstore. Se il numero è elevato rispetto alle dimensioni della tabella, provare a ricreare la partizione o a ricompilare l'indice, perché questo le rimuove fisicamente. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | Usarla in combinazione con le colonne AVG e MAX per comprendere l'intervallo di valori per i rowgroup nel columnstore. Un numero basso oltre la soglia di caricamento (102.400 per ogni distribuzione di partizioni allineate) suggerisce che sono disponibili ottimizzazioni nel caricamento dei dati. |
| [COMPRESSED\_rowgroup\_rows\_MAX] | Come sopra. |
| [OPEN\_rowgroup\_count] | I gruppi di righe aperti sono normali. È ragionevole aspettarsi un rowgroup OPEN per ogni distribuzione di tabella (60). Un numero eccessivo suggerisce il caricamento di dati tra le partizioni. Verificare la strategia di partizionamento per assicurarsi che sia valida. |
| [OPEN\_rowgroup\_rows] | Ogni rowgroup può contenere al massimo 1.048.576 righe. Usare questo valore per controllare l'attuale livello di riempimento dei rowgroup Open. |
| [OPEN\_rowgroup\_rows\_MIN] | I gruppi aperti indicano che i dati vengono caricati gradualmente nella tabella o che il carico precedente ha distribuito le righe rimanenti in questo rowgroup. Usare le colonne MIN, MAX, AVG per visualizzare la quantità di dati rimasta nei rowgroup OPEN. Per le tabelle di piccole dimensioni potrebbe essere il 100% di tutti i dati. In tal caso, usare ALTER INDEX REBUILD per forzare i dati nel columnstore. |
| [OPEN\_rowgroup\_rows\_MAX] | Come sopra. |
| [OPEN\_rowgroup\_rows\_AVG] | Come sopra. |
| [CLOSED\_rowgroup\_rows] | Esaminare le righe dei rowgroup Closed come controllo di integrità. Se |
| [CLOSED\_rowgroup\_count] | Il numero di rowgroup Closed deve essere basso o non essere visualizzato affatto. I rowgroup Closed possono essere convertiti in rowgroup Compressed usando il comando ALTER INDEX... REORGANISE. In genere questa operazione non è tuttavia richiesta. I gruppi Closed vengono convertiti automaticamente in rowgroup del columnstore dal processo in background del "motore di tuple". |
| [CLOSED\_rowgroup\_rows\_MIN] | I rowgroup Closed devono avere una velocità di riempimento (fill rate) molto elevata. Se la velocità di riempimento per un rowgroup Closed è bassa, è necessaria un'ulteriore analisi del columnstore. |
| [CLOSED\_rowgroup\_rows\_MAX] | Come sopra. |
| [CLOSED\_rowgroup\_rows\_AVG] | Come sopra. |


## Passaggio 4: Esaminare la causa radice

Esaminare la causa radice consente di verificare se è possibile apportare modifiche alla progettazione, al caricamento o ad altri processi della tabella al fine di migliorare la densità delle righe nei rowgroup e, di conseguenza, la compressione e le prestazioni delle query.

I fattori seguenti possono far sì che un indice columnstore abbia un numero di righe decisamente inferiore a 1.048.576 per ogni rowgroup. E possono anche far sì che le righe vadano nel rowgroup differenziale invece che nel rowgroup compresso.

1. Operazioni DML pesanti
2. Operazioni di caricamento di piccole dimensioni o con un flusso irregolare
3. Troppe partizioni

### Operazioni DML pesanti** 

Le operazioni DML pesanti per l'aggiornamento e l'eliminazione di righe causano l'inefficienza del columnstore. Ciò vale soprattutto quando viene modificata la maggior parte delle righe di un rowgroup.

- L'eliminazione di una riga da un rowgroup compresso si limita a contrassegnare in modo logico la riga come eliminata. La riga rimane nel rowgroup compresso finché la partizione o la tabella non viene ricompilata.
- Quando si inserisce una riga, questa viene aggiunta a una tabella rowstore interna denominata rowgroup differenziale. La riga inserita non viene convertita nel columnstore fino a quando il rowgroup differenziale non è pieno e viene contrassegnato come chiuso. I rowgroup vengono chiusi quando raggiungono la capacità massima di 1.048.576 righe. 
- L'aggiornamento di una riga nel formato columnstore viene elaborato come un'eliminazione logica e poi come operazione di inserimento. La riga inserita viene archiviata nell'archivio differenziale.

Le operazioni di aggiornamento e inserimento in batch che superano la soglia in blocco di 102.400 righe per distribuzione allineata a partizione, vengono scritte direttamente nel formato columnstore. Tuttavia, presupponendo una distribuzione uniforme, perché ciò si verifichi si dovranno modificare più di 6.144.000 righe in una singola operazione. Se il numero di righe per una determinata distribuzione allineata a partizione è minore di 102.400, le righe vengono inviate all'archivio differenziale e lì rimangono fino a quando non vengono inserite o modificate abbastanza righe da chiudere il rowgroup o fino a che l'indice non viene ricompilato.

### Operazioni di caricamento di piccole dimensioni o con un flusso irregolare

I caricamenti di piccole dimensioni in SQL Data Warehouse sono anche definiti flussi irregolari. In genere rappresentano un flusso quasi costante di dati inseriti nel sistema. Quando questo flusso è quasi continuo, tuttavia, il volume di righe non è particolarmente elevato. Molto spesso i dati sono notevolmente inferiori alla soglia necessaria per un caricamento diretto nel formato columnstore.

In queste situazioni è spesso preferibile indirizzare i dati nell'archivio BLOB di Azure e lasciare che se ne accumuli la quantità necessaria prima di caricarli. Questa tecnica viene spesso definita come *micro invio in batch*.

### Troppe partizioni

Le partizioni possono essere troppe. Nell'architettura basata sull'elaborazione parallela massiva (MPP, Massively Parallel Processing) una tabella definita dall'utente viene distribuita e implementata in background come 60 tabelle. Ogni indice columnstore, quindi, viene implementato come 60 indici columnstore. Analogamente, ogni partizione viene implementata in questi 60 indici columnstore. Si tratta di una **differenza notevole** rispetto a SQL Server che presenta un'architettura Symmetric Multiprocessing (SMP).

Se si caricano 1.048.576 righe in una partizione di una tabella di SQL Server SMP, questa verrà compressa nel columnstore. Se invece si caricano 1.048.576 righe in una partizione di una tabella di SQL Data Warehouse, in ognuna delle 60 distribuzioni verranno caricate solo 17.467 righe, presupponendo una distribuzione uniforme. Poiché 17.467 è inferiore alla soglia di 102.400 righe per distribuzione, SQL Data Warehouse archivierà i dati nei rowgroup deltastore. Pertanto, per caricare le righe direttamente nel formato columnstore compresso, è necessario caricare più di 6.144.000 righe (60 x 102.400) in una singola partizione di una tabella di SQL Data Warehouse.

## Passaggio 5: Allocare risorse di calcolo aggiuntive

Per ricompilare gli indici, specialmente con tabelle di grandi dimensioni, spesso sono necessarie risorse aggiuntive. SQL Data Warehouse include una funzionalità di gestione del carico di lavoro che può allocare altra memoria a un utente. Per informazioni su come riservare una quantità di memoria maggiore per la ricompilazione degli indici, vedere la sezione relativa alla gestione del carico di lavoro nell'articolo sulla [concorrenza][].

## Passaggio 6: Ricompilare l'indice per migliorare la media di righe per rowgroup

Per unire rowgroup compressi esistenti e forzare rowgroup differenziali nel columnstore, è necessario ricompilare l'indice. In genere, la quantità di dati presenti è eccessiva per poter ricompilare l'intero indice, è consigliabile quindi ricompilare solo una o più partizioni. In SQL Data Warehouse è possibile ricompilare l'indice in uno di questi modi:

1. Usare [ALTER INDEX][] per ricompilare una partizione.
2. Usare [CTAS][] per ricreare una partizione in una nuova tabella e quindi usare il cambio di partizione per riportare la partizione nella tabella originale.

Qual è il modo migliore? Per grandi volumi di dati, [CTAS][] è in genere più veloce di [ALTER INDEX][]. Per i volumi di dati più piccoli, [ALTER INDEX][] è più facile da usare.

### Metodo n. 1: Usare ALTER INDEX per ricompilare piccoli volumi di dati offline

Questi esempi illustrano come ricompilare l'intero indice columnstore e una singola partizione. Nelle tabelle di grandi dimensioni, è consigliabile ricompilare solo una singola partizione. Si tratta di un'operazione offline.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

Per altre informazioni, vedere la sezione ALTER INDEX REBUILD nella pagina relativa alla [deframmentazione degli indici columnstore](https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1) e l'argomento sintassi [ALTER INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188388.aspx).

### Metodo n. 2: Usare CTAS per eseguire ricompilazione e cambio di partizione in volumi elevati di dati online

In questo esempio vengono usati [CTAS][] e cambio di partizione per ricompilare una partizione di tabella.


```sql
-- Step 01. Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Per altri dettagli sulla ricompilazione di partizioni tramite `CTAS`, vedere le pagina relative alle [partizioni delle tabelle][] e alla [concorrenza][].


## Passaggi successivi

Per altri consigli dettagliati sulla gestione degli indici, vedere l'articolo sulla [gestione degli indici][].

Per altri suggerimenti relativi alla gestione, vedere la panoramica della [gestione][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[partizioni delle tabelle]: sql-data-warehouse-develop-table-partitions.md
[concorrenza]: sql-data-warehouse-develop-concurrency.md
[gestione]: sql-data-warehouse-manage-monitor.md
[gestione degli indici]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->