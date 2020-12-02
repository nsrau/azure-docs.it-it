---
title: Esempi di Java
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice Java ricerca cognitiva demo di Azure che usano Azure .NET SDK per Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 10dff18f7b9db7273fcd6ec92bcca5970bb83b08
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510370"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Esempi di codice Java per Azure ricerca cognitiva

Informazioni sugli esempi di codice Java che illustrano le funzionalità e le funzionalità di Azure ricerca cognitiva. I repository primari sono i seguenti:

| Archivio | Descrizione |
|------------|-------------|
| [Azure-SDK-for-Java/tree/master/SDK/ricerca/Azure-search-documents/src/Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Esempi prodotti dal team di Azure SDK forniti con la libreria client di Azure.Search.Documents nell'SDK. È anche possibile esaminare gli [unit test](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) per la libreria client per vedere come vengono chiamate le varie API. |
| [Azure-Samples/Azure-Search-java-Samples](https://github.com/Azure-Samples/azure-search-java-samples) | Esempi di codice che accompagnano gli articoli sulle procedure. Gli **esempi in questo repository non sono ancora stati aggiornati a usare Azure SDK per Java**. Attualmente, questi esempi chiamano le API REST nel codice Java.|

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?languages=java&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.

## <a name="java-sdk-samples"></a>Esempi di Java SDK

Azure SDK per Java include numerosi esempi e una [pagina](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) introduttiva che illustra l'installazione dei pacchetti. La pagina elenca anche un'ampia gamma di esempi. Alcune delle operazioni più comuni sono elencate di seguito per praticità.

| Esempi | Descrizione |
|---------|-------------|
| [Creazione dell'indice di ricerca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Viene illustrato come creare [indici di ricerca](search-what-is-an-index.md). |
| [Creazione di sinonimi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Viene illustrato come creare [mappe sinonimo](search-synonyms.md).  |
| [Creazione dell'indicizzatore di ricerca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Viene illustrato come creare gli [indicizzatori](search-indexer-overview.md). |
| [Creazione dell'origine dati dell'indicizzatore di ricerca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Viene illustrato come creare origini dati dell'indicizzatore, necessarie per l'indicizzazione basata su indicizzatore di [origini dati di Azure supportate](search-indexer-overview.md#supported-data-sources). |
| [Creazione di competenze](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Viene illustrato come creare [skillsets](cognitive-search-working-with-skillsets.md) che sono indicizzatori collegati e che eseguono l'arricchimento basato su intelligenza artificiale durante l'indicizzazione. |
| [Caricare i documenti](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Viene illustrato come caricare o unire i documenti in un indice in un'operazione di [importazione dati](search-what-is-data-import.md) . |
| [Sintassi di query](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Viene illustrato come impostare una [query di base](search-query-overview.md). |

## <a name="documentation-samples"></a>Esempi di documentazione

Gli esempi seguenti includono un articolo associato in [Azure ricerca cognitiva documentazione](./index.yml).

| Esempi | Descrizione | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca in Java](search-get-started-java.md). Questo esempio chiama le API REST. |
| [ricerca-Java-Indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Viene illustrato un indicizzatore Azure Cosmos DB in Java. Questo esempio chiama le API REST. |