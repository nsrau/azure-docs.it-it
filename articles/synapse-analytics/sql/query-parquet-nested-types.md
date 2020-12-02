---
title: Eseguire query sui tipi annidati parquet usando un pool SQL senza server
description: In questo articolo si apprenderà come eseguire una query sui tipi annidati parquet usando un pool SQL senza server.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 91f612ba7f19deb739dbb6004e275ea044a5a3d3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462555"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Eseguire query sui tipi annidati nei file parquet e JSON usando un pool SQL senza server in Azure sinapsi Analytics

Questo articolo illustra come scrivere una query usando un pool SQL senza server in Azure sinapsi Analytics. La query eseguirà la lettura dei tipi annidati parquet.
I tipi annidati sono strutture complesse che rappresentano oggetti o matrici. I tipi annidati possono essere archiviati in: 
- [Parquet](query-parquet-files.md), in cui è possibile disporre di più colonne complesse che contengono matrici e oggetti.
- [File JSON](query-json-files.md)gerarchici, in cui è possibile leggere un documento JSON complesso come colonna singola.
- Azure Cosmos DB raccolte (attualmente in anteprima pubblica gestita), in cui ogni documento può contenere proprietà nidificate complesse.

Il pool SQL senza server formatta tutti i tipi annidati come oggetti e matrici JSON. È quindi possibile [estrarre o modificare oggetti complessi usando funzioni JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) o [analizzare i dati JSON usando la funzione OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Di seguito è riportato un esempio di una query che estrae i valori scalari e di oggetti dal file JSON del [set di dati Open Research COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) , che contiene gli oggetti annidati: 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

La `JSON_VALUE` funzione restituisce un valore scalare dal campo nel percorso specificato. La `JSON_QUERY` funzione restituisce un oggetto formattato come JSON dal campo nel percorso specificato.

> [!IMPORTANT]
> Questo esempio usa un file del set di dati di ricerca aperto COVID-19. [Vedere la licenza e la struttura dei dati qui](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel creare un database in cui verrà creata l'origine dati. Sarà quindi possibile inizializzare gli oggetti eseguendo uno [script di installazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nel database. Lo script di installazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni utilizzati negli esempi.

## <a name="project-nested-or-repeated-data"></a>Proiettare dati annidati o ripetuti

Un file parquet può includere più colonne con tipi complessi. I valori di queste colonne vengono formattati come testo JSON e restituiti come colonne VARCHAR. Nella query seguente viene letto il file structExample. parquet e viene illustrato come leggere i valori delle colonne nidificate: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Questa query restituisce il risultato seguente. Il contenuto di ogni oggetto annidato viene restituito come testo JSON.

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0,5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

La query seguente legge il file justSimpleArray.parquet Proietta tutte le colonne dal file parquet, inclusi i dati annidati e ripetuti.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Questa query restituirà il risultato seguente:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Leggere le proprietà da colonne oggetto annidate

La `JSON_VALUE` funzione consente di restituire valori da colonne formattate come testo JSON:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Il risultato è illustrato nella tabella seguente:

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Informazioni supplementari su Eco-epidemiolo... | Julien   | -Figura S1: filogenesi... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Diversamente dai file JSON, che nella maggior parte dei casi restituiscono una singola colonna contenente un oggetto JSON complesso, i file parquet possono avere più colonne complesse. È possibile leggere le proprietà delle colonne annidate utilizzando la `JSON_VALUE` funzione in ogni colonna. `OPENROWSET` consente di specificare direttamente i percorsi delle proprietà annidate in una `WITH` clausola. È possibile impostare i percorsi come nome di una colonna oppure è possibile aggiungere un'espressione di [percorso JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) dopo il tipo di colonna.

Nella query seguente viene letto il file structExample. parquet e viene illustrato come visualizzare gli elementi di una colonna nidificata. Esistono due modi per fare riferimento a un valore annidato:
- Specificando l'espressione del percorso del valore annidato dopo la specifica del tipo.
- Formattando il nome della colonna come tracciato annidato utilizzando "." per fare riferimento ai campi.

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
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Accesso agli elementi di colonne ripetute

La query seguente legge il file justSimpleArray. parquet e USA [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare un elemento scalare dall'interno di una colonna ripetuta, ad esempio una matrice o una mappa:

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

Il risultato è il seguente:

|SimpleArray    | FirstElement  | Secondoelement | Terzoelement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Accesso a oggetti secondari da colonne complesse

La query seguente legge il file mapExample. parquet e USA [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per recuperare un elemento non scalare dall'interno di una colonna ripetuta, ad esempio una matrice o una mappa:

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

È inoltre possibile fare riferimento in modo esplicito alle colonne che si desidera restituire in una `WITH` clausola:

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

La struttura `MapOfPersons` viene restituita come colonna varchar e formattata come stringa JSON.

## <a name="project-values-from-repeated-columns"></a>Valori di progetto da colonne ripetute

Se si dispone di una matrice di valori scalari (ad esempio `[1,2,3]` ) in alcune colonne, è possibile espanderli facilmente e aggiungerli alla riga principale usando questo script:

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
