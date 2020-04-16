---
title: Query Parquet tipi nidificati utilizzando SQL su richiesta (anteprima)Query Parquet nested types using SQL on-demand (preview)
description: In questo articolo verrà illustrato come eseguire query sui tipi nidificati di parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431657"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Query Parquet nested types using SQL on-demand (preview) in Azure Synapse Analytics

In this article, you'll learn how to write a query using SQL on-demand (preview) in Azure Synapse Analytics.  Questa query leggerà i tipi nidificati Parquet.

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere il resto di questo articolo, leggere i seguenti articoli:

- [Configurazione al primo tempo](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Proiettare dati nidificati o ripetuti

La query seguente legge il file *justSimpleArray.parquet.* Proietta tutte le colonne del file Parquet inclusi i dati nidificati o ripetuti.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Accedere agli elementi da colonne annidateAccess elements from nested columns

La query seguente legge il file *structExample.parquet* e mostra come esporre gli elementi di una colonna nidificata:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Accedere agli elementi da colonne ripetute

La query seguente legge il file *justSimpleArray.parquet* e [utilizza JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare un elemento **scalare** dall'interno di una colonna ripetuta, ad esempio una matrice o una mappa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

La query seguente legge il file *mapExample.parquet* e [utilizza JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare un elemento **non scalare** dall'interno di una colonna ripetuta, ad esempio una matrice o una mappa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Passaggi successivi

Il prossimo articolo ti mostrerà come eseguire query sui [file JSON.](query-json-files.md)
