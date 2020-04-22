---
title: Creare e usare tabelle esterne in SQL su richiesta (anteprima)
description: Questa sezione illustra come creare e usare tabelle esterne in SQL su richiesta (anteprima). Le tabelle esterne sono utili se si vuole controllare l'accesso ai dati esterni in SQL su richiesta e se si vogliono usare strumenti, come Power BI, in combinazione con SQL su richiesta.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420795"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Creare e usare tabelle esterne in SQL su richiesta (anteprima) con Azure Synapse Analytics

Questa sezione illustra come creare e usare tabelle esterne in SQL su richiesta (anteprima). Le tabelle esterne sono utili se si vuole controllare l'accesso ai dati esterni in SQL su richiesta e se si vogliono usare strumenti, come Power BI, in combinazione con SQL su richiesta.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel leggere gli articoli seguenti e assicurarsi di aver soddisfatto i prerequisiti per la creazione e l'uso di tabelle esterne in SQL su richiesta:

- [Prima configurazione](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Creare una tabella esterna

È possibile creare tabelle esterne nello stesso modo in cui si creano le normali tabelle esterne di SQL Server. La query seguente crea una tabella esterna che legge il file *population.csv*.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato. Se non è stato creato un database, leggere [Prima configurazione](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Usare una tabella esterna

È possibile usare tabelle esterne nelle query nello stesso modo in cui si usano nelle query di SQL Server.

La query seguente illustra l'uso della tabella esterna *population* creata nella sezione [Creare una tabella esterna](#create-an-external-table). Restituisce i nomi dei paesi con la popolazione del 2019 in ordine decrescente.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato. Se non è stato creato un database, leggere [Prima configurazione](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come archiviare i risultati di una query nell'archiviazione, vedere [Archiviare i risultati della query nell'archiviazione](../sql/create-external-table-as-select.md).
