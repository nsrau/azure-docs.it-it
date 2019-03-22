---
title: "Indicizzare blob che contiene più di eseguire ricerche nei documenti dell'indice da indicizzatore di Blob di Azure per la ricerca full-text: ricerca di Azure"
description: Ricerca per indicizzazione i BLOB di Azure per il contenuto di testo usando l'indicizzatore Blob ricerca di Azure. Ogni blob può contenere uno o più documenti di indice di ricerca di Azure.
ms.date: 02/12/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: e95eff015340659b642dff800a03f615e22c1577
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57220839"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indicizzazione di blob che produce più documenti di ricerca
Per impostazione predefinita, un indicizzatore blob considererà il contenuto di un blob come un documento di ricerca singola. Determinati **parsingMode** valori supportano scenari in cui un singolo blob può comportare più documenti di ricerca. I diversi tipi di **parsingMode** che consentono un indicizzatore di estrarre più di una ricerca nel documento da un blob è:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

> [!IMPORTANT]
> `jsonLines` modalità di analisi è in anteprima pubblica e non deve essere utilizzata in ambienti di produzione. Per altre informazioni, vedere [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md). 

## <a name="one-to-many-document-key"></a>Chiave del documento uno-a-molti
Ogni documento che viene visualizzato in un indice di ricerca di Azure viene identificata da una chiave del documento. 

Quando viene specificata alcuna modalità di analisi, e se è presente alcun esplicita mapping per il campo chiave nell'indice di ricerca di Azure automaticamente [viene eseguito il mapping](search-indexer-field-mappings.md) il `metadata_storage_path` proprietà come chiave. Questo mapping garantisce che ogni blob viene visualizzato come un documento di ricerca distinct.

Quando si usa la modalità di analisi elencate in precedenza, un blob viene eseguito il mapping ai documenti di ricerca "molti", effettuare una chiave del documento in base esclusivamente i metadati del blob non idoneo. Per ovviare a questo vincolo, ricerca di Azure è in grado di generare una chiave del documento "uno-a-molti" per ogni singola entità estratti da un blob. Questa proprietà è denominata `AzureSearch_DocumentKey` e viene aggiunto a ogni singola entità estratti dal blob. Il valore di questa proprietà è sicuramente univoco per ogni singola entità _tra BLOB_ e le entità verranno visualizzati come documenti di ricerca separate.

Per impostazione predefinita, quando nessun mapping di campo esplicito per il campo chiave dell'indice viene specificato, il `AzureSearch_DocumentKey` viene eseguito il mapping, utilizzando il `base64Encode` funzione di mapping campi.

## <a name="example"></a>Esempio
Si supponga di che avere una definizione di indice con i campi seguenti:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Il contenitore blob con i BLOB con la struttura seguente:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando si crea un indicizzatore e impostare il **parsingMode** a `jsonLines` - senza specificare qualsiasi campo esplicito i mapping per il campo chiave, il mapping seguente verranno applicati in modo implicito
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Questa configurazione comporterà l'indice di ricerca di Azure contenente le informazioni seguenti (con codificata base64 id abbreviato per comodità)

| id | temperatura | pressure |  timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapping dei campi personalizzati per il campo chiave di indice

Supponendo che la stessa definizione di indice dell'esempio precedente, ad esempio che il contenitore blob con i BLOB con la struttura seguente:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando si crea un indicizzatore con `delimitedText` **parsingMode**, è possibile che si desideri naturale per configurare una funzione di mapping campi per il campo chiave come indicato di seguito:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Tuttavia, tale mapping verrà _non_ comportare 4 documenti visualizzati in corrispondenza dell'indice, perché le `recordid` campo non è univoco _tra BLOB_. Di conseguenza, è consigliabile rendere utilizzare il mapping dei campi implicita applicato dal `AzureSearch_DocumentKey` proprietà per il campo di indice di chiave per la modalità di analisi "uno-a-molti".

Se si desidera configurare un mapping dei campi espliciti, assicurarsi che il _sourceField_ distinta per ogni singola entità **tra tutti i BLOB**.

> [!NOTE]
> L'approccio utilizzato da `AzureSearch_DocumentKey` garantire l'univocità per ogni entità estratti è soggette a modifiche e pertanto è consigliabile non fare affidamento su presenta un valore per le esigenze dell'applicazione.

## <a name="see-also"></a>Vedere anche 

+ [Indicizzatori in Ricerca di Azure](search-indexer-overview.md)
+ [Indicizzazione di Archiviazione BLOB di Azure con Ricerca di Azure](search-howto-index-json-blobs.md)
+ [Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-csv-blobs.md)
+ [Indicizzazione di BLOB JSON con l'indicizzatore blob ricerca di Azure](search-howto-index-csv-blobs.md)

## <a name="NextSteps"></a>Passaggi successivi
* Per altre informazioni su Ricerca di Azure, vedere la [pagina del servizio ricerca](https://azure.microsoft.com/services/search/).