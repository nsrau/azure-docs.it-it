---
title: Archiviare i risultati delle query nell'archiviazione
description: Questo articolo illustra come archiviare i risultati delle query nell'archiviazione tramite SQL su richiesta (anteprima).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: dd7666bb9f22214fb4701e6be9edc171912d9bf9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691855"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Archiviare i risultati delle query nell'archiviazione usando SQL su richiesta (anteprima) con Azure Synapse Analytics

Questo articolo illustra come archiviare i risultati delle query nell'archiviazione tramite SQL su richiesta (anteprima).

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel leggere gli articoli seguenti e assicurarsi di aver soddisfatto i prerequisiti:

- [Prima configurazione](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>CREATE EXTERNAL TABLE AS SELECT

È possibile usare l'istruzione CREATE EXTERNAL TABLE AS SELECT (CETAS) per archiviare i risultati della query nell'archiviazione.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato. Se non è stato creato un database, leggere [Prima configurazione](query-data-storage.md#first-time-setup). È necessario modificare il parametro LOCATION per l'origine dati esterna MyDataSource in modo che punti alla posizione per cui si dispone dell'autorizzazione di scrittura. 

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Usare una tabella esterna creata

È possibile usare una tabella esterna creata tramite CETAS come una normale tabella esterna.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato. Se non è stato creato un database, leggere [Prima configurazione](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come eseguire una query su tipi di file diversi, vedere gli articoli [Eseguire query su un singolo file CSV](query-single-csv-file.md), [Eseguire query su file Per](query-parquet-files.md) e [Eseguire query su file JSON](query-json-files.md).
