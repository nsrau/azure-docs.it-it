---
title: BLOB di indici contenenti più documenti dell'indice di ricerca dall'indicizzatore BLOB di Azure per la ricerca full-text-ricerca di Azure
description: Eseguire l'indicizzazione dei BLOB di Azure per il contenuto di testo usando l'indicizzatore di BLOB di ricerca Ogni BLOB può contenere uno o più documenti dell'indice di ricerca di Azure.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 2c2a17d006f65854a89b9fac1818fcec420c07dc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182325"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indicizzazione di BLOB che producono più documenti di ricerca
Per impostazione predefinita, un indicizzatore BLOB considererà il contenuto di un BLOB come singolo documento di ricerca. Alcuni valori **parsingMode** supportano scenari in cui un singolo BLOB può produrre più documenti di ricerca. I diversi tipi di **parsingMode** che consentono a un indicizzatore di estrarre più di un documento di ricerca da un BLOB sono:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chiave documento uno-a-molti
Ogni documento visualizzato in un indice di ricerca di Azure viene identificato in modo univoco da una chiave del documento. 

Quando non viene specificata alcuna modalità di analisi e non esiste alcun mapping esplicito per il campo chiave nell'indice, ricerca di Azure [esegue](search-indexer-field-mappings.md) automaticamente `metadata_storage_path` il mapping della proprietà come chiave. Questo mapping garantisce che ogni BLOB venga visualizzato come documento di ricerca distinto.

Quando si usa una delle modalità di analisi sopra elencate, un BLOB viene mappato a "molti" documenti di ricerca, rendendo una chiave del documento esclusivamente basata sui metadati del BLOB non idonei. Per ovviare a questo vincolo, ricerca di Azure è in grado di generare una chiave di documento "uno-a-molti" per ogni singola entità estratta da un BLOB. Questa proprietà è denominata `AzureSearch_DocumentKey` e viene aggiunta a ogni singola entità estratta dal BLOB. Il valore di questa proprietà è sicuramente univoco per ogni singola entità _nei BLOB_ e le entità verranno visualizzate come documenti di ricerca distinti.

Per impostazione predefinita, quando non viene specificato alcun mapping esplicito dei campi per il campo indice `AzureSearch_DocumentKey` chiave, viene eseguito il mapping di `base64Encode` a tale campo, usando la funzione di mapping dei campi.

## <a name="example"></a>Esempio
Si supponga di avere una definizione di indice con i campi seguenti:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E il contenitore BLOB contiene BLOB con la struttura seguente:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando si crea un indicizzatore e si imposta **parsingMode** su `jsonLines` -senza specificare alcun mapping esplicito dei campi per il campo chiave, il mapping seguente verrà applicato in modo implicito
    
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

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando si crea un indicizzatore con `delimitedText` **parsingMode**, potrebbe sembrare naturale impostare una funzione di mapping dei campi nel campo chiave come indicato di seguito:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Tuttavia, questo mapping _non_ comporterà la visualizzazione di 4 documenti nell'indice, perché il `recordid` campo non è univoco _tra i BLOB_. È quindi consigliabile usare il mapping di campi implicito applicato dalla `AzureSearch_DocumentKey` proprietà al campo indice chiave per le modalità di analisi "uno-a-molti".

Se si vuole impostare un mapping di campi esplicito, assicurarsi che _campoOrigine_ sia distinto per ogni singola entità **in tutti i BLOB**.

> [!NOTE]
> L'approccio utilizzato da `AzureSearch_DocumentKey` per garantire l'univocità per entità estratta è soggetto a modifiche e pertanto non è necessario basarsi sul valore per le esigenze dell'applicazione.

## <a name="see-also"></a>Vedere anche

+ [Indicizzatori in Ricerca di Azure](search-indexer-overview.md)
+ [Indicizzazione di Archiviazione BLOB di Azure con Ricerca di Azure](search-howto-index-json-blobs.md)
+ [Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-csv-blobs.md)
+ [Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di ricerca di Azure](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>Passaggi successivi
* Per altre informazioni su Ricerca di Azure, vedere la [pagina del servizio ricerca](https://azure.microsoft.com/services/search/).