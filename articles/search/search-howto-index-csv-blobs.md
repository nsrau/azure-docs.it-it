---
title: Ricerca nei BLOB CSV
titleSuffix: Azure Cognitive Search
description: Estrarre e importare CSV dall'archiviazione BLOB di Azure usando la modalità di analisi del testo delimitato.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122322"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Come indicizzare i BLOB CSV usando la modalità di analisi del testo delimitato e gli indicizzatori BLOB in Ricerca cognitiva di AzureHow to index CSV blobs using delimitedText parsing mode and Blob indexers in Azure Cognitive Search

Per impostazione predefinita, [l'indicizzatore BLOB di Ricerca cognitiva di Azure](search-howto-indexing-azure-blob-storage.md) analizza i BLOB di testo delimitati come un singolo blocco di testo. Nel caso dei BLOB che contengono dati, tuttavia, è spesso consigliabile gestire ogni riga del BLOB come documento separato. Ad esempio, in base al testo delimitato seguente, si potrebbe decidere di analizzarlo in due documenti, ciascuno contenente campi "id", "datePublished" e "tag": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

In questo articolo si apprenderà come analizzare i BLOB CSV con `delimitedText` un indicizzatore BLOB di Ricerca cognitiva di Azure impostando la modalità di analisi. 

> [!NOTE]
> Seguire i consigli di configurazione dell'indicizzatore nell'indicizzazione uno-a-molti per restituire più documenti di ricerca da un BLOB di Azure.Follow the indexer configuration recommendations in [One-to-many indexing](search-howto-index-one-to-many-blobs.md) to output multiple search documents from one Azure blob.

## <a name="setting-up-csv-indexing"></a>Configurazione dell'indicizzazione di CSV
Per indicizzare i BLOB CSV, creare `delimitedText` o aggiornare una definizione dell'indicizzatore con la modalità di analisi in una richiesta di creazione indicizzatore:To index CSV blobs, create or update an indexer definition with the parsing mode on a [Create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) request:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` indica che la prima riga (non vuota) di ogni BLOB contiene un'intestazione.
Se i BLOB non contengono una riga di intestazione iniziale, è necessario specificare le intestazioni nella configurazione dell'indicizzatore: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

È possibile personalizzare il carattere di delimitazione usando l'impostazione di configurazione `delimitedTextDelimiter`. Ad esempio:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> È attualmente supportata solo la codifica UTF-8. Se è necessario il supporto per altre codifiche, votare su [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando si usa la modalità di analisi del testo delimitata, Ricerca cognitiva di Azure presuppone che tutti i BLOB nell'origine dati siano CSV. Se è necessario supportare una combinazione di BLOB di tipo CSV e non CSV nella stessa origine dati, votare su [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Esempi di richiesta
Per concludere, ecco gli esempi completi del payload. 

Origine dati: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indicizzatore:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Aiutaci a migliorare la ricerca cognitiva di Azure
Per richieste di funzionalità o idee su miglioramenti da apportare, fornire i suggerimenti su [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

