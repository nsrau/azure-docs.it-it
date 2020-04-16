---
title: Progettare tabelle con Synapse SQLDesign tables using Synapse SQL
description: Introduzione alla progettazione di tabelle in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f1eec76d92edc97f7e4058d3afe813f0bb2aae47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431878"
---
# <a name="design-tables-using-synapse-sql"></a>Progettare tabelle con Synapse SQLDesign tables using Synapse SQL

Questo documento include concetti chiave per la progettazione di tabelle con pool SQL e SQL su richiesta (anteprima).  

[SQL on-demand (anteprima)](on-demand-workspace-overview.md) è un servizio di query sui dati nel data lake. Non dispone di archiviazione locale per l'inserimento di dati. [Il pool SQL](best-practices-sql-pool.md) rappresenta una raccolta di risorse analitiche di cui viene eseguito il provisioning quando si usa Synapse SQL. Le dimensioni del pool SQL sono determinate dalle unità Data Warehouse (DWU).

Nella tabella seguente sono elencati gli argomenti relativi al pool SQL rispetto a SQL su richiesta:

| Argomento                                                        | Pool SQL | SQL su richiesta |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Determinare la categoria della tabella](#determine-table-category)        | Sì                | No                      |
| [Nomi di schemi.](#schema-names)                                | Sì                | Sì                     |
| [Nomi delle tabelle](#table-names)                                  | Sì                | No                      |
| [Persistenza delle tabelle](#table-persistence)                      | Sì                | No                      |
| [Tabella normale](#regular-table)                              | Sì                | No                      |
| [Tabella temporanea](#temporary-table)                          | Sì                | Sì                     |
| [Tabella esterna](#external-table)                            | Sì                | Sì                     |
| [Tipi di dati](#data-types)                                    | Sì                | Sì                     |
| [Tabelle con distribuzione](#distributed-tables)                    | Sì                | No                      |
| [Tabelle con distribuzione hash](#hash-distributed-tables)          | Sì                | No                      |
| [Tabelle replicate](#replicated-tables)                      | Sì                | No                      |
| [Tabelle round robin](#round-robin-tables)                    | Sì                | No                      |
| [Metodi di distribuzione comuni per le tabelle](#common-distribution-methods-for-tables) | Sì                | No                      |
| [Partizioni](#partitions)                                    | Sì                | Sì                     |
| [Indici columnstore](#columnstore-indexes)                  | Sì                | No                      |
| [Statistiche](#statistics)                                    | Sì                | Sì                     |
| [Chiave primaria e chiave univoca](#primary-key-and-unique-key)    | Sì                | No                      |
| [Comandi per la creazione di tabelle](#commands-for-creating-tables) | Sì                | No                      |
| [Allineamento dei dati di origine con il data warehouse](#aligning-source-data-with-the-data-warehouse) | Sì                | No                      |
| [Caratteristiche delle tabelle non supportate](#unsupported-table-features)    | Sì                | No                      |
| [Query di dimensioni della tabella](#table-size-queries)                    | Sì                | No                      |

## <a name="determine-table-category"></a>Determinare la categoria della tabella

Uno [schema star](https://en.wikipedia.org/wiki/Star_schema) organizza i dati in tabelle fact e tabelle delle dimensioni. Alcune tabelle vengono utilizzate per l'integrazione o la gestione temporanea dei dati prima del passaggio a una tabella dei fatti o delle dimensioni. Quando si progetta una tabella, occorre decidere se i dati appartengono a una tabella fact, delle dimensioni o di integrazione. Questa decisione determina la struttura della tabella e la distribuzione appropriate.

- **Le tabelle dei fatti** contengono dati quantitativi che vengono comunemente generati in un sistema transazionale e quindi caricati nel data warehouse. Un'azienda di vendita al dettaglio, ad esempio, genera quotidianamente transazioni di vendita e successivamente carica i dati in una tabella fact di data warehouse per analizzarli.

- Le **tabelle delle dimensioni** contengono i dati degli attributi che possono cambiare, ma che in genere cambiano raramente. Il nome e l'indirizzo di un cliente, ad esempio, vengono archiviati in una tabella delle dimensioni e aggiornati solo quando viene modificato il profilo del cliente. Per ridurre al minimo le dimensioni di una tabella dei fatti di grandi dimensioni, il nome e l'indirizzo del cliente non devono essere in ogni riga di una tabella dei fatti. La tabella fact e la tabella delle dimensioni possono invece condividere un ID cliente. Una query può creare un join tra le due tabelle per associare il profilo e le transazioni di un cliente.

- Le **tabelle di integrazione** sono un luogo in cui integrare o gestire temporaneamente i dati. È possibile creare una tabella di integrazione come una tabella normale, una tabella esterna o una tabella temporanea. È ad esempio possibile caricare i dati in una tabella di staging, eseguire trasformazioni sui dati in gestione temporanea e quindi inserirli in una tabella di produzione.

## <a name="schema-names"></a>Nomi di schemi.

Gli schemi sono un buon modo per raggruppare gli oggetti che vengono utilizzati in modo simile. Il codice seguente crea uno [schema definito dall'utente](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) denominato wwi.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Nomi tabella

Se si esegue la migrazione di più database da una soluzione locali al pool SQL, la procedura consigliata consiste nel migrare tutte le tabelle di realtà, dimensione e integrazione in uno schema di pool SQL. Ad esempio, è possibile archiviare tutte le tabelle nel data warehouse di esempio [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) all'interno di uno schema denominato wwi.

Per mostrare l'organizzazione delle tabelle nel pool SQL, è possibile utilizzare fact, dim e int come prefissi per i nomi delle tabelle. La tabella seguente mostra alcuni dei nomi di tabella e schema per WideWorldImportersDW.  

| Tabella WideWorldImportersDW  | Tipo di tabella. | Pool SQL |
|:-----|:-----|:------|:-----|
| city | Dimension | wwi.DimCity |
| JSON | Fact | wwi.FactOrder |

## <a name="table-persistence"></a>Persistenza delle tabelle

Le tabelle archiviano i dati in modo permanente in Archiviazione di Azure, temporaneamente in Archiviazione di Azure o in un archivio dati esterno al data warehouse.

### <a name="regular-table"></a>Tabella normale

Una tabella normale archivia i dati in Archiviazione di Azure come parte del data warehouse. La tabella e i dati persistono indipendentemente dal fatto che una sessione sia aperta o meno.  L'esempio seguente crea una tabella normale con due colonne.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabella temporanea

Una tabella temporanea esiste solo per la durata della sessione. È possibile utilizzare una tabella temporanea per impedire ad altri utenti di visualizzare risultati temporanei. L'utilizzo di tabelle temporanee riduce inoltre la necessità di pulizia.  Le tabelle temporanee utilizzano l'archiviazione locale e, nel pool SQL, possono offrire prestazioni più veloci.  

SQL su richiesta supporta le tabelle temporanee. Tuttavia, l'utilizzo è limitato poiché è possibile scegliere dalla tabella temporanea, ma non è possibile unirla ai file nell'archivio.

Per altre informazioni, vedere [Tabelle temporanee](develop-tables-temporary.md).

### <a name="external-table"></a>Tabella esterna

[Le tabelle esterne puntano](develop-tables-external-tables.md) ai dati che si trovano nel BLOB di Archiviazione di Azure o in Archiviazione di Azure Data Lake.External tables point to data located in Azure Storage blob or Azure Data Lake Storage.

Importare dati da tabelle esterne nel pool SQL utilizzando l'istruzione [CREATE TABLE AS SELECT.](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Per un'esercitazione sul caricamento, vedere [Usare PolyBase per caricare dati dall'archivio BLOB di Azure a SQL Data Warehouse](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Per SQL on-demand, è possibile usare CETAS per salvare il risultato della query in una tabella esterna in Archiviazione di Azure.For SQL on-demand, you can use [CETAS](develop-tables-cetas.md) to save the query result to an external table in Azure Storage.

## <a name="data-types"></a>Tipi di dati

Il pool SQL supporta i tipi di dati utilizzati più di più di recente. Per un elenco dei tipi di dati supportati, vedere [tipi di dati](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) nel riferimento sull'istruzione CREATE TABLE. Per ulteriori informazioni sull'utilizzo dei tipi di dati, vedere [Tipi di dati](../sql/develop-tables-data-types.md).

## <a name="distributed-tables"></a>Tabelle con distribuzione

Una funzionalità fondamentale del pool SQL è il modo in cui può archiviare e operare sulle tabelle tra [distribuzioni](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions).  Il pool SQL supporta tre metodi per la distribuzione dei dati:SQL pool supports three methods for distributing data:

- Round robin (impostazione predefinita)
- Hash
- Replicata

### <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash

Una tabella distribuita hash distribuisce le righe in base al valore nella colonna di distribuzione. Una tabella distribuita con hash è progettata per ottenere prestazioni elevate per le query su tabelle di grandi dimensioni. Esistono diversi fattori da considerare quando si sceglie una colonna di distribuzione.

Per altre informazioni, vedere [Linee guida di progettazione per tabelle distribuite](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="replicated-tables"></a>Tabelle replicate

Le tabelle replicate mettono a disposizione una copia completa della tabella in ogni nodo di calcolo. Le query vengono eseguite rapidamente nelle tabelle replicate perché i join nelle tabelle replicate non richiedono lo spostamento dei dati. La replica richiede tuttavia spazio di archiviazione aggiuntivo e non è pratica per le tabelle di grandi dimensioni.

Per altre informazioni, vedere [Linee guida di progettazione per l'uso di tabelle replicate in Azure SQL Data Warehouse](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="round-robin-tables"></a>Tabelle round robin

Una tabella round robin distribuisce le righe della tabella in modo uniforme tra tutte le distribuzioni. Le righe vengono distribuite in modo casuale. Il caricamento dei dati in una tabella round robin è veloce.  Tuttavia, le query possono richiedere più spostamento di dati rispetto agli altri metodi di distribuzione.

Per altre informazioni, vedere [Linee guida di progettazione per tabelle distribuite](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="common-distribution-methods-for-tables"></a>Metodi di distribuzione comuni per le tabelle

La categoria della tabella determina spesso l'opzione ottimale per la distribuzione delle tabelle.

| Categoria di tabella | Opzione di distribuzione consigliata |
|:---------------|:--------------------|
| Fact           | Usare la distribuzione hash con indice columnstore cluster. Le prestazioni aumentano quando si crea un join tra due tabelle hash nella stessa colonna di distribuzione. |
| Dimension      | Usare le tabelle replicate per le tabelle di dimensioni più piccole. Se le tabelle sono troppo grandi per essere archiviate in ogni nodo di calcolo, usare le tabelle con distribuzione hash. |
| Staging        | Usare una tabella round robin per la tabella di staging. Il carico con un'istruzione CTAS è veloce. Una volta che i dati sono nella tabella di staging, utilizzare INSERT... SELECT per spostare i dati nelle tabelle di produzione. |

## <a name="partitions"></a>Partizioni

Nel pool SQL una tabella partizionata archivia ed esegue operazioni sulle righe della tabella in base agli intervalli di dati. Una tabella può, ad esempio, essere partizionata in base ai giorni, ai mesi o agli anni. È possibile migliorare le prestazioni delle query tramite l'eliminazione della partizione, che limita l'analisi di una query ai dati all'interno di una partizione.

È inoltre possibile gestire i dati tramite la commutazione tra partizioni. Poiché i dati nel pool SQL sono già distribuiti, troppe partizioni possono rallentare le prestazioni delle query. Per altre informazioni, vedere [Indicazioni sul partizionamento](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> Quando si passa alla partizione di passaggio a partizioni di tabella non vuote, è consigliabile usare l'opzione TRUNCATE_TARGET nell'istruzione [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) se i dati esistenti devono essere troncati.

Il codice seguente consente di trasformare i dati giornalieri trasformati in una partizione SalesFact e di sovrascrivere tutti i dati esistenti.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

In SQL su richiesta è possibile limitare i file/cartelle (partizioni) che verranno letti dalla query. Il partizionamento in base al percorso è supportato utilizzando le funzioni filepath e fileinfo descritte in [Querying storage files](develop-storage-files-overview.md). L'esempio seguente legge una cartella con i dati per l'anno 2017:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Indici columnstore

Per impostazione predefinita, il pool SQL archivia una tabella come indice columnstore cluster. Questo modulo di archiviazione dei dati raggiunge compressione dei dati e prestazioni di query elevate su tabelle di grandi dimensioni.  L'indice columnstore cluster è in genere la scelta migliore, ma in alcuni casi un indice cluster o un heap è la struttura di archiviazione appropriata.  

> [!TIP]
> Una tabella heap può essere particolarmente utile per il caricamento di dati temporanei, ad esempio una tabella di staging, che viene trasformata in una tabella finale.

Per un elenco delle funzionalità columnstore, vedere [Indici columnstore - Novità](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Per migliorare le prestazioni dell'indice columnstore, vedere [Ottimizzazione della qualità di un gruppo di righe per columnstore](../sql/data-load-columnstore-compression.md).

## <a name="statistics"></a>Statistiche


Quando crea il piano per l'esecuzione di una query, Query Optimizer usa le statistiche a livello di colonna. Per migliorare le prestazioni delle query, è importante disporre di statistiche su singole colonne, in particolare sulle colonne utilizzate nei join di query. Synapse SQL supporta la creazione automatica di statistiche. 

L'aggiornamento statistico non avviene automaticamente. Aggiornare le statistiche dopo l'aggiunta o la modifica di un numero significativo di righe. Ad esempio, aggiornare le statistiche dopo un caricamento. Ulteriori informazioni sono disponibili nell'articolo [Indicazioni](develop-tables-statistics.md) statistiche.

## <a name="primary-key-and-unique-key"></a>Chiave primaria e chiave univoca

PRIMARY KEY è supportato solo quando vengono utilizzati non CLUSTERED e NOT ENFORCED.  Il vincolo UNIQUE è supportato solo quando si utilizza NOT ENFORCED.  Per altre informazioni, vedere l'articolo [Vincoli di tabella del pool SQL.](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="commands-for-creating-tables"></a>Comandi per la creazione di tabelle

È possibile creare una tabella come nuova tabella vuota. È inoltre possibile creare e popolare una tabella con i risultati di un'istruzione SELECT. Di seguito sono riportati i comandi T-SQL per la creazione di una tabella.

| Istruzione T-SQL | Descrizione |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Crea una tabella vuota definendo tutte le opzioni e le colonne della tabella. |
| [CREA TABELLA ESTERNA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Crea una tabella esterna. La definizione della tabella viene archiviata nel pool SQL. I dati della tabella vengono archiviati in Archiviazione BLOB di Azure o Archiviazione dati di Azure.The table data is stored in Azure Blob storage or Azure Data Lake Storage. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Popola una nuova tabella con i risultati di un'istruzione SELECT. Le colonne e i tipi di dati della tabella si basano sui risultati dell'istruzione SELECT. Per importare i dati, questa istruzione può selezionare da una tabella esterna. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Crea una nuova tabella esterna esportando i risultati di un'istruzione SELECT in una posizione esterna,  Il percorso è Archiviazione BLOB di Azure o Archiviazione di Azure Data Lake.The location is either Azure Blob storage or Azure Data Lake Storage. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Allineamento dei dati di origine con il data warehouse

Le tabelle del data warehouse vengono popolate caricando i dati da un'altra origine dati. Per ottenere un caricamento corretto, il numero e i tipi di dati delle colonne nei dati di origine devono essere allineati alla definizione della tabella nel data warehouse.

> [!NOTE]
> Il recupero dei dati da allineare potrebbe risultare l'operazione più difficile della progettazione delle tabelle.

Se i dati provengono da più archivi dati, è possibile eseguire il porting dei dati nel data warehouse e archiviarli in una tabella di integrazione. Una volta che i dati si trovano nella tabella di integrazione, è possibile utilizzare la potenza del pool SQL per implementare le operazioni di trasformazione. Dopo aver preparati i dati, è possibile inserirli nelle tabelle di produzione.

## <a name="unsupported-table-features"></a>Funzionalità non supportate delle tabelle

Il pool SQL supporta molte, ma non tutte, le funzionalità delle tabelle offerte da altri database.  Nell'elenco seguente vengono illustrate alcune delle funzionalità della tabella non supportate nel pool SQL.

- Chiave esterna, controllo [Vincoli tabella](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Colonne calcolate](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Viste indicizzate](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequenza](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Colonne di tipo sparse](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- Chiavi surrogate, implementare con [identitàSurrogate](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Keys, implement with Identity
- [Sinonimi](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Trigger](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Indici univoci](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Tipi definiti dall'utente](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Query di dimensioni della tabella

Un modo semplice per identificare lo spazio e le righe utilizzate da una tabella in ognuna delle 60 distribuzioni consiste nell'utilizzare [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Tenere presente che l'utilizzo dei comandi DBCC può essere molto limitante.  Le viste a gestione dinamica (DMV) mostrano maggiori dettagli rispetto ai comandi DBCC. Iniziare creando la vista riportata di seguito.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Riepilogo dello spazio della tabella

Questa query restituisce le righe e lo spazio per singola tabella.  Il riepilogo dello spazio tabelle consente di visualizzare le tabelle che sono le tabelle più grandi. Verrà inoltre visualizzato se sono round robin, replicati o sottoposti a hash.  Per le tabelle con distribuzione hash, la query mostra la colonna di distribuzione.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Spazio della tabella per tipo di distribuzione

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Spazio della tabella per tipo di indice

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Riepilogo dello spazio di distribuzione

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Passaggi successivi

Dopo avere creato le tabelle per il data warehouse, il passaggio successivo consiste nel caricare i dati nella tabella.  Per un'esercitazione sul caricamento, vedere [Caricamento di dati nel pool SQL.](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)
