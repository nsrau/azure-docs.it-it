---
title: Eseguire ricerche su BLOB CSV
titleSuffix: Azure Cognitive Search
description: Estrarre e Importare CSV dall'archiviazione BLOB di Azure usando la modalità di analisi delimitedText.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122322"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Come indicizzare i BLOB CSV usando la modalità di analisi delimitedText e gli indicizzatori BLOB in Azure ricerca cognitiva

Per impostazione predefinita, l' [indicizzatore BLOB di Azure ricerca cognitiva](search-howto-indexing-azure-blob-storage.md) analizza i BLOB di testo delimitati come singolo blocco di testo. Nel caso dei BLOB che contengono dati, tuttavia, è spesso consigliabile gestire ogni riga del BLOB come documento separato. Ad esempio, in base al testo delimitato seguente, si potrebbe decidere di analizzarlo in due documenti, ciascuno contenente campi "id", "datePublished" e "tag": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

In questo articolo si apprenderà come analizzare i BLOB CSV con un indicizzatore BLOB di Azure ricerca cognitiva impostando la modalità di analisi `delimitedText`. 

> [!NOTE]
> Seguire i consigli di configurazione dell'indicizzatore nell' [indicizzazione uno-a-molti](search-howto-index-one-to-many-blobs.md) per restituire più documenti di ricerca da un BLOB di Azure.

## <a name="setting-up-csv-indexing"></a>Configurazione dell'indicizzazione di CSV
Per indicizzare i BLOB CSV, creare o aggiornare una definizione dell'indicizzatore con la modalità di analisi `delimitedText` in una richiesta [create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

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
> Quando si usa la modalità di analisi del testo delimitata, Azure ricerca cognitiva presuppone che tutti i BLOB nell'origine dati siano CSV. Se è necessario supportare una combinazione di BLOB di tipo CSV e non CSV nella stessa origine dati, votare su [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
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

## <a name="help-us-make-azure-cognitive-search-better"></a>Aiutaci a migliorare Azure ricerca cognitiva
Per richieste di funzionalità o idee su miglioramenti da apportare, fornire i suggerimenti su [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

