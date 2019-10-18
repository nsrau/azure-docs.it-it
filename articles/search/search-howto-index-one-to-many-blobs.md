---
title: Indicizzare un BLOB in molti documenti dell'indice di ricerca dall'indicizzatore BLOB di Azure per la ricerca full-text
description: Eseguire l'indicizzazione dei BLOB di Azure per il contenuto di testo usando l'indicizzatore di BLOB di ricerca Ogni BLOB può restituire uno o più documenti dell'indice di ricerca di Azure.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: 585d1e64ae124dce8cb0d4165ecbf0f503560405
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533692"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indicizzazione di BLOB per produrre più documenti di ricerca
Per impostazione predefinita, un indicizzatore BLOB considererà il contenuto di un BLOB come singolo documento di ricerca. Alcuni valori **parsingMode** supportano scenari in cui un singolo BLOB può produrre più documenti di ricerca. I diversi tipi di **parsingMode** che consentono a un indicizzatore di estrarre più di un documento di ricerca da un BLOB sono:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chiave documento uno-a-molti
Ogni documento visualizzato in un indice di ricerca di Azure viene identificato in modo univoco da una chiave del documento. 

Quando non viene specificata alcuna modalità di analisi e non esiste alcun mapping esplicito per il campo chiave nell'indice, ricerca di Azure [esegue](search-indexer-field-mappings.md) automaticamente il mapping della proprietà `metadata_storage_path` come chiave. Questo mapping garantisce che ogni BLOB venga visualizzato come documento di ricerca distinto.

Quando si usa una delle modalità di analisi sopra elencate, un BLOB viene mappato a "molti" documenti di ricerca, rendendo una chiave del documento esclusivamente basata sui metadati del BLOB non idonei. Per ovviare a questo vincolo, ricerca di Azure è in grado di generare una chiave di documento "uno-a-molti" per ogni singola entità estratta da un BLOB. Questa proprietà è denominata `AzureSearch_DocumentKey` e viene aggiunta a ogni singola entità estratta dal BLOB. Il valore di questa proprietà è sicuramente univoco per ogni singola entità _nei BLOB_ e le entità verranno visualizzate come documenti di ricerca distinti.

Per impostazione predefinita, quando non viene specificato alcun mapping esplicito dei campi per il campo indice chiave, viene eseguito il mapping del `AzureSearch_DocumentKey` a tale campo, usando la funzione di mapping dei campi `base64Encode`.

## <a name="example"></a>Esempio
Si supponga di avere una definizione di indice con i campi seguenti:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E il contenitore BLOB contiene BLOB con la struttura seguente:

_Blob1. JSON_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. JSON_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando si crea un indicizzatore e si imposta **parsingMode** su `jsonLines`, senza specificare alcun mapping esplicito dei campi per il campo chiave, il mapping seguente verrà applicato in modo implicito
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Questa installazione determinerà l'indice di ricerca di Azure contenente le informazioni seguenti (ID con codifica Base64 abbreviato per brevità)

| id | temperatura | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapping dei campi personalizzati per il campo chiave di indice

Supponendo la stessa definizione di indice dell'esempio precedente, si supponga che il contenitore BLOB disponga di BLOB con la struttura seguente:

_Blob1. JSON_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. JSON_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando si crea un indicizzatore con `delimitedText` **parsingMode**, potrebbe sembrare naturale impostare una funzione di mapping dei campi nel campo chiave come indicato di seguito:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Tuttavia, questo mapping _non_ comporterà la visualizzazione di 4 documenti nell'indice, perché il campo `recordid` non è univoco _tra i BLOB_. È quindi consigliabile usare il mapping di campi implicito applicato dalla proprietà `AzureSearch_DocumentKey` al campo indice chiave per le modalità di analisi "uno-a-molti".

Se si vuole impostare un mapping di campi esplicito, assicurarsi che _campoOrigine_ sia distinto per ogni singola entità **in tutti i BLOB**.

> [!NOTE]
> L'approccio usato da `AzureSearch_DocumentKey` di garantire l'univocità per entità estratta è soggetto a modifiche e pertanto non è necessario basarsi sul valore per le esigenze dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se non si ha già familiarità con la struttura di base e il flusso di lavoro di indicizzazione BLOB, è necessario prima esaminare l' [indicizzazione dell'archiviazione BLOB di Azure con ricerca di Azure](search-howto-index-json-blobs.md) . Per altre informazioni sulle modalità di analisi per i diversi tipi di contenuto BLOB, vedere gli articoli seguenti.

> [!div class="nextstepaction"]
> [Indicizzazione di BLOB CSV](search-howto-index-csv-blobs.md) 
>  l'[indicizzazione di BLOB JSON](search-howto-index-json-blobs.md)