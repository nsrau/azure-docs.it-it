---
title: Eseguire query sui file JSON con SQL su richiesta (anteprima)
description: Questa sezione illustra come leggere i file JSON usando SQL su richiesta in Azure sinapsi Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4c1fe9ac5d3b2470fb70231a83e57f3e08d0dfb1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197595"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Eseguire query su file JSON con SQL su richiesta (anteprima) in Azure sinapsi Analytics

Questo articolo illustra come scrivere una query usando SQL su richiesta (anteprima) in Azure sinapsi Analytics. L'obiettivo della query è leggere i file JSON. I formati supportati sono elencati in [OPENROWSET](develop-openrowset.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere la parte restante di questo articolo, vedere gli articoli seguenti:

- [Prima configurazione](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>File JSON di esempio

La sezione seguente contiene script di esempio per leggere i file JSON. I file vengono archiviati in un contenitore *JSON* , *libri*di cartelle e contengono una singola voce di libro con la struttura seguente:

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>Leggere i file JSON

Per elaborare i file JSON usando JSON_VALUE e [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), è necessario leggere il file JSON dalla risorsa di archiviazione come colonna singola. Lo script seguente legge il file *Book1. JSON* come una singola colonna:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> Si sta leggendo l'intero file JSON come singola riga o colonna. FIELDTERMINATOR, FIELDQUOTE e ROWTERMINATOR sono quindi impostati su 0x0B.

## <a name="query-json-files-using-json_value"></a>Eseguire query sui file JSON usando JSON_VALUE

La query seguente illustra come usare [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare i valori scalari (titolo, autore) da un libro denominato *metodi probabilistici e statistici in crittografia, un'introduzione dagli articoli selezionati*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>Eseguire query sui file JSON usando JSON_QUERY

Nella query seguente viene illustrato come utilizzare [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare oggetti e matrici (autori) da un libro denominato *metodi probabilistici e statistici in crittografia, un'introduzione da parte degli argomenti selezionati*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>Eseguire query sui file JSON con OPENJSON

Nella query seguente viene usato [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Recupererà gli oggetti e le proprietà all'interno di un libro denominato *metodi probabilistici e statistici in crittografia, un'introduzione dagli articoli selezionati*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Passaggi successivi

Negli articoli successivi di questa serie verrà illustrato come:

- [Esecuzione di query su cartelle e più file](query-folders-multiple-csv-files.md)
- [Creare e usare viste](create-use-views.md)
