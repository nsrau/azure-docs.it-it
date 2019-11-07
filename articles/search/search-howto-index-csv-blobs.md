---
title: Indicizzare i BLOB CSV con la modalità di analisi delimitedText (anteprima)
titleSuffix: Azure Cognitive Search
description: Estrarre e Importare CSV dall'archiviazione BLOB di Azure usando la modalità di analisi delimitedText, attualmente disponibile in anteprima pubblica.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4edeb8d535504c305319aad35637bb1b09f65984
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719239"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Come indicizzare i BLOB CSV usando la modalità di analisi delimitedText e gli indicizzatori BLOB in Azure ricerca cognitiva 

> [!IMPORTANT] 
> La modalità di analisi delimitedText è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Attualmente non è disponibile alcun portale o supporto per .NET SDK.

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

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indicizzatore:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
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

