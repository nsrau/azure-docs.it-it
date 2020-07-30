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
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386606"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Eseguire query sui tipi annidati in file parquet e JSON usando SQL su richiesta (anteprima) in Azure sinapsi Analytics

In questo articolo si apprenderà come scrivere una query usando SQL su richiesta (anteprima) in Azure Synapse Analytics. Questa query leggerà tipi nidificati Parquet.
I tipi annidati sono strutture complesse che rappresentano oggetti o matrici. I tipi annidati possono essere archiviati in: 
- [Parquet](query-parquet-files.md) in cui è possibile disporre di più colonne complesse contenenti matrici e oggetti.
- [File JSON](query-json-files.md) gerarchici in cui è possibile leggere documenti JSON complessi come colonna singola.
- Raccolta CosmosDB in cui ogni documento può contenere proprietà nidificate complesse (attualmente sotto la versione di anteprima pubblica gestita).

Sinapsi SQL su richiesta formatta tutti i tipi annidati come oggetti e matrici JSON, in modo da poter [estrarre o modificare oggetti complessi usando funzioni JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) o [analizzare i dati JSON usando la funzione OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Di seguito è riportato un esempio di query che estrae i valori scalari e oggetti da [COVID-19 Open Research DataSet](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON file con oggetti annidati. 

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

`JSON_VALUE`la funzione restituisce un valore scalare dal campo in corrispondenza del percorso specificato. `JSON_QUERY`la funzione restituisce un oggetto formattato come JSON dal campo nel percorso specificato.

> [!IMPORTANT]
> Questo esempio usa un file del [set di dati di ricerca aperto COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Vedere la licenza THS e la struttura dei dati in questa pagina.

## <a name="prerequisites"></a>Prerequisiti

Il primo passaggio consiste nel **creare un database** con un'origine dati di riferimento. Inizializzare quindi gli oggetti eseguendo uno [script di configurazione](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) su tale database. Questo script di configurazione creerà le origini dati, le credenziali con ambito database e i formati di file esterni usati in questi esempi.

## <a name="project-nested-or-repeated-data"></a>Proiettare dati annidati o ripetuti

Il file PARQUET può includere più colonne con tipi complessi. I valori di queste colonne vengono formattati come testo JSON e restituiti come colonna VARCHAR. Nella query seguente viene letto il file *structExample. parquet* e viene illustrato come leggere i valori delle colonne nidificate: 

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

Questa query restituirà il risultato seguente, in cui il contenuto di ogni oggetto annidato viene restituito come testo JSON:

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0,5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

La query seguente legge il file *justSimpleArray.parquet* e proietta tutte le colonne del file parquet, inclusi i dati nidificati o ripetuti.

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

`JSON_VALUE`la funzione consente di restituire valori da una colonna formattata come testo JSON:

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

Diversamente dai file JSON che nella maggior parte dei casi restituiscono una singola colonna contenente un oggetto JSON complesso. I file PARQUET possono avere più complessi. È possibile leggere le proprietà della colonna nidificata utilizzando la `JSON_VALUE` funzione in ogni colonna. `OPENROWSET`consente di specificare direttamente i percorsi della clausola proprietà annidate in `WITH` . I percorsi possono essere impostati come nome della colonna oppure è possibile aggiungere un' [espressione di percorso JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) dopo il tipo di colonna.

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
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
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

Il risultato è illustrato nella tabella seguente:

|SimpleArray    | FirstElement  | Secondoelement | Terzoelement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Accesso a oggetti secondari da colonne complesse

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

La struttura `MapOfPersons` viene restituita come `VARCHAR` colonna e formattata come stringa JSON.

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
