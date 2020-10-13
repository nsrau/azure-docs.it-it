---
title: BLOB di indici contenenti più documenti
titleSuffix: Azure Cognitive Search
description: Eseguire la ricerca per indicizzazione di BLOB di Azure per contenuti di testo tramite l'indicizzatore BLOB di Azure ricerca cognitiva, in cui ogni BLOB può restituire uno o più documenti dell'indice
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: e5a69525c4bd0717c0561bc61ee3c52aa68e1c9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533962"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indicizzazione di BLOB per produrre più documenti di ricerca
Per impostazione predefinita, un indicizzatore BLOB considererà il contenuto di un BLOB come singolo documento di ricerca. Alcuni valori **parsingMode** supportano scenari in cui un singolo BLOB può produrre più documenti di ricerca. I diversi tipi di **parsingMode** che consentono a un indicizzatore di estrarre più di un documento di ricerca da un BLOB sono:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chiave documento uno-a-molti
Ogni documento visualizzato in un indice di ricerca cognitiva di Azure viene identificato in modo univoco da una chiave del documento. 

Quando non viene specificata alcuna modalità di analisi e non esiste alcun mapping esplicito per il campo chiave nell'indice Azure ricerca cognitiva esegue automaticamente il [mapping](search-indexer-field-mappings.md) della `metadata_storage_path` proprietà come chiave. Questo mapping garantisce che ogni BLOB venga visualizzato come documento di ricerca distinto.

Quando si usa una delle modalità di analisi sopra elencate, un BLOB viene mappato a "molti" documenti di ricerca, rendendo una chiave del documento esclusivamente basata sui metadati del BLOB non idonei. Per ovviare a questo vincolo, Azure ricerca cognitiva è in grado di generare una chiave di documento "uno-a-molti" per ogni singola entità estratta da un BLOB. Questa proprietà è denominata `AzureSearch_DocumentKey` e viene aggiunta a ogni singola entità estratta dal BLOB. Il valore di questa proprietà è sicuramente univoco per ogni singola entità _nei BLOB_ e le entità verranno visualizzate come documenti di ricerca distinti.

Per impostazione predefinita, quando non viene specificato alcun mapping esplicito dei campi per il campo indice chiave, `AzureSearch_DocumentKey` viene eseguito il mapping di a tale campo, usando la `base64Encode` funzione di mapping dei campi.

## <a name="example"></a>Esempio
Si supponga di avere una definizione di indice con i campi seguenti:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E il contenitore BLOB contiene BLOB con la struttura seguente:

_Blob1.js_

```json
    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }
```

_Blob2.js_

```json
    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }
```

Quando si crea un indicizzatore e si imposta **parsingMode** su `jsonLines` -senza specificare alcun mapping esplicito dei campi per il campo chiave, il mapping seguente verrà applicato in modo implicito

```http
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }
```

Questa installazione determinerà l'indice del ricerca cognitiva di Azure contenente le informazioni seguenti (ID con codifica Base64 abbreviato per brevità)

| ID | temperatura | pressure |  timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapping dei campi personalizzati per il campo chiave di indice

Supponendo la stessa definizione di indice dell'esempio precedente, si supponga che il contenitore BLOB disponga di BLOB con la struttura seguente:

_Blob1.js_

```json
    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.js_

```json
    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 
```

Quando si crea un indicizzatore con `delimitedText` **parsingMode**, potrebbe sembrare naturale impostare una funzione di mapping dei campi nel campo chiave come indicato di seguito:

```http
    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }
```

Tuttavia, questo mapping _non_ comporterà la visualizzazione di 4 documenti nell'indice, perché il `recordid` campo non è univoco _tra i BLOB_. È quindi consigliabile usare il mapping di campi implicito applicato dalla `AzureSearch_DocumentKey` proprietà al campo indice chiave per le modalità di analisi "uno-a-molti".

Se si vuole impostare un mapping di campi esplicito, assicurarsi che _campoOrigine_ sia distinto per ogni singola entità **in tutti i BLOB**.

> [!NOTE]
> L'approccio utilizzato da `AzureSearch_DocumentKey` per garantire l'univocità per entità estratta è soggetto a modifiche e pertanto non è necessario basarsi sul valore per le esigenze dell'applicazione.

## <a name="help-us-make-azure-cognitive-search-better"></a>Aiutaci a migliorare Azure ricerca cognitiva
Per richieste di funzionalità o idee su miglioramenti da apportare, fornire i suggerimenti su [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Per informazioni sull'uso della funzionalità esistente, pubblicare la domanda in [stack overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Passaggi successivi

Se non si ha già familiarità con la struttura di base e il flusso di lavoro di indicizzazione BLOB, è consigliabile esaminare prima di tutto l' [indicizzazione dell'archiviazione BLOB di Azure con azure ricerca cognitiva](search-howto-index-json-blobs.md) Per altre informazioni sulle modalità di analisi per i diversi tipi di contenuto BLOB, vedere gli articoli seguenti.

> [!div class="nextstepaction"]
> [Indicizzazione di BLOB CSV](search-howto-index-csv-blobs.md) 
>  [Indicizzazione di BLOB JSON](search-howto-index-json-blobs.md)
