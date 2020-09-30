---
title: Eseguire ricerche in BLOB in testo normale
titleSuffix: Azure Cognitive Search
description: Configurare un indicizzatore di ricerca per estrarre testo normale dai BLOB di Azure per la ricerca full-text in Azure ricerca cognitiva.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 417bdacc3ce8b619d5ec9618e6060ac071882471
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533926"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Come indicizzare i BLOB in testo normale in Azure ricerca cognitiva

Quando si usa un [indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md) per estrarre il testo ricercabile per la ricerca full-text, è possibile richiamare varie modalità di analisi per ottenere risultati di indicizzazione migliori. Per impostazione predefinita, l'indicizzatore analizza i BLOB di testo delimitati come singolo blocco di testo. Tuttavia, se tutti i BLOB contengono testo normale nella stessa codifica, è possibile migliorare significativamente le prestazioni di indicizzazione usando la **modalità di analisi del testo**.

## <a name="set-up-plain-text-indexing"></a>Configurare l'indicizzazione di testo normale

Per indicizzare i BLOB di testo normale, creare o aggiornare una definizione dell'indicizzatore con la `parsingMode` proprietà di configurazione a `text` in una richiesta [create Indexer](/rest/api/searchservice/create-indexer) :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Per impostazione predefinita, verrà utilizzata la codifica `UTF-8`. Per specificare una codifica diversa, utilizzare la proprietà di configurazione `encoding`: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Esempio di richiesta

Le modalità di analisi sono specificate nella definizione dell'indicizzatore.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Aiutaci a migliorare Azure ricerca cognitiva

Per richieste di funzionalità o idee su miglioramenti da apportare, fornire i suggerimenti su [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Per informazioni sull'uso della funzionalità esistente, pubblicare la domanda in [stack overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Passaggi successivi

* [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
* [Come configurare un indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md)
* [Panoramica dell'indicizzazione BLOB](search-blob-storage-integration.md)