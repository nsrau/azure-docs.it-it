---
title: Eseguire query su un indice di Ricerca di Azure | Documentazione Microsoft
description: Compilare una query di ricerca in Ricerca di Azure e usare i parametri di ricerca per filtrare e ordinare i risultati della ricerca.
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 69205d7a-363f-4b92-a53f-6ca818a3d2c7
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 04/26/2017
ms.author: ashmaka
ms.openlocfilehash: 01be1b14e838c4f1b6f2498111fb8369c2bbb92a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="query-your-azure-search-index"></a>Eseguire query su un indice di Ricerca di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-query-overview.md)
> * [Portale](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Quando si inviano richieste di ricerca in Ricerca di Azure, è possibile specificare una serie di parametri insieme alle parole effettive digitate nella casella di ricerca dell'applicazione. Questi parametri di query consentono di ottenere un controllo più approfondito dell'[esperienza di ricerca full-text](search-lucene-query-architecture.md).

Di seguito è riportato un elenco che illustra brevemente gli usi più comuni dei parametri di query in Ricerca di Azure. Per la copertura completa dei parametri di query e il relativo comportamento, vedere le pagine dettagliate per l'[API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) e [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary).

## <a name="types-of-queries"></a>Tipi di query
Ricerca di Azure offre numerose opzioni per creare query estremamente avanzate. I due tipi di query principali che si useranno sono `search` e `filter`. Una query `search` cerca uno o più termini in tutti i campi *ricercabili* dell'indice e funziona come un motore di ricerca, ad esempio Google o Bing. Una query `filter` valuta un'espressione booleana su tutti i campi *filtrabili* di un indice. Diversamente dalle query `search`, le query `filter` ricercano la corrispondenza esatta con il contenuto di un campo, quindi supportano la distinzione tra lettere maiuscole e minuscole per i campi di tipo stringa.

È possibile usare le ricerche e i filtri insieme o separatamente. Se si usano insieme, prima viene applicato il filtro all'intero indice e quindi viene eseguita la ricerca sui risultati del filtro. I filtri quindi possono essere un'utile tecnica per migliorare le prestazioni delle query perché riducono il set di documenti che la query di ricerca deve elaborare.

La sintassi per le espressioni di filtro è un subset del [linguaggio di filtro OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Per le query di ricerca è possibile usare la [sintassi semplificata](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) o la [sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search), illustrate di seguito.

### <a name="simple-query-syntax"></a>Sintassi di query semplice
La [sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) è il linguaggio di query predefinito usato in Ricerca di Azure. La sintassi di query semplice supporta un numero di operatori di ricerca comuni tra cui gli operatori AND, OR, NOT, frase, suffisso e di precedenza.

### <a name="lucene-query-syntax"></a>sintassi di query Lucene
La [sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) consente di usare il linguaggio di query espressivo e ampiamente diffuso sviluppato come parte di [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Usando questa sintassi di query è possibile ottenere facilmente le funzionalità seguenti: [query con ambito campo](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields), [ricerca fuzzy](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy), [ricerca di prossimità](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity), [aumento priorità dei termini](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost), [ricerca di espressione regolare](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex), [ricerca con caratteri jolly](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard), [elementi fondamentali della sintassi](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax) e [query con operatori booleani](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean).

## <a name="ordering-results"></a>Ordinamento dei risultati
Quando si ricevono i risultati di una query di ricerca, è possibile richiedere che Ricerca di Azure presenti i risultati ordinati in base ai valori di un campo specifico. Per impostazione predefinita, Ricerca di Azure ordina i risultati della ricerca in base alle priorità del punteggio di ricerca di ciascun documento, che deriva da [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se si vuole che Ricerca di Azure restituisca i risultati ordinati in base a un valore diverso dal punteggio di ricerca, è possibile usare il parametro di ricerca `orderby` . È possibile specificare il valore del parametro `orderby` per includere i nomi dei campi e le chiamate alla [`geo.distance()` funzione](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) per ottenere valori geospaziali. Ogni espressione può essere seguita da `asc` per indicare che i risultati vengono richiesti in ordine crescente e `desc` per indicare che i risultati vengono richiesti in ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente.

## <a name="paging"></a>Paging
Ricerca di Azure rende più facile implementare il paging dei risultati della ricerca. Tramite i parametri `top` e `skip` è possibile eseguire in modo uniforme le richieste di ricerca che consentono di ricevere il set totale di risultati della ricerca in subset gestibili e ordinati in grado di abilitare facilmente ottime procedure di ricerca nell'interfaccia utente. Quando si ricevono questi subset di risultati più piccoli, è anche possibile ottenere il numero di documenti nel set di totale dei risultati della ricerca.

Altre informazioni sul paging dei risultati della ricerca sono disponibili nell'articolo [Come impaginare i risultati della ricerca in Ricerca di Azure](search-pagination-page-layout.md).

## <a name="hit-highlighting"></a>Evidenziazione dei risultati
In Ricerca di Azure è semplice mettere in evidenza la parte esatta dei risultati della ricerca che corrispondono alla query di ricerca usando i parametri `highlight`, `highlightPreTag` e `highlightPostTag`. È possibile specificare quali campi *ricercabili* devono avere il testo corrispondente evidenziato e specificare anche i tag della stringa esatta da aggiungere all'inizio e alla fine del testo corrispondente restituito da Ricerca di Azure.

## <a name="try-out-query-syntax"></a>Prova della sintassi di query

Il modo migliore per comprendere le differenze di sintassi consiste nell'inviare query ed esaminarne i risultati.

+ Usare [Esplora ricerche](search-explorer.md) nel portale di Azure. Distribuendo l'[indice di esempio](search-get-started-portal.md), è possibile eseguire una query dell'indice in pochi minuti usando gli strumenti del portale.

+ Usare Telerik Fiddler o Chrome Postman per inviare query a un indice che è stato caricato nel servizio di ricerca. Entrambi gli strumenti supportano le chiamate REST a un endpoint HTTP. 