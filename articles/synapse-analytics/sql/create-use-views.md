---
title: Creare e usare viste in SQL su richiesta (anteprima)
description: Questa sezione illustra come creare e usare viste per il wrapping di query di SQL su richiesta (anteprima). Le viste consentono di riutilizzare tali query. Sono anche necessarie se si vogliono usare strumenti, come Power BI, in combinazione con SQL su richiesta.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420775"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Creare e usare viste in SQL su richiesta (anteprima) con Azure Synapse Analytics

Questa sezione illustra come creare e usare viste per il wrapping di query di SQL su richiesta (anteprima). Le viste consentono di riutilizzare tali query. Sono anche necessarie se si vogliono usare strumenti, come Power BI, in combinazione con SQL su richiesta.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel leggere gli articoli seguenti e assicurarsi di aver soddisfatto i prerequisiti per la creazione e l'uso di viste di SQL su richiesta:

- [Prima configurazione](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Creare una vista

È possibile creare viste nello stesso modo in cui si creano le normali viste di SQL Server. La query seguente crea una vista che legge il file *population.csv*.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato. Se non è stato creato un database, leggere [Prima configurazione](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

## <a name="use-a-view"></a>Usare una vista

È possibile usare viste nelle query nello stesso modo in cui vengono usate nelle query di SQL Server.

La query seguente illustra l'uso della vista *population* creata nella sezione [Creare una vista](#create-a-view). Restituisce i nomi dei paesi con la popolazione del 2019 in ordine decrescente.

> [!NOTE]
> Cambiare la prima riga della query, ossia [mydbname], in modo da usare il database creato. Se non è stato creato un database, leggere [Prima configurazione](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come eseguire una query su tipi di file diversi, vedere gli articoli [Eseguire query su un singolo file CSV](query-single-csv-file.md), [Eseguire query su file Per](query-parquet-files.md) e [Eseguire query su file JSON](query-json-files.md).
