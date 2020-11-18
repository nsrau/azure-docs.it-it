---
title: Esempi per Python
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice Python di Azure ricerca cognitiva demo che usano Azure .NET SDK per Python o REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 3de630552f7ad2cc941fe23369398c10ffce5870
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686848"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Esempi di codice Python per Azure ricerca cognitiva

Informazioni sugli esempi di codice Python che illustrano le funzionalità e le funzionalità di Azure ricerca cognitiva. I repository primari sono i seguenti:

| Archivio | Descrizione |
|------------|-------------|
| [Azure-SDK-for-Python/tree/master/SDK/ricerca/Azure-ricerca-documenti/esempi/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Esempi prodotti dal team di Azure SDK forniti con la libreria client di Azure.Search.Documents nell'SDK. È anche possibile esaminare gli [unit test](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) per la libreria client per vedere come vengono chiamate le varie API. |
| [Azure-Samples/Azure-Search-Python-esempi](https://github.com/Azure-Samples/azure-search-python-samples) | Esempi di codice che accompagnano gli articoli sulle procedure, inclusa [la Guida introduttiva: creare un indice di ricerca in Python](search-get-started-python.md).|

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?languages=csharp&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.

## <a name="python-sdk-samples"></a>Esempi di Python SDK

Azure SDK per Python include numerosi esempi e una [pagina](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) introduttiva che include i prerequisiti e l'installazione dei pacchetti. La pagina contiene inoltre i collegamenti agli esempi seguenti, elencati di seguito per praticità.

| Esempi | Descrizione |
|---------|-------------|
| [Autentica](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Viene illustrato come configurare un client ed eseguire l'autenticazione al servizio. | 
| [Operazioni di creazione dell'indice-lettura-aggiornamento-eliminazione](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare gli [indici di ricerca](search-what-is-an-index.md). |
| [Operazioni di creazione dell'indicizzatore-lettura-aggiornamento-eliminazione](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Viene illustrato come creare, aggiornare, ottenere, elencare, reimpostare ed eliminare gli [indicizzatori](search-indexer-overview.md). |
| [Cerca origini dati dell'indicizzatore](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare le origini dati dell'indicizzatore, necessarie per l'indicizzazione basata su indicizzatore di [origini dati di Azure supportate](search-indexer-overview.md#supported-data-sources). |
| [Sinonimi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Viene illustrato come creare, aggiornare, ottenere, elencare ed eliminare [mappe sinonimo](search-synonyms.md).  |
| [Caricare i documenti](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Viene illustrato come caricare o unire i documenti in un indice in un'operazione di [importazione dati](search-what-is-data-import.md) . |
| [Query semplice](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Viene illustrato come impostare una [query di base](search-query-overview.md). |
| [Filtra query](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Viene illustrata la configurazione di un' [espressione di filtro](search-filters.md). |
| [Query facet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Viene illustrato l'utilizzo di [facet](search-filters-facets.md). |

## <a name="documentation-samples"></a>Esempi di documentazione

Gli esempi seguenti includono un articolo associato in [Azure ricerca cognitiva documentazione](https://docs.microsoft.com/azure/search/).

| Esempi | Descrizione | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca in Python](search-get-started-python.md).  |
| [esercitazione-intelligenza artificiale](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Codice sorgente per [esercitazione: usare Python e intelligenza artificiale per generare contenuto ricercabile da BLOB di Azure](cognitive-search-tutorial-blob-python.md).  |
| [AzureML-Custom-skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Codice sorgente [, ad esempio, creare una competenza personalizzata usando Python](cognitive-search-custom-skill-python.md).  |