---
title: Aggiornare le versioni dell'API RESTUpgrade REST API versions
titleSuffix: Azure Cognitive Search
description: Esaminare le differenze nelle versioni delle API e scoprire quali azioni sono necessarie per eseguire la migrazione del codice esistente alla versione più recente dell'API REST del servizio Ricerca cognitiva di Azure.Review differences in API versions and learn which actions are required to migrate existing code to the newest Azure Cognitive Search service REST API version.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112162"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Eseguire l'aggiornamento alla versione più recente dell'API REST del servizio Ricerca cognitiva di AzureUpgrade to the latest Azure Cognitive Search service REST API version

Se si usa una versione precedente dell'API REST di [ricerca,](https://docs.microsoft.com/rest/api/searchservice/)questo articolo consente di aggiornare l'applicazione per usare la versione API disponibile in generale più recente, 2019-05-06.

La versione 2019-05-06 dell'API REST contiene alcune modifiche delle versioni precedenti. Le versioni sono abbastanza compatibili tra loro, pertanto la modifica del codice richiede un impegno minimo, a seconda della versione in uso prima. [I passaggi per l'aggiornamento delineano](#UpgradeSteps) le modifiche al codice necessarie per l'utilizzo delle nuove funzionalità.

> [!NOTE]
> Un'istanza del servizio Ricerca cognitiva di Azure supporta una serie di versioni dell'API REST, incluse quelle precedenti. È possibile continuare a usare tali versioni dell'API, ma è consigliabile eseguire la migrazione del codice alla versione più recente in modo da poter accedere alle nuove funzionalità.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Novità della versione 2019-05-06
Versione 2019-05-06 è la versione più recente generalmente disponibile dell'API REST. Le funzionalità che sono passate allo stato generalmente disponibile in questa versione dell'API includono:Features that have transitiond to generally available status in this API version include:

* [Completamento automatico](index-add-suggesters.md) è una funzionalità typeahead che completa un input di termine parzialmente specificato.

* [I tipi](search-howto-complex-data-types.md) complessi forniscono supporto nativo per i dati degli oggetti strutturati nell'indice di ricerca.Complex types provides native support for structured object data in search index.

* [Le modalità di analisi JsonLines,](search-howto-index-json-blobs.md)parte dell'indicizzazione BLOB di Azure, creano un documento di ricerca per ogni entità JSON separato da una nuova riga.

* [L'arricchimento](cognitive-search-concept-intro.md) dell'iA fornisce un'indicizzazione che sfrutta i motori di arricchimento dell'IA di Servizi cognitivi.

Diversi rilasci di funzionalità di anteprima coincidono con questo aggiornamento generalmente disponibile. Per esaminare l'elenco delle nuove funzionalità di anteprima, vedere [Ricerca REST api-version 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Modifiche che causano un'interruzione

Il codice esistente che contiene le funzionalità seguenti verrà interrotto in api-version-2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indicizzatore per Azure Cosmos DB - datasource è ora "type": "cosmosdb"

Se si utilizza un [indicizzatore Cosmos DB](search-howto-index-cosmosdb.md ), è necessario passare `"type": "documentdb"` a `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Gli errori dei risultati dell'esecuzione dell'indicizzatore non hanno più statoIndexer execution result errors no longer have status

La struttura degli errori per `status` l'esecuzione dell'indicizzatore in precedenza disponeva di un elemento. Questo elemento è stato rimosso perché non forniva informazioni utili.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>L'API dell'origine dati dell'indicizzatore non restituisce più le stringhe di connessioneIndexer data source API no longer returns connection strings

Dalle versioni API 2019-05-06 e 2019-05-06-Preview in poi, l'API dell'origine dati non restituisce più le stringhe di connessione in risposta a qualsiasi operazione REST. Nelle versioni precedenti dell'API, per le origini dati create usando POST, Ricerca cognitiva di Azure ha restituito **201** seguito dalla risposta OData, che conteneva la stringa di connessione in testo normale.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>L'abilità cognitiva di riconoscimento delle entità denominate non è più disponibile

Se si chiama la competenza [Name Entity Recognition](cognitive-search-skill-named-entity-recognition.md) nel codice, la chiamata avrà esito negativo. La funzionalità di sostituzione è [Entity Recognition](cognitive-search-skill-entity-recognition.md). Dovresti essere in grado di sostituire il riferimento di abilità senza altre modifiche. La firma dell'API è la stessa per entrambe le versioni. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Se si esegue l'aggiornamento da una versione precedente ga, 2017-11-11 o 2016-09-01, probabilmente non sarà necessario apportare modifiche al codice, se non per modificare il numero di versione. Le uniche situazioni in cui può essere necessario modificare il codice si verificano quando:

* Il codice ha esito negativo quando vengono restituite proprietà sconosciute in una risposta API. Per impostazione predefinita, l'applicazione deve ignorare le proprietà che non riconosce.

* Il codice rende persistenti le richieste API e tenta di inviarle nuovamente alla nuova versione dell'API. Ad esempio, questa situazione può verificarsi se l'applicazione mantiene i token di continuazione restituiti dall'API di ricerca. Per altre informazioni, cercare `@search.nextPageParameters` nel [riferimento all'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Se una delle situazioni seguenti si applica al caso dell'utente, può essere necessario modificare il codice in modo appropriato. In caso contrario, non dovrebbero essere necessarie modifiche a meno che non si desideri iniziare a utilizzare le [nuove funzionalità](#WhatsNew) della versione 2019-05-06.

Se si esegue l'aggiornamento da una versione api di anteprima, si applica anche quanto sopra, ma è necessario tenere presente che alcune funzionalità di anteprima non sono disponibili nella versione 2019-05-06:If you are upgrading from a preview API version, the above also applies, but you must also be aware that some preview features are not available in version 2019-05-06:

* [Query "Altri elementi simili"](search-more-like-this.md)
* [Indicizzatore per BLOB CSV](search-howto-index-csv-blobs.md)
* [Supporto dell'API MongoDB per gli indicizzatori Cosmos DB](search-howto-index-cosmosdb.md)

Se il codice usa queste funzionalità, non sarà possibile eseguire l'aggiornamento alla versione API 2019-05-06 senza rimuoverle.

> [!IMPORTANT]
> Le API di anteprima sono destinate alle attività di test e valutazione e non devono essere usate negli ambienti di produzione.
> 

### <a name="upgrading-complex-types"></a>Aggiornamento di tipi complessi

Se il codice usa tipi complessi con le versioni precedenti dell'API di anteprima 2017-11-11-Preview o 2016-09-01-Preview, esistono alcuni limiti nuovi e modificati nella versione 2019-05-06 di cui è necessario tenere presente quanto:

+ I limiti relativi alla profondità dei sottocampi e al numero di raccolte complesse per indice sono stati ridotti. Se sono stati creati indici che superano questi limiti usando le versioni api di anteprima, qualsiasi tentativo di aggiornarli o ricrearli usando la versione API 2019-05-06 avrà esito negativo. Se questo si applica all'utente, sarà necessario riprogettare lo schema per adattarlo ai nuovi limiti e quindi ricompilare l'indice.

+ È disponibile un nuovo limite in api-version 2019-05-06 sul numero di elementi di raccolte complesse per documento. Se sono stati creati indici con documenti che superano questi limiti usando le versioni api di anteprima, qualsiasi tentativo di reindicizzare i dati utilizzando api-version 2019-05-06 avrà esito negativo. Se questo si applica all'utente, è necessario ridurre il numero di elementi di raccolta complessi per documento prima di reindicizzare i dati.

Per altre informazioni, vedere [Limiti del servizio per Ricerca cognitiva](search-limits-quotas-capacity.md)di Azure.For more information, see Service limits for Azure Cognitive Search .

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Come aggiornare una struttura di tipi complessi precedenteHow to upgrade an old complex type structure

Se il codice usa tipi complessi con una delle versioni precedenti dell'API di anteprima, è possibile che si stia usando un formato di definizione dell'indice simile al seguente:If your code is using complex types with one of the older preview API versions, you may be using an index definition format that looks like this:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Un formato ad albero più recente per la definizione dei campi di indice è stato introdotto nella versione API 2017-11-11-Preview. Nel nuovo formato, ogni campo complesso ha una raccolta di campi in cui sono definiti i relativi sottocampi. Nella versione API 2019-05-06, questo nuovo formato viene utilizzato in modo esclusivo e il tentativo di creare o aggiornare un indice utilizzando il formato precedente avrà esito negativo. Se si dispone di indici creati utilizzando il vecchio formato, è necessario utilizzare la versione API 2017-11-11-Preview per aggiornarli al nuovo formato prima che possano essere gestiti utilizzando la versione API 2019-05-06.

È possibile aggiornare gli indici "flat" al nuovo formato con i passaggi seguenti utilizzando la versione API 2017-11-11-Preview:

1. Eseguire una richiesta GET per recuperare l'indice. Se è già nel nuovo formato, il numero è finito.

2. Traslare l'indice dal formato "piatto" al nuovo formato. Sarà necessario scrivere codice per questo poiché non è disponibile alcun codice di esempio al momento della stesura di questo articolo.

3. Eseguire una richiesta PUT per aggiornare l'indice al nuovo formato. Assicurarsi di non modificare altri dettagli dell'indice, ad esempio la ricerca/filtro dei campi, poiché ciò non è consentito dall'API dell'indice di aggiornamento.

> [!NOTE]
> Non è possibile gestire gli indici creati con il vecchio formato "flat" dal portale di Azure.It is not possible to manage indexes created with the old "flat" format from the Azure portal. Si prega di aggiornare i tuoi indici dalla rappresentazione "piatta" alla rappresentazione "albero" al più presto conveniente.

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione di riferimento dell'API REST di ricerca. Se riscontri problemi, chiedici aiuto su [StackOverflow](https://stackoverflow.com/) o [contatta il supporto.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Riferimento all'API REST del servizio di ricercaSearch service REST API Reference](https://docs.microsoft.com/rest/api/searchservice/)

