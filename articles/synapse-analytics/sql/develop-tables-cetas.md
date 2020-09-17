---
title: CETAS in Synapse SQL
description: Uso di CETAS con Synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 18f472da30b34fcacd70bba9ea7371b56f1a7abf
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032912"
---
# <a name="cetas-with-synapse-sql"></a>CETAS con Synapse SQL

Nel pool SQL o in SQL su richiesta (anteprima) è possibile usare CREATE EXTERNAL TABLE AS SELECT (CETAS) per completare le attività seguenti:  

- Creare una tabella esterna
- Esportare, in parallelo, i risultati di un'istruzione Transact-SQL SELECT in

  - Hadoop
  - BLOB di Archiviazione di Azure
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>CETAS nel pool SQL

Per informazioni sul pool SQL e sull'utilizzo e la sintassi di CETAS, vedere l'articolo [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Inoltre, per informazioni su CTAS con il pool SQL, vedere l'articolo [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="cetas-in-sql-on-demand"></a>CETAS in SQL su richiesta

Se si usa la risorsa SQL su richiesta, CETAS viene usato per creare una tabella esterna ed esportare i risultati delle query in BLOB del servizio di archiviazione di Azure o in Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Sintassi

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argomenti

*[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

Nome della tabella da creare, composto da una, due o tre parti. Per una tabella esterna, SQL su richiesta archivia solo i relativi metadati. Nessun dato effettivo viene archiviato o spostato in SQL su richiesta.

LOCATION = *'path_to_folder'*

Specifica la posizione in cui scrivere i risultati dell'istruzione SELECT nell'origine dati esterna. La cartella radice è il percorso dei dati specificato nell'origine dati esterna. LOCATION deve puntare a una cartella e avere un carattere finale /. Esempio: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Specifica il nome dell'oggetto origine dati esterna che contiene la posizione in cui verranno archiviati i dati esterni. Per creare un'origine dati esterna, usare [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Specifica il nome dell'oggetto formato di file esterno che contiene il formato per il file di dati esterno. Per creare un formato di file esterno, usare [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Attualmente sono supportati solo i formati di file esterni con FORMAT = 'PARQUET'.

WITH *<common_table_expression>*

Indica un set di risultati denominato temporaneo, noto come espressione di tabella comune (CTE). Per altre informazioni, vedere [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

SELECT <select_criteria>

Popola la nuova tabella con i risultati di un'istruzione SELECT. *select_criteria* è il corpo dell'istruzione SELECT che determina i dati da copiare nella nuova tabella. Per informazioni sulle istruzioni SELECT, vedere [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> La clausola ORDER BY nella parte SELECT di CETAS non è supportata.

## <a name="permissions"></a>Autorizzazioni

Per il corretto funzionamento di CETAS, è necessario avere le autorizzazioni per elencare il contenuto della cartella e scrivere nella cartella in LOCATION.

## <a name="examples"></a>Esempi

Questi esempi usano CETAS per salvare i dati aggregati sulla popolazione totale per anno e stato in una cartella aggregated_data che si trova nell'origine dati population_ds.

Questo esempio si basa su credenziali, origine dati e formato di file esterno creati in precedenza. Vedere il documento [Tabelle esterne](develop-tables-external-tables.md). Per salvare i risultati delle query in una cartella diversa nella stessa origine dati, cambiare l'argomento LOCATION. 

Per salvare i risultati in un account di archiviazione diverso, creare e usare un'origine dati diversa per l'argomento DATA_SOURCE.

> [!NOTE]
> Gli esempi seguenti usano un account di archiviazione pubblico del set di dati aperti di Azure. È di sola lettura. Per eseguire queste query, è necessario specificare l'origine dati per chi si hanno autorizzazioni di scrittura.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

L'esempio seguente usa una tabella esterna come origine per CETAS. Si basa su credenziali, origine dati e formato di file esterno e tabella esterna creati in precedenza. Vedere il documento [Tabelle esterne](develop-tables-external-tables.md).

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Tipi di dati supportati

È possibile usare CETAS per archiviare i set di risultati con i tipi di dati SQL seguenti:

- BINARY
- varbinary
- char
- varchar
- Data
- time
- datetime2
- decimal
- NUMERIC
- float
- real
- bigint
- INT
- SMALLINT
- TINYINT
- bit

> [!NOTE]
> I LOB non possono essere usati con CETAS.

I tipi di dati seguenti non possono essere usati nella parte SELECT di CETAS:

- NCHAR
- NVARCHAR
- Datetime
- smalldatetime
- datetimeoffset
- money
- SMALLMONEY
- UNIQUEIDENTIFIER

## <a name="next-steps"></a>Passaggi successivi

È possibile provare a eseguire query su [tabelle esterne Apache Spark per Azure Synapse](develop-storage-files-spark-tables.md).
