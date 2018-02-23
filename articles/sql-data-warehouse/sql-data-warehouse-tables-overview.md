---
title: Introduzione alla progettazione di tabelle - Azure SQL Data Warehouse | Microsoft Docs
description: Introduzione alla progettazione di tabelle in Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 01/18/2018
ms.author: barbkess
ms.openlocfilehash: 5c163880a7508d69bce0019cc5379bca8c704d59
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="introduction-to-designing-tables-in-azure-sql-data-warehouse"></a>Introduzione alla progettazione di tabelle in Azure SQL Data Warehouse

Concetti chiave della progettazione di tabelle in Azure SQL Data Warehouse. 

## <a name="determining-table-category"></a>Determinazione della categoria della tabella 

Uno [schema star](https://en.wikipedia.org/wiki/Star_schema) organizza i dati in tabelle fact e tabelle delle dimensioni. Alcune tabelle vengono usate per i dati di integrazione o di staging prima di spostarli in una tabella fact o delle dimensioni. Quando si progetta una tabella, occorre decidere se i dati appartengono a una tabella fact, delle dimensioni o di integrazione. Questa decisione determina la struttura della tabella e la distribuzione appropriate. 

- Le **tabelle fact** contengono i dati quantitativi che vengono comunemente generati in un sistema transazionale e successivamente caricati nel data warehouse. Un'azienda di vendita al dettaglio, ad esempio, genera quotidianamente transazioni di vendita e successivamente carica i dati in una tabella fact di data warehouse per analizzarli.

- Le **tabelle delle dimensioni** contengono i dati degli attributi che possono cambiare, ma che in genere cambiano raramente. Il nome e l'indirizzo di un cliente, ad esempio, vengono archiviati in una tabella delle dimensioni e aggiornati solo quando viene modificato il profilo del cliente. Per ridurre al minimo le dimensioni di una tabella fact di grandi dimensioni, si può evitare di inserire il nome e l'indirizzo del cliente in ogni riga della tabella. La tabella fact e la tabella delle dimensioni possono invece condividere un ID cliente. Una query può creare un join tra le due tabelle per associare il profilo e le transazioni di un cliente. 

- Le **tabelle di integrazione** sono un luogo in cui integrare o gestire temporaneamente i dati. È possibile creare una tabella di integrazione come una tabella normale, una tabella esterna o una tabella temporanea. È ad esempio possibile caricare i dati in una tabella di staging, eseguire trasformazioni sui dati in gestione temporanea e quindi inserirli in una tabella di produzione.

## <a name="schema-and-table-names"></a>Nomi di tabella e dello schema
In SQL Data Warehouse un data warehouse è un tipo di database. Tutte le tabelle nel data warehouse sono contenute all'interno dello stesso database.  Non è possibile creare un join tra le tabelle che si trovano in più data warehouse. Questo comportamento è diverso da quello di SQL Server, dove i join tra database sono supportati. 

In un database di SQL Server è possibile usare fact, dim o integrate come nomi di schema. Se si esegue la migrazione di un database di SQL Server a SQL Data Warehouse, è consigliabile migrare tutte le tabelle fact, delle dimensioni e di integrazione in un unico schema in SQL Data Warehouse. Si può, ad esempio, archiviare tutte le tabelle nel data warehouse di esempio [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) all'interno di un unico schema denominato wwi. Il codice seguente crea uno [schema definito dall'utente](/sql/t-sql/statements/create-schema-transact-sql) denominato wwi.

```sql
CREATE SCHEMA wwi;
```

Per visualizzare l'organizzazione delle tabelle in SQL Data Warehouse, è possibile usare fact, dim e int come prefissi dei nomi delle tabelle. La tabella seguente include alcuni dei nomi di tabella e dello schema per WideWorldImportersDW. Questa tabella mette a confronto i nomi in SQL Server con i nomi in SQL Data Warehouse. 

| Tabella WideWorldImportersDW  | Tipo di tabella | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|
| city | Dimensione | Dimension.City | wwi.DimCity |
| Ordine | Fact | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Persistenza delle tabelle 

Le tabelle archiviano i dati in modo permanente in Archiviazione di Azure, temporaneamente in Archiviazione di Azure oppure in un archivio dati esterno al data warehouse.

### <a name="regular-table"></a>Tabella normale

