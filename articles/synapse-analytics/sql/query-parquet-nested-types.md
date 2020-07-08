---
title: Eseguire query su tipi nidificati Parquet con SQL su richiesta (anteprima)
description: In questo successivo si apprenderà come eseguire query su tipi nidificati Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: bf2dbf501b5cd3b6cd0ab6b0e9bbbc2208c98a58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478451"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Eseguire query su tipi nidificati Parquet con SQL su richiesta (anteprima) in Azure Synapse Analytics

In questo articolo si apprenderà come scrivere una query usando SQL su richiesta (anteprima) in Azure Synapse Analytics.  Questa query leggerà tipi nidificati Parquet.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel **creare un database** con un'origine dati di riferimento. Inizializzare quindi gli oggetti eseguendo uno [script di configurazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Questo script di configurazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati in questi esempi.

## <a name="project-nested-or-repeated-data"></a>Proiettare dati annidati o ripetuti

La query seguente legge il file *justSimpleArray.parquet* e proietta tutte le colonne del file parquet, inclusi i dati nidificati o ripetuti.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Accesso agli elementi di colonne annidate

Nella query seguente viene letto il file *structExample. parquet* e viene illustrato come visualizzare gli elementi di una colonna nidificata. Sono disponibili due modi per fare riferimento a un valore annidato:
- Specifica dell'espressione del percorso del valore annidato dopo la specifica del tipo.
- Formattazione del nome della colonna come percorso annidato utilizzando do "." per fare riferimento ai campi.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original nested columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateValue] DATE '$.DateStruct.Date',
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Accesso agli elementi di colonne ripetute

La query seguente legge il file *justSimpleArray.parquet* e usa [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare un elemento **scalare** dall'interno di una colonna ripetuta, ad esempio una matrice o una mappa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

La query seguente legge il file *mapExample.parquet* e usa [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare un elemento **non scalare** dall'interno di una colonna ripetuta, ad esempio una matrice o una mappa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

È inoltre possibile fare riferimento in modo esplicito alle colonne che si desidera restituire nella `WITH` clausola:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

La struttura `MakOfPersons` viene restituita come `VARCHAR` colonna e formattata come stringa JSON.

## <a name="projecting-values-from-repeated-columns"></a>Proiezione di valori da colonne ripetute

Se si dispone di una matrice di valori scalari (ad esempio `[1,2,3]` ) in alcune colonne, è possibile espanderli facilmente e aggiungerli alla riga principale usando lo script seguente:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Passaggi successivi

Il prossimo articolo illustra come [eseguire query su file JSON](query-json-files.md).
