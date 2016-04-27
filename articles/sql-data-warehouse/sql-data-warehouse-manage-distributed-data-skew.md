<properties
   pageTitle="Gestire la differenza dati per le tabelle distribuite in Azure SQL Data Warehouse | Microsoft Azure"
   description="Individuare e risolvere la differenza dati quando le righe sono distribuite in modo non uniforme in tutte le distribuzioni di una tabella con distribuzione hash in Azure SQL Data Warehouse." 
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

# Gestire la differenza dati per le tabelle distribuite in Azure SQL Data Warehouse
In questa esercitazione viene identificata una differenza dati in tabelle con distribuzione hash e sono indicati suggerimenti per la risoluzione del problema.

Quando i dati di una tabella sono distribuiti mediante il metodo di distribuzione hash, alcune distribuzioni presenteranno molti più dati di altre. Un'eccessiva differenza dati può compromettere le prestazioni delle query, in quanto il risultato finale di una query distribuita non sarà pronto fino a quando non terminerà la distribuzione con il massimo tempo di esecuzione. A seconda del grado di differenza dati potrebbe essere necessario risolvere il problema.

In questa esercitazione si apprenderà come:

- Usare metadati per determinare quali tabelle presentano una differenza dati
- Capire quando risolvere una differenza dati
- Ricreare la tabella per risolvere la differenza dati.


## Passaggio 1: Creare una visualizzazione che individua la differenza dati

Creare questa visualizzazione per identificare quali tabelle presentano una differenza dati.

```sql
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
SELECT 
	SUBSTRING(@@version,34,4)															AS  [build_number]
,	GETDATE()																			AS  [execution_time]
,	DB_NAME()																			AS  [database_name]
,	s.name																				AS  [schema_name]
,	t.name																				AS  [table_name]
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)												AS  [two_part_name]
,	nt.[name]																			AS  [node_table_name]
,	ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))					AS  [node_table_name_seq]
,	tp.[distribution_policy_desc]														AS  [distribution_policy_name]
,	nt.[distribution_id]																AS  [distribution_id]
,	nt.[pdw_node_id]																	AS  [pdw_node_id]
,	pn.[type]																			AS	[pdw_node_type]
,	pn.[name]																			AS	[pdw_node_name]
,	nps.[partition_number]																AS	[partition_nmbr]
,	nps.[reserved_page_count]															AS	[reserved_space_page_count]
,	nps.[reserved_page_count] - nps.[used_page_count]									AS	[unused_space_page_count]
,	nps.[in_row_data_page_count] 
	+ nps.[row_overflow_used_page_count] + nps.[lob_used_page_count]					AS  [data_space_page_count]
,	nps.[reserved_page_count] 
	- (nps.[reserved_page_count] - nps.[used_page_count]) 
	- ([in_row_data_page_count]+[row_overflow_used_page_count]+[lob_used_page_count])	AS  [index_space_page_count]
,	nps.[row_count]																		AS  [row_count]
from sys.schemas s
join sys.tables t								ON	s.[schema_id]			= t.[schema_id]
join sys.pdw_table_distribution_properties	tp	ON	t.[object_id]			= tp.[object_id]
join sys.pdw_table_mappings tm					ON	t.[object_id]			= tm.[object_id]
join sys.pdw_nodes_tables nt					ON	tm.[physical_name]		= nt.[name]
join sys.dm_pdw_nodes pn 						ON  nt.[pdw_node_id]		= pn.[pdw_node_id]
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.[object_id]			= nps.[object_id]
												AND nt.[pdw_node_id]		= nps.[pdw_node_id]
												AND nt.[distribution_id]	= nps.[distribution_id]
)
, size
AS
(
SELECT	[build_number]
,		[execution_time]
,		[database_name]
,		[schema_name]
,		[table_name]
,		[two_part_name]
,		[node_table_name]
,		[node_table_name_seq]
,		[distribution_policy_name]
,		[distribution_id]
,		[pdw_node_id]
,		[pdw_node_type]
,		[pdw_node_name]
,		[partition_nmbr]
,		[reserved_space_page_count]
,		[unused_space_page_count]
,		[data_space_page_count]
,		[index_space_page_count]
,		[row_count]
,		([reserved_space_page_count] * 8.0)				AS [reserved_space_KB]
,		([reserved_space_page_count] * 8.0)/1000		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1000000		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1000000000	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1000		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1000000		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1000000000	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1000		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1000000		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1000000000	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1000		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1000000		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1000000000	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

## Passaggio 2: Eseguire una query sulla visualizzazione

Dopo aver creato la visualizzazione, eseguire questa query di esempio per identificare quali tabelle presentano una differenza dati.

```sql
SELECT	[two_part_name]
,		[distribution_id]
,		[row_count]
,		[reserved_space_GB]
,		[unused_space_GB]
,		[data_space_GB]
,		[index_space_GB]
FROM	[dbo].[vDistributionSkew]
WHERE	[table_name] = 'FactInternetSales'
ORDER BY [row_count] DESC
```

>[AZURE.NOTE] Le tabelle distribuite con il metodo ROUND\_ROBIN non devono essere asimmetriche. Da progettazione, i dati vengono distribuiti in modo uniforme tra i nodi.

## Passaggio 3: Decidere se risolvere la differenza dati

Per decidere se sia necessario risolvere la differenza dati di una tabella, è necessario conoscere nel modo più completo possibile i volumi di dati e le query del carico di lavoro.

La distribuzione è essenzialmente l'individuazione del giusto equilibrio fra la minimizzazione della differenza dati e la minimizzazione dello spostamento dei dati. Questi obiettivi possono essere contrastanti e talvolta può essere necessario tollerare una determinata differenza dati per poter ridurre lo spostamento dei dati. Ad esempio, quando la colonna di distribuzione corrisponde con un'elevata frequenza alla colonna condivisa di join e aggregazioni, si ridurrà al minimo lo spostamento dei dati. Il vantaggio di uno spostamento dei dati minimo potrebbe essere quello di compensare l'impatto negativo della differenza dati.

## Passaggio 4: Risolvere la differenza dati

Per risolvere la differenza dati è possibile procedere in due modi. Scegliere uno di questi modi se si decide di risolvere la differenza.

### Metodo 1: Ricreare la tabella con una colonna di distribuzione diversa

Il modo più comune per risolvere la differenza dati è ricreare la tabella con una colonna di distribuzione diversa. Per istruzioni su come selezionare una colonna di distribuzione hash, vedere [Distribuzione hash e relativo effetto sulle prestazioni delle query in SQL Data Warehouse][]. In questo esempio si usa [CTAS][] per ricreare una tabella con una colonna di distribuzione diversa.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### Metodo 2: Ricreare la tabella con distribuzione ROUND-ROBIN

In questo esempio si ricrea la tabella con distribuzione ROUND-ROBIN anziché HASH. Questa modifica genererà una distribuzione dei dati uniforme ma di solito incrementa lo spostamento dei dati per le query.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## Passaggi successivi
Per altre informazioni sulla distribuzione delle tabelle, vedere gli articoli seguenti:

* [Progettazione della tabella][]
* [Distribuzione hash][]

<!--Image references-->

<!--Article references-->
[Progettazione della tabella]: sql-data-warehouse-develop-table-design.md
[Distribuzione hash]: sql-data-warehouse-develop-hash-distribution-key.md
[Distribuzione hash e relativo effetto sulle prestazioni delle query in SQL Data Warehouse]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->