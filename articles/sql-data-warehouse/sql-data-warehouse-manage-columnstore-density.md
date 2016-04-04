<properties
   pageTitle="Gestione delle differenze nella distribuzione di tabelle | Microsoft Azure"
   description="Indicazioni per consentire agli utenti di identificare le differenze nella distribuzione di tabelle distribuite"
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
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestione degli indici columnstore
Gli indici columnstore sono la struttura portante di Azure SQL Data Warehouse. Mantenendo gli indici in uno stato ottimale è possibile massimizzare le prestazioni del sistema.

Questo articolo illustra come interrogare i metadati dell'indice columnstore per le tabelle, per diagnosticare più facilmente i problemi e risolverli rapidamente.

## Esecuzione di query sui metadati di columnstore
Per comprendere la densità dell'indice columnstore, è necessaria una query su metadati di sistema. Di seguito è riportato un esempio del tipo di informazioni che è possibile scoprire.

```
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

Dopo aver creato la vista, è possibile analizzare facilmente i metadati del columnstore. Di seguito è riportato un esempio di query.

```
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

## Miglioramento della densità del columnstore
Dopo avere eseguito la query, è possibile iniziare a esaminare i dati e analizzare i risultati.

È opportuno esaminare diversi aspetti:

| Colonna | Come usare questi dati |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Se la tabella è partizionata, è prevedibile che vengano visualizzati conteggi più alti nei rowgroup Open. In teoria, a ogni partizione nella distribuzione può essere associato un rowgroup Open. Includere questo fattore nell'analisi. Una tabella di piccole dimensioni partizionata può essere ottimizzata rimuovendo completamente il partizionamento, perché questa operazione contribuisce a migliorare la compressione. |
| [row\_count\_total] | Numero totale di righe per la tabella. Questo valore può essere usato, ad esempio, per calcolare una percentuale di righe con stato compresso. |
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

## Gestione degli indici
La ricompressione dei rowgroup si può ottenere creando le partizioni con [CTAS][] o ricompilando l'indice stesso con [ALTER INDEX][]. [CTAS][] viene eseguito in genere più velocemente di [ALTER INDEX][], specialmente per tabelle o partizioni di grandi dimensioni. Tuttavia, per le tabelle o le partizioni più piccole [ALTER INDEX][] è spesso molto più pratico.

>[AZURE.NOTE] ALTER INDEX...REBUILD è un'operazione offline. ALTER INDEX...REORGANISE è un'operazione online. REORGANISE non influisce tuttavia sui rowgroup Open. Per includere i rowgroup Open, è necessario ALTER INDEX...REBUILD.

La ricompilazione degli indici, specialmente con tabelle di grandi dimensioni, trae spesso vantaggio dall'uso di risorse aggiuntive. Azure SQL Data Warehouse include una funzionalità di gestione del carico di lavoro che può essere usata per allocare altra memoria a un utente. Per informazioni su come riservare una maggiore quantità di memoria per la ricompilazione dell'indice, vedere la sezione relativa alla gestione del carico di lavoro nell'articolo sulla [concorrenza][].

## Passaggi successivi
Per altre informazioni sulla ricreazione di partizioni con `CTAS`, vedere gli articoli seguenti:

* [Partizionamento delle tabelle][]
* [Concorrenza][]

Per altri consigli dettagliati sulla gestione degli indici, vedere l'articolo sulla [gestione degli indici][].

Per altri suggerimenti relativi alla gestione, vedere la panoramica della[gestione][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Partizionamento delle tabelle]: sql-data-warehouse-develop-table-partitions.md
[concorrenza]: sql-data-warehouse-develop-concurrency.md
[gestione]: sql-data-warehouse-manage-monitor.md
[gestione degli indici]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/it-IT/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->