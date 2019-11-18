---
title: Aggiornare le versioni dell'API REST
titleSuffix: Azure Cognitive Search
description: Esaminare le differenze nelle versioni dell'API e scoprire quali azioni sono necessarie per eseguire la migrazione del codice esistente alla versione più recente dell'API REST del servizio ricerca cognitiva di Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112162"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Eseguire l'aggiornamento alla versione più recente dell'API REST del servizio ricerca cognitiva di Azure

Se si usa una versione precedente dell' [API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/), questo articolo consentirà di aggiornare l'applicazione per usare la versione API disponibile a livello generale più recente, 2019-05-06.

La versione 2019-05-06 dell'API REST contiene alcune modifiche rispetto alle versioni precedenti. Le versioni sono abbastanza compatibili tra loro, pertanto la modifica del codice richiede un impegno minimo, a seconda della versione in uso prima. I [passaggi per l'aggiornamento](#UpgradeSteps) delineano le modifiche del codice necessarie per l'utilizzo delle nuove funzionalità.

> [!NOTE]
> Un'istanza del servizio ricerca cognitiva di Azure supporta una gamma di versioni dell'API REST, incluse quelle precedenti. È possibile continuare a usare queste versioni dell'API, ma è consigliabile eseguire la migrazione del codice alla versione più recente, in modo da poter accedere a nuove funzionalità.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Novità della versione 2019-05-06
La versione 2019-05-06 è la versione più recente disponibile a livello generale dell'API REST. Le funzionalità che sono passate allo stato disponibile a livello generale in questa versione dell'API includono:

* Il [completamento automatico](index-add-suggesters.md) è una funzionalità di typeahead che completa un input di termine parzialmente specificato.

* I [tipi complessi](search-howto-complex-data-types.md) forniscono il supporto nativo per i dati di oggetti strutturati nell'indice di ricerca.

* [Modalità di analisi di JsonLines](search-howto-index-json-blobs.md), parte dell'indicizzazione BLOB di Azure, crea un documento di ricerca per entità JSON separato da una nuova riga.

* L' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) fornisce l'indicizzazione che sfrutta i motori di arricchimento di intelligenza artificiale dei servizi cognitivi.

Diverse versioni delle funzionalità di anteprima coincidono con questo aggiornamento disponibile a livello generale. Per esaminare l'elenco delle nuove funzionalità di anteprima, vedere [ricerca API REST-versione 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Modifiche di rilievo

Il codice esistente che contiene le funzionalità seguenti si interrompe in API-Version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer per Azure Cosmos DB-DataSource è ora "Type": "cosmosdb"

Se si usa un [indicizzatore di Cosmos DB](search-howto-index-cosmosdb.md ), è necessario modificare `"type": "documentdb"` in `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Gli errori di risultato dell'esecuzione dell'indicizzatore non hanno più lo stato

La struttura di errore per l'esecuzione dell'indicizzatore aveva in precedenza un elemento `status`. Questo elemento è stato rimosso perché non forniva informazioni utili.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>L'API dell'origine dati dell'indicizzatore non restituisce più le stringhe di connessione

Dalle versioni API 2019-05-06 e 2019-05-06-Preview in poi, l'API dell'origine dati non restituisce più le stringhe di connessione nella risposta di qualsiasi operazione REST. Nelle versioni precedenti dell'API, per le origini dati create con POST, Azure ricerca cognitiva ha restituito **201** seguito dalla risposta OData, che conteneva la stringa di connessione in testo normale.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Il riconoscimento delle entità denominate skill cognitive è ora interrotto

Se si chiama [nome entità riconoscimento entità](cognitive-search-skill-named-entity-recognition.md) nel codice, la chiamata avrà esito negativo. La funzionalità di sostituzione è il [riconoscimento delle entità](cognitive-search-skill-entity-recognition.md). Dovrebbe essere possibile sostituire il riferimento a skill senza altre modifiche. La firma API è identica per entrambe le versioni. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Se si esegue l'aggiornamento da una versione GA precedente, 2017-11-11 o 2016-09-01, probabilmente non sarà necessario apportare modifiche al codice, tranne che per modificare il numero di versione. Le uniche situazioni in cui può essere necessario modificare il codice si verificano quando:

* Il codice ha esito negativo quando vengono restituite proprietà sconosciute in una risposta API. Per impostazione predefinita, l'applicazione deve ignorare le proprietà che non riconosce.

* Il codice rende persistenti le richieste API e tenta di inviarle nuovamente alla nuova versione dell'API. Ad esempio, questa situazione può verificarsi se l'applicazione mantiene i token di continuazione restituiti dall'API di ricerca. Per altre informazioni, cercare `@search.nextPageParameters` nel [riferimento all'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Se una delle situazioni seguenti si applica al caso dell'utente, può essere necessario modificare il codice in modo appropriato. In caso contrario, non dovrebbe essere necessario apportare modifiche a meno che non si desideri iniziare a utilizzare le [nuove funzionalità](#WhatsNew) della versione 2019-05-06.

Se si esegue l'aggiornamento da una versione dell'API di anteprima, si applica anche il precedente, ma è anche necessario tenere presente che alcune funzionalità di anteprima non sono disponibili nella versione 2019-05-06:

* [Query "più simili a questa"](search-more-like-this.md)
* [Indicizzazione BLOB CSV](search-howto-index-csv-blobs.md)
* [Supporto dell'API MongoDB per Cosmos DB indicizzatori](search-howto-index-cosmosdb.md)

Se il codice usa queste funzionalità, non sarà possibile eseguire l'aggiornamento alla versione API 2019-05-06 senza rimuoverne l'utilizzo.

> [!IMPORTANT]
> Le API di anteprima sono destinate alle attività di test e valutazione e non devono essere usate negli ambienti di produzione.
> 

### <a name="upgrading-complex-types"></a>Aggiornamento di tipi complessi

Se il codice usa tipi complessi con le versioni precedenti dell'API di anteprima 2017-11-11-Preview o 2016-09-01-Preview, sono presenti alcuni limiti nuovi e modificati nella versione 2019-05-06 di cui è necessario tenere conto:

+ I limiti relativi alla profondità dei sottocampi e al numero di raccolte complesse per indice sono stati ridotti. Se sono stati creati indici che superano questi limiti usando le versioni API di anteprima, qualsiasi tentativo di aggiornarli o ricrearli usando l'API versione 2019-05-06 avrà esito negativo. Se si applica questo problema, sarà necessario riprogettare lo schema in base ai nuovi limiti e quindi ricompilare l'indice.

+ Esiste un nuovo limite in API-Version 2019-05-06 sul numero di elementi di raccolte complesse per documento. Se sono stati creati indici con documenti che superano questi limiti usando le versioni API di anteprima, qualsiasi tentativo di reindicizzare i dati con API-Version 2019-05-06 avrà esito negativo. Se si applica questo problema, sarà necessario ridurre il numero di elementi di raccolta complessi per documento prima di reindicizzare i dati.

Per altre informazioni, vedere [limiti dei servizi per Azure ricerca cognitiva](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Come aggiornare una struttura di tipi complessi obsoleti

Se il codice usa tipi complessi con una delle versioni precedenti dell'API di anteprima, è possibile che si stia usando un formato di definizione di indice simile al seguente:

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

Un nuovo formato di tipo albero per la definizione dei campi di indice è stato introdotto nella versione API 2017-11-11-Preview. Nel nuovo formato ogni campo complesso dispone di una raccolta di campi in cui sono definiti i relativi campi secondari. Nella versione API 2019-05-06, questo nuovo formato viene usato esclusivamente e il tentativo di creare o aggiornare un indice con il vecchio formato avrà esito negativo. Se sono stati creati indici con il formato precedente, sarà necessario usare la versione API 2017-11-11-Preview per aggiornarli nel nuovo formato prima di poterli gestire con la versione 2019-05-06 dell'API.

È possibile aggiornare gli indici "flat" al nuovo formato con i passaggi seguenti usando l'API versione 2017-11-11-Preview:

1. Eseguire una richiesta GET per recuperare l'indice. Se è già nel nuovo formato, l'operazione è terminata.

2. Tradurre l'indice dal formato "flat" al nuovo formato. È necessario scrivere codice perché non è disponibile codice di esempio al momento della stesura di questo articolo.

3. Eseguire una richiesta PUT per aggiornare l'indice al nuovo formato. Assicurarsi di non modificare altri dettagli dell'indice, ad esempio la ricercabilità/filtrabilità dei campi, perché questo non è consentito dall'API Update index.

> [!NOTE]
> Non è possibile gestire gli indici creati con il vecchio formato "flat" dal portale di Azure. Aggiornare gli indici dalla rappresentazione "flat" alla rappresentazione "Tree" con la prima convenienza.

## <a name="next-steps"></a>Passaggi successivi

Vedere la documentazione di riferimento dell'API REST di ricerca. Se si verificano problemi, richiedere assistenza in [StackOverflow](https://stackoverflow.com/) o [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST del servizio di ricerca](https://docs.microsoft.com/rest/api/searchservice/)