Una tabella normale archivia i dati in Archiviazione di Azure come parte del data warehouse. La tabella e i dati sono persistenti indipendentemente dalla presenza o meno di una sessione aperta.  Questo esempio crea una tabella normale con due colonne. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabella temporanea
Una tabella temporanea esiste solo per la durata della sessione. Si può usare questo tipo di tabella per impedire ad altri utenti di visualizzare i risultati temporanei e anche per ridurre la necessità di eseguire una pulizia.  Poiché le tabelle temporanee utilizzano anche archiviazione locale, possono offrire prestazioni più veloci per alcune operazioni.  Per altre informazioni, vedere [Tabelle temporanee](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabella esterna
Una tabella esterna punta ai dati che si trovano nel BLOB del servizio di archiviazione di Azure o in Azure Data Lake Store. Quando viene usata in combinazione con l'istruzione CREATE TABLE AS SELECT, la selezione da una tabella esterna importa i dati in SQL Data Warehouse. Le tabelle esterne sono pertanto utili per il caricamento dei dati. Per un'esercitazione sul caricamento, vedere [Usare PolyBase per caricare dati dall'archivio BLOB di Azure a SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Tipi di dati
SQL Data Warehouse supporta i tipi di dati più diffusi. Per un elenco dei tipi di dati supportati, vedere [tipi di dati](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) nel riferimento sull'istruzione CREATE TABLE. La riduzione al minimo delle dimensioni dei tipi di dati consente di migliorare le prestazioni delle query. Per informazioni sull'uso dei tipi di dati, vedere [Tipi di dati](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Tabelle con distribuzione
Una caratteristica fondamentale di SQL Data Warehouse è il modo è riesce ad archiviare e operare sulle tabelle in 60 [distribuzioni](massively-parallel-processing-mpp-architecture.md#distributions).  Le tabelle vengono distribuite usando un metodo round robin, hash o di replica.

### <a name="hash-distributed-tables"></a>Tabelle con distribuzione hash
La distribuzione hash distribuisce le righe in base al valore nella colonna di distribuzione. La tabella con distribuzione hash è progettata per ottenere prestazioni elevate in presenza di join di query tra tabelle di grandi dimensioni. Molti fattori influiscono sulla scelta della colonna di distribuzione. 

Per altre informazioni, vedere [Linee guida di progettazione per tabelle distribuite](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Tabelle replicate
Le tabelle replicate mettono a disposizione una copia completa della tabella in ogni nodo di calcolo. Le query sulle tabelle replicate sono veloci poiché i join nelle tabelle replicate non richiedono lo spostamento dei dati. La replica esige tuttavia uno spazio di archiviazione aggiuntivo e non risulta pratica per le tabelle di grandi dimensioni. 

Per altre informazioni, vedere [Linee guida di progettazione per l'uso di tabelle replicate in Azure SQL Data Warehouse](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tabelle round robin
Una tabella round robin distribuisce le righe della tabella in modo uniforme tra tutte le distribuzioni. Le righe vengono distribuite in modo casuale. Il caricamento dei dati in una tabella round robin è veloce.  Tuttavia, le query possono richiedere un maggiore spostamento dei dati rispetto agli altri metodi di distribuzione. 

Per altre informazioni, vedere [Linee guida di progettazione per tabelle distribuite](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Metodi di distribuzione comuni per le tabelle
La categoria della tabella spesso determina l'opzione appropriata per la distribuzione della tabella. 

| Categoria di tabella | Opzione di distribuzione consigliata |
|:---------------|:--------------------|
| Fact           | Usare la distribuzione hash con indice columnstore cluster. Le prestazioni aumentano quando si crea un join tra due tabelle hash nella stessa colonna di distribuzione. |
| Dimensione      | Usare le tabelle replicate per le tabelle di dimensioni più piccole. Se le tabelle sono troppo grandi per essere archiviate in ogni nodo di calcolo, usare le tabelle con distribuzione hash. |
| Staging        | Usare una tabella round robin per la tabella di staging. Il carico con un'istruzione CTAS è veloce. Dopo che i dati sono stati inseriti nella tabella di staging, usare INSERT...SELECT per spostarli in una tabella di produzione. |

## <a name="table-partitions"></a>Partizioni della tabella
Una tabella partizionata archivia ed esegue operazioni sulle righe di tabella in base agli intervalli di dati. Una tabella può, ad esempio, essere partizionata in base ai giorni, ai mesi o agli anni. È possibile migliorare le prestazioni delle query tramite l'eliminazione della partizione, che limita l'analisi di una query ai dati all'interno di una partizione. È inoltre possibile gestire i dati tramite la commutazione tra partizioni. Poiché i dati in SQL Data Warehouse sono già distribuiti, un numero eccessivo di partizioni può rallentare le prestazioni delle query. Per altre informazioni, vedere [Indicazioni sul partizionamento](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Indici Columnstore
Per impostazione predefinita, SQL Data Warehouse archivia una tabella come indice columnstore cluster. Questo modulo di archiviazione dei dati raggiunge compressione dei dati e prestazioni di query elevate su tabelle di grandi dimensioni.  L'indice columnstore cluster è in genere la scelta migliore, ma in alcuni casi un indice cluster o un heap è la struttura di archiviazione appropriata.

Per un elenco delle funzionalità columnstore, vedere [Indici columnstore - Novità](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Per migliorare le prestazioni dell'indice columnstore, vedere [Ottimizzazione della qualità di un gruppo di righe per columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistiche
Quando crea il piano per l'esecuzione di una query, Query Optimizer usa le statistiche a livello di colonna. Per migliorare le prestazioni delle query, è importante creare statistiche su singole colonne, in particolare sulle colonne usate nei join delle query. La creazione e l'aggiornamento delle statistiche non vengono eseguiti automaticamente. [Creare le statistiche](/sql/t-sql/statements/create-statistics-transact-sql) dopo la creazione di una tabella. Aggiornare le statistiche dopo l'aggiunta o la modifica di un numero significativo di righe. Aggiornare, ad esempio, le statistiche dopo un carico. Per altre informazioni, vedere [Indicazioni sulle statistiche](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Comandi per la creazione di tabelle
È possibile creare una tabella come nuova tabella vuota. È inoltre possibile creare e popolare una tabella con i risultati di un'istruzione SELECT. Di seguito sono riportati i comandi T-SQL per la creazione di una tabella.

| Istruzione T-SQL | DESCRIZIONE |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Crea una tabella vuota definendo tutte le opzioni e le colonne della tabella. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) | Crea una tabella esterna. La definizione della tabella viene archiviata in SQL Data Warehouse. I dati della tabella vengono archiviati nell'archivio BLOB di Azure o in Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Popola una nuova tabella con i risultati di un'istruzione SELECT. Le colonne e i tipi di dati della tabella si basano sui risultati dell'istruzione SELECT. Per importare i dati, questa istruzione può selezionare da una tabella esterna. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Crea una nuova tabella esterna esportando i risultati di un'istruzione SELECT in una posizione esterna,  vale a dire l'archivio BLOB di Azure o Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Allineamento dei dati di origine con il data warehouse

Le tabelle del data warehouse vengono popolate caricando i dati da un'altra origine dati. Per eseguire un caricamento corretto, il numero e i tipi di dati delle colonne nei dati di origine devono essere allineati con la definizione della tabella nel data warehouse. Il recupero dei dati da allineare potrebbe risultare l'operazione più difficile della progettazione delle tabelle. 

Se i dati provengono da più archivi dati, è possibile migrarli nel data warehouse e archiviarli in una tabella di integrazione. Dopo che i dati sono stati inseriti nella tabella di integrazione, è possibile sfruttare la potenza di SQL Data Warehouse per eseguire operazioni di trasformazione. Dopo aver preparati i dati, è possibile inserirli nelle tabelle di produzione.

## <a name="unsupported-table-features"></a>Funzionalità non supportate delle tabelle
SQL Data Warehouse supporta molte delle funzionalità per tabelle offerte da altri database, ma non tutte.  Di seguito sono elencate alcune delle funzionalità per tabelle che non sono supportate in SQL Data Warehouse.

- Chiave primaria, chiavi esterne, univoco, controllo [Vincoli di tabella](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Colonne calcolate](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Viste indicizzate](/sql/relational-databases/views/create-indexed-views)
- [Sequenza](/sql/t-sql/statements/create-sequence-transact-sql)
- [Colonne di tipo sparse](/sql/relational-databases/tables/use-sparse-columns)
- [Chiavi surrogate](). Implementare con [Identity](sql-data-warehouse-tables-identity.md).
- [Sinonimi](/sql/t-sql/statements/create-synonym-transact-sql)
- [Trigger](/sql/t-sql/statements/create-trigger-transact-sql)
- [Indici univoci](/sql/t-sql/statements/create-index-transact-sql)
- [Tipi definiti dall'utente](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Query di dimensioni della tabella
Un modo semplice per identificare lo spazio e le righe usati da una tabella in ciascuna delle 60 distribuzioni consiste nell'usare [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Tuttavia, l'utilizzo dei comandi DBCC può essere abbastanza restrittivo.  Le viste a gestione dinamica (DMV) mostrano maggiori dettagli rispetto ai comandi DBCC. Iniziare creando questa vista.

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

Questa query restituisce le righe e lo spazio per singola tabella.  Consente di vedere quali sono le tabelle più grandi e se si tratta di tabelle con distribuzione hash, replicate o round robin.  Per le tabelle con distribuzione hash, la query mostra la colonna di distribuzione.  

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
Dopo avere creato le tabelle per il data warehouse, il passaggio successivo consiste nel caricare i dati nella tabella.  Per un'esercitazione sul caricamento, vedere [Usare PolyBase per caricare dati dall'archivio BLOB di Azure a SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).