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
ms.openlocfilehash: cbf6d42f3b1d130a6bf89f07bd3a7009ff0e8fa8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647528"
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

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL [SasTokenWrite]
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

## <a name="use-the-external-table"></a>Usare la tabella esterna

È possibile usare la tabella esterna creata tramite CETAS come una normale tabella esterna.

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

Per altre informazioni su come eseguire una query su tipi di file diversi, vedere gli articoli [Eseguire query su un singolo file CSV](query-single-csv-file.md), [Eseguire query su file Per](query-parquet-files.md) e [Eseguire query su file JSON](query-json-files.md).
