<properties
   pageTitle="Panoramica delle tabelle in SQL Data Warehouse | Microsoft Azure"
   description="Introduzione alle tabelle di SQL Data Warehouse di Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>  

# Panoramica delle tabelle in SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuzione][]
- [Index][]
- [Partition][]
- [Statistiche][]
- [Temporanea][]

Iniziare a creare tabelle in SQL Data Warehouse è facile. La sintassi di base [CREATE TABLE][] segue la sintassi comune che probabilmente è già nota all'utente perché usata in altri database. Per creare una tabella, è sufficiente assegnarle un nome, assegnare un nome alle colonne e definire i tipi di dati per ogni colonna. Se sono state create tabelle in altri database, si dovrebbe avere già familiarità con la procedura.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

Nell'esempio sopra riportato viene creata una tabella di nome Customers con due colonne: FirstName e LastName. Ogni colonna è definita con un tipo di dati VARCHAR(25), che limita i dati a 25 caratteri. Questi e altri attributi fondamentali di una tabella sono praticamente identici a quelli di altri database. I tipi di dati vengono definiti per ogni colonna e garantiscono l'integrità dei dati. È possibile aggiungere indici per aumentare le prestazioni riducendo l'I/O. È possibile aggiungere il partizionamento per migliorare le prestazioni quando è necessario modificare i dati.

La [ridenominazione][RENAME] di una tabella di SQL Data Warehouse sarà simile a quanto descritto di seguito:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## Tabelle con distribuzione

Un nuovo attributo fondamentale introdotto da sistemi distribuiti come SQL Data Warehouse è la **colonna di distribuzione**. Il nome stesso è indicativo di cosa sia una colonna di distribuzione. Si tratta della colonna che determina come distribuire, o dividere, i dati in background. Quando si crea una tabella senza specificare la colonna di distribuzione, la tabella viene automaticamente distribuita mediante **round robin**. Sebbene le tabelle round robin possano essere sufficiente in alcuni scenari, definire le colonne di distribuzione può ridurre considerevolmente lo spostamento dei dati durante le query, ottimizzando così le prestazioni. Vedere [Distribuzione di una tabella][Distribute] per ulteriori informazioni su come selezionare una colonna di distribuzione.

## Indicizzazione e partizionamento delle tabelle

Con l'acquisizione di maggiore esperienza nell'uso di SQL Data Warehouse e il desiderio di ottimizzare le prestazioni, l'utente vorrà trovare ulteriori informazioni sulla progettazione della tabella. Per ulteriori informazioni, vedere gli articoli su [tipi di dati della tabella][Data Types], [distribuzione di una tabella][Distribute], [indicizzazione di una tabella][Index] e [partizionamento di una tabella][Partition].

## Statistiche della tabella

Le statistiche sono estremamente importanti per ottenere le migliori prestazioni da SQL Data Warehouse. Dal momento che SQL Data Warehouse non è ancora in grado di creare e aggiornare automaticamente le statistiche per l'utente, come ci si aspetta nel database SQL di Azure, l'articolo sulle [statistiche][] potrebbe essere uno dei più importanti per essere certi di ottenere prestazioni ottimali dalle query.

## Tabelle temporanee

Le tabelle temporanee sono tabelle che esistono solo per la durata dell'accesso e che non possono essere visualizzate da altri utenti. Le tabelle temporanee possono essere un ottimo modo per impedire ad altri utenti di visualizzare i risultati temporanei e per ridurre la necessità di pulizia. Poiché le tabelle temporanee utilizzano anche archiviazione locale, possono offrire prestazioni più veloci per alcune operazioni. Vedere gli articoli sulle [tabelle temporanee][Temporary] per ulteriori dettagli.

## Tabelle esterne

Le tabelle esterne, note anche come tabelle Polybase, sono tabelle in cui è possibile eseguire query da SQL Data Warehouse, ma che puntano a dati esterni da SQL Data Warehouse. Ad esempio, è possibile creare una tabella esterna che punta ai file nell'archiviazione BLOB di Azure. Per ulteriori dettagli su come creare ed eseguire query in una tabella esterna, vedere [Caricare dati con Polybase][].

## Funzionalità non supportate delle tabelle

Mentre SQL Data Warehouse contiene molte delle stesse funzionalità delle tabelle offerte da altri database, esistono alcune funzionalità che non sono ancora supportate. Di seguito è riportato un elenco di alcune funzionalità non ancora supportate.

| Funzionalità non supportate |
| --- |
|[Proprietà Identity][] vedere [Soluzione alternativa per l'assegnazione di chiavi sostitutive][])|
|[Vincoli di tabella][] Primary key, Foreign key, Unique e Check|
|[Indici univoci][]|
|[Colonne calcolate][]|
|[Colonne di tipo sparse][]|
|[Tipi definiti dall'utente][]|
|[Sequenza][]|
|[Trigger][]|
|[Viste indicizzate][]|
|[Sinonimi][]|

## Query di dimensioni della tabella

Un modo semplice per identificare lo spazio e le righe utilizzati da una tabella in ognuna delle 60 distribuzioni consiste nell'usare [DBCC PDW\_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Tuttavia, l'utilizzo dei comandi DBCC può essere abbastanza restrittivo. Le viste a gestione dinamica (DMV) consentono di visualizzare molti più dettagli, nonché di fornire un controllo di gran lunga superiore sui risultati della query. Per iniziare, creare questa vista, a cui si farà riferimento in molti esempi di questo e di altri articoli.

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

### Riepilogo dello spazio della tabella

Questa query restituisce le righe e lo spazio per singola tabella. È una query molto utile per verificare quali sono le tabelle più grandi e se sono con distribuzione hash o round robin. Per le tabelle con distribuzione hash viene mostrata anche la colonna di distribuzione. Nella maggior parte dei casi le tabelle più grandi devono essere con distribuzione hash e avere un indice columnstore cluster.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,	  distribution_column
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
,	  distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### Spazio della tabella per tipo di distribuzione

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

### Spazio della tabella per tipo di indice

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

### Riepilogo dello spazio di distribuzione

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

## Passaggi successivi

Per altre informazioni, vedere gli articoli su [tipi di dati delle tabelle][Data Types], [distribuzione di una tabella][Distribute], [indicizzazione di una tabella][Index], [partizionamento di una tabella][Partition], [conservazione delle statistiche delle tabelle][Statistics] e [tabelle temporanee][Temporary]. Per altre informazioni sulle procedure consigliate, vedere [Procedure consigliate per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribuzione]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporanea]: ./sql-data-warehouse-tables-temporary.md
[Procedure consigliate per SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Caricare dati con Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW\_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Proprietà Identity]: https://msdn.microsoft.com/library/ms186775.aspx
[Soluzione alternativa per l'assegnazione di chiavi sostitutive]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[Vincoli di tabella]: https://msdn.microsoft.com/library/ms188066.aspx
[Colonne calcolate]: https://msdn.microsoft.com/library/ms186241.aspx
[Colonne di tipo sparse]: https://msdn.microsoft.com/library/cc280604.aspx
[Tipi definiti dall'utente]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequenza]: https://msdn.microsoft.com/library/ff878091.aspx
[Trigger]: https://msdn.microsoft.com/library/ms189799.aspx
[Viste indicizzate]: https://msdn.microsoft.com/library/ms191432.aspx
[Sinonimi]: https://msdn.microsoft.com/library/ms177544.aspx
[Indici univoci]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->  

<!---HONumber=AcomDC_0810_2016-->