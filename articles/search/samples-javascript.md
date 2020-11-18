---
title: Esempi JavaScript
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice JavaScript di Azure ricerca cognitiva demo che usano Azure .NET SDK per JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6cd696bf0853b1e6bafc06f2e99b2808970fed25
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686857"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Esempi di codice JavaScript per ricerca cognitiva di Azure

Informazioni sugli esempi di codice JavaScript che illustrano le funzionalità e le funzionalità di Azure ricerca cognitiva. I repository primari sono i seguenti:

| Archivio | Descrizione |
|------------|-------------|
| [Azure-SDK-for-JS/tree/master/SDK/ricerca/ricerca-documenti](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Esempi prodotti dal team di Azure SDK forniti con la libreria client di Azure.Search.Documents nell'SDK. È anche possibile esaminare gli [unit test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) per la libreria client per vedere come vengono chiamate le varie API. |
| [Azure-Samples/Azure-Search-JavaScript-esempi](https://github.com/Azure-Samples/azure-search-javascript-samples) | Esempi di codice che accompagnano gli articoli sulle procedure, inclusa [la Guida introduttiva: creare un indice di ricerca in JavaScript](search-get-started-javascript.md).|

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?languages=csharp&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.

## <a name="javascript-sdk-samples"></a>Esempi di JavaScript SDK

Azure SDK per Java include numerosi esempi e una [pagina](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) introduttiva che illustra l'installazione del pacchetto, la configurazione del client e la risoluzione dei problemi. La pagina descrive anche le categorie di esempio seguenti, elencate di seguito per praticità.

| Esempi | Descrizione |
|---------|-------------|
| [indici](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare gli [indici di ricerca](search-what-is-an-index.md). Questa categoria di esempio include anche un esempio di statistica del servizio. |
| [dataSourceConnections (per gli indicizzatori)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare le origini dati dell'indicizzatore, necessarie per l'indicizzazione basata su indicizzatore di [origini dati di Azure supportate](search-indexer-overview.md#supported-data-sources). |
| [indicizzatori](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Viene illustrato come creare, aggiornare, ottenere, elencare, reimpostare ed eliminare gli [indicizzatori](search-indexer-overview.md).|
| [Competenze](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [skillsets](cognitive-search-working-with-skillsets.md) che sono indicizzatori collegati e che eseguono l'arricchimento basato su intelligenza artificiale durante l'indicizzazione. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [mappe sinonimo](search-synonyms.md).  |
| [Query](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Le query sono di sola lettura. Questa query di esempio viene eseguita in base a un indice pubblico ospitato da Microsoft.  |

## <a name="typescript-samples"></a>Esempi di TypeScript

L'SDK fornisce anche esempi di TypeScript, elencati di seguito per praticità.

| Esempi | Descrizione |
|---------|-------------|
| [indici](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare gli [indici di ricerca](search-what-is-an-index.md). Questa categoria di esempio include anche un esempio di statistica del servizio. |
| [dataSourceConnections (per gli indicizzatori)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare le origini dati dell'indicizzatore, necessarie per l'indicizzazione basata su indicizzatore di [origini dati di Azure supportate](search-indexer-overview.md#supported-data-sources). |
| [indicizzatori](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Viene illustrato come creare, aggiornare, ottenere, elencare, reimpostare ed eliminare gli [indicizzatori](search-indexer-overview.md).|
| [Competenze](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [skillsets](cognitive-search-working-with-skillsets.md) che sono indicizzatori collegati e che eseguono l'arricchimento basato su intelligenza artificiale durante l'indicizzazione. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [mappe sinonimo](search-synonyms.md).  |
| [Query](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.js) | Viene illustrata l'esecuzione di query su un indice pubblico di sola lettura ospitato da Microsoft.  |

## <a name="documentation-samples"></a>Esempi di documentazione

Gli esempi seguenti includono un articolo associato in [Azure ricerca cognitiva documentazione](https://docs.microsoft.com/azure/search/).

| Esempi | Descrizione | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/Quickstart) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca in JavaScript](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Esempi autonomi

| Esempi | Descrizione |
|---------|-------------|
| [Azure-Search-React-template](https://github.com/dereklegenzoff/azure-search-react-template) | Modello React per Azure ricerca cognitiva (github.com) |