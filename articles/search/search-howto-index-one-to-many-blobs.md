---
title: Indicizzare BLOB contenenti più documentiIndex blobs containing multiple documents
titleSuffix: Azure Cognitive Search
description: Eseguire la ricerca per indicizzazione dei BLOB di Azure per il contenuto di testo usando l'indicizzatore BLOB di ricerca concasoscafo di Azure, in cui ogni BLOB potrebbe restituire uno o più documenti di indice di ricerca.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112273"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indicizzazione di BLOB per produrre più documenti di ricercaIndexing blobs to produce multiple search documents
Per impostazione predefinita, un indicizzatore BLOB considererà il contenuto di un BLOB come un singolo documento di ricerca. Alcuni valori **parsingMode** supportano scenari in cui un singolo BLOB può generare più documenti di ricerca. I diversi tipi di parsingMode che consentono a un indicizzatore di estrarre più di un documento di ricerca da un BLOB sono:The different types of **parsingMode** that allow an indexer to extract more than one search document from a blob are:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chiave documento uno-a-molti
Ogni documento visualizzato in un indice di Ricerca cognitiva di Azure è identificato in modo univoco da una chiave del documento. 

Quando non viene specificata alcuna modalità di analisi e se non è presente alcun `metadata_storage_path` mapping esplicito per il campo chiave nell'indice Ricerca cognitiva di Azure esegue automaticamente il [mapping](search-indexer-field-mappings.md) della proprietà come chiave. Questo mapping garantisce che ogni BLOB venga visualizzato come documento di ricerca distinto.

Quando si usa una delle modalità di analisi elencate in precedenza, un BLOB esegue il mapping ai documenti di ricerca "molti", rendendo non adatta una chiave di documento basata esclusivamente sui metadati BLOB. Per superare questo vincolo, Ricerca cognitiva di Azure è in grado di generare una chiave documento "uno-a-molti" per ogni singola entità estratta da un BLOB. Questa proprietà `AzureSearch_DocumentKey` è denominata e viene aggiunta a ogni singola entità estratta dal BLOB. Il valore di questa proprietà è garantito per essere univoco per ogni singola entità _tra BLOB_ e le entità verranno visualizzate come documenti di ricerca separati.

Per impostazione predefinita, quando non viene specificato alcun `AzureSearch_DocumentKey` mapping esplicito dei campi `base64Encode` per il campo dell'indice di chiave, l'oggetto viene mappato ad esso, utilizzando la funzione di mapping dei campi.

## <a name="example"></a>Esempio
Si supponga di essere una definizione di indice con i campi seguenti:Assume you've an index definition with the following fields:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E il contenitore BLOB ha BLOB con la struttura seguente:And your blob container has blobs with the following structure:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando si crea un indicizzatore e `jsonLines` si imposta **parsingMode** su - senza specificare alcun mapping di campo esplicito per il campo chiave, il mapping seguente verrà applicato in modo implicito
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Questa configurazione comporterà l'indice di Ricerca cognitiva di Azure contenente le informazioni seguenti (ID codificato base64 abbreviato per brevità)This setup will result in the Azure Cognitive Search index containing the following information (base64 encoded id shortened for brevity)

| id | temperatura | pressure |  timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00 |
| aHR0 ... YjEuanNvbjsjjjjjjj | 33 | 30 | 2019-02-14T00:00:00 |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00 |
| aHR0 ... YjIuanNvbjsjjjjjj | 120 | 3 | 2013-05-11T00:00:00 |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapping dei campi personalizzati per il campo chiave dell'indice

Supponendo che la stessa definizione di indice dell'esempio precedente, supponiamo che il contenitore BLOB abbia BLOB con la struttura seguente:Suming the same index definition as the previous example, say your blob container has blobs with the following structure:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando si crea un `delimitedText` indicizzatore con **parsingMode**, potrebbe essere naturale impostare una funzione di mapping dei campi nel campo chiave come segue:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Tuttavia, questo mapping _non_ comporterà la visualizzazione di `recordid` 4 documenti nell'indice, poiché il campo non è univoco _tra i BLOB._ Pertanto, è consigliabile utilizzare il mapping di `AzureSearch_DocumentKey` campo implicito applicato dalla proprietà al campo dell'indice di chiave per le modalità di analisi "uno-a-molti".

Se si desidera impostare un mapping esplicito dei campi, assicurarsi che _sourceField_ sia distinto per ogni singola entità **in tutti i BLOB.**

> [!NOTE]
> L'approccio `AzureSearch_DocumentKey` utilizzato per garantire l'univocità per entità estratta è soggetto a modifiche e pertanto non è consigliabile basarsi sul valore per le esigenze dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se non si ha già familiarità con la struttura di base e il flusso di lavoro dell'indicizzazione BLOB, è consigliabile esaminare prima [L'indicizzazione](search-howto-index-json-blobs.md) di Archiviazione BLOB di Azure con Ricerca cognitiva di Azure.If you aren't already familiar with the basic structure and workflow of blob indexing, you should review Indexing Azure Blob Storage with Azure Cognitive Search first. Per altre informazioni sulle modalità di analisi per diversi tipi di contenuto BLOB, vedere gli articoli seguenti.

> [!div class="nextstepaction"]
> [Indicizzazione di BLOB](search-howto-index-csv-blobs.md)
> CSV[Indicizzazione BLOB JSON Indexing JSON blobs](search-howto-index-json-blobs.md)
