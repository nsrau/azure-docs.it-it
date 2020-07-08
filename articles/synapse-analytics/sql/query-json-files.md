---
title: Eseguire query su file JSON con SQL su richiesta (anteprima)
description: Questa sezione illustra come leggere i file JSON con SQL su richiesta in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5d02736e9cb0a612e434dc5a79a73d7a62785728
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207652"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Eseguire query su file JSON con SQL su richiesta (anteprima) in Azure Synapse Analytics

Questo articolo illustra come scrivere una query usando SQL su richiesta (anteprima) in Azure Synapse Analytics. L'obiettivo della query è leggere i file JSON. In [OPENROWSET](develop-openrowset.md) sono elencati i formati supportati.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel **creare un database** in cui verranno eseguite le query. Inizializzare quindi gli oggetti eseguendo uno [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Questo script di installazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati in questi esempi.

## <a name="sample-json-files"></a>File JSON di esempio

La sezione seguente contiene script di esempio per leggere i file JSON. I file sono archiviati nella cartella *books* del contenitore *json* e contengono voci di singoli libri con la struttura seguente:

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

## <a name="read-json-files"></a>Leggere file JSON

Per elaborare un file JSON con JSON_VALUE e [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), è necessario leggere il file JSON dalla risorsa di archiviazione come colonna singola. Lo script seguente legge il file *book1.json* come colonna singola:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'json/books/book1.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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
> L'intero file JSON viene letto come singola riga o colonna. FIELDTERMINATOR, FIELDQUOTE e ROWTERMINATOR sono quindi impostati su 0x0b.

## <a name="query-json-files-using-json_value"></a>Eseguire query sui file JSON usando JSON_VALUE

La query seguente mostra come usare [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare valori scalari (titolo, editore) da un libro intitolato *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

La query seguente mostra come usare [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare oggetti e array (autori) da un libro intitolato *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

## <a name="query-json-files-using-openjson"></a>Eseguire query sui file JSON usando OPENJSON

Nella query seguente viene usato il comando [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Verranno recuperati oggetti e proprietà all'interno di un libro intitolato *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

- [Eseguire query su cartelle e file multipli](query-folders-multiple-csv-files.md)
- [Creare e usare viste](create-use-views.md)
