---
title: Indicizzare BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure - Ricerca di Azure
description: Eseguire la ricerca per indicizzazione nell'archiviazione BLOB di Azure per la ricerca full-text usando un indice di Ricerca di Azure. Gli indicizzatori automatizzano l'inserimento di dati per le origini dati selezionate, ad esempio l'archiviazione BLOB di Azure.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 193ed7099293fb1ee4c056abcc5c2f34d78627b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024693"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure
Per impostazione predefinita, l' [indicizzatore di BLOB di Ricerca di Azure](search-howto-indexing-azure-blob-storage.md) analizza i BLOB di testo delimitato come blocco singolo di testo. Nel caso dei BLOB che contengono dati, tuttavia, è spesso consigliabile gestire ogni riga del BLOB come documento separato. Ad esempio, in base al testo delimitato seguente, si potrebbe decidere di analizzarlo in due documenti, ciascuno contenente campi "id", "datePublished" e "tag": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

In questo articolo si apprenderà come analizzare i BLOB CSV con un'impostazione indexerby blob di ricerca di Azure il `delimitedText` modalità di analisi. 

Il `delimitedText` modalità di analisi è attualmente in anteprima pubblica e non è consigliata per i carichi di lavoro di produzione.

> [!NOTE]
> Seguire le indicazioni di configurazione dell'indicizzatore nelle [indicizzazione di uno-a-molti](search-howto-index-one-to-many-blobs.md) per visualizzare più documenti di ricerca da un blob di Azure.

## <a name="setting-up-csv-indexing"></a>Configurazione dell'indicizzazione di CSV
Per indicizzare BLOB CSV, creare o aggiornare la definizione di un indicizzatore con la modalità di analisi `delimitedText` :  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` indica che la prima riga (non vuota) di ogni BLOB contiene un'intestazione.
Se i BLOB non contengono una riga di intestazione iniziale, è necessario specificare le intestazioni nella configurazione dell'indicizzatore: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

È possibile personalizzare il carattere di delimitazione usando l'impostazione di configurazione `delimitedTextDelimiter`. Ad esempio: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> È attualmente supportata solo la codifica UTF-8. Se è necessario il supporto per altre codifiche, votare su [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando si usa la modalità di analisi di testo delimitato, Ricerca di Azure presuppone che tutti i BLOB nell'origine dati siano di tipo CSV. Se è necessario supportare una combinazione di BLOB di tipo CSV e non CSV nella stessa origine dati, votare su [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
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

## <a name="help-us-make-azure-search-better"></a>Come contribuire al miglioramento di Ricerca di Azure
Per richieste di funzionalità o idee su miglioramenti da apportare, fornire i suggerimenti su [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

