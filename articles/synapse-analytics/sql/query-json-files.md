---
title: Eseguire query sui file JSON usando LA richiesta SQL (anteprima)Query JSON files using SQL on-demand (preview)
description: Questa sezione illustra come leggere i file JSON usando SQL su richiesta in Azure Synapse Analytics.This section explains how to read JSON files using SQL on-demand in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1054328216d0517b3f450ba4fe08f3bef32d68f7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431722"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Query JSON files using SQL on-demand (preview) in Azure Synapse Analytics

In this article, you'll learn how to write a query using SQL on-demand (preview) in Azure Synapse Analytics. L'obiettivo della query è leggere i file JSON.

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere il resto di questo articolo, leggere i seguenti articoli:

- [Configurazione al primo tempo](query-data-storage.md#first-time-setup)
- [Prerequisiti](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>File JSON di esempio

La sezione seguente contiene script di esempio per leggere i file JSON. I file vengono memorizzati in un contenitore *json,* *nella raccolta di cartelle*e contengono una singola voce di libro con la seguente struttura:

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

## <a name="read-json-files"></a>Leggere i file JSONRead JSON files

Per elaborare i file JSON utilizzando JSON_VALUE e [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), è necessario leggere il file JSON dall'archiviazione come una singola colonna. Lo script seguente legge il file *book1.json* come una singola colonna:

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
> Si sta leggendo l'intero file JSON come singola riga o colonna. Quindi, FIELDTERMINATOR, FIELDQUOTE e ROWTERMINATOR sono impostati su 0x0b.

## <a name="query-json-files-using-json_value"></a>Eseguire query sui file JSON usando JSON_VALUE

La query seguente mostra come utilizzare [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare i valori scalari (titolo, editore) da un libro intitolato *Metodi probabilistici e statistici in Crittografia, Un'introduzione*per articoli selezionati :

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

La query seguente mostra come utilizzare [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare oggetti e matrici (autori) da un libro intitolato *Metodi probabilistici e statistici in crittografia, Introduzione per argomenti selezionati*:

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

## <a name="query-json-files-using-openjson"></a>Query JSON files using OPENJSON

La query seguente utilizza [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Recupererà oggetti e proprietà all'interno di un libro intitolato *Metodi probabilistici e statistici in crittografia, Un'introduzione per articoli selezionati*:

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
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Passaggi successivi

I prossimi articoli di questa serie dimostreranno come:

- [Esecuzione di query su cartelle e più file](query-folders-multiple-csv-files.md)
- [Creare e utilizzare le viste](create-use-views.md).
