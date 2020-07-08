---
title: Aggiornare le versioni dell'API REST
titleSuffix: Azure Cognitive Search
description: Esaminare le differenze nelle versioni dell'API e scoprire quali azioni sono necessarie per eseguire la migrazione del codice esistente alla versione più recente dell'API REST del servizio ricerca cognitiva di Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 1e5269333de27c146d4b9e2040801c4b14564125
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85562619"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Eseguire l'aggiornamento alla versione più recente dell'API REST del servizio ricerca cognitiva di Azure

Se si usa una versione precedente dell' [API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/), questo articolo consentirà di aggiornare l'applicazione per usare la versione API disponibile a livello generale più recente, 2020-06-30.

La versione 2020-06-30 dell'API REST contiene alcune modifiche rispetto alle versioni precedenti. Le versioni sono abbastanza compatibili tra loro, pertanto la modifica del codice richiede un impegno minimo, a seconda della versione in uso prima. I [passaggi per l'aggiornamento](#UpgradeSteps) delineano le modifiche del codice necessarie per l'utilizzo delle nuove funzionalità.

> [!NOTE]
> Un'istanza del servizio ricerca cognitiva di Azure supporta una gamma di versioni dell'API REST, incluse quelle precedenti. È possibile continuare a usare queste versioni dell'API, ma è consigliabile eseguire la migrazione del codice alla versione più recente, in modo da poter accedere a nuove funzionalità.

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

Quando si esegue l'aggiornamento a nuove versioni, probabilmente non sarà necessario apportare modifiche al codice, tranne che per modificare il numero di versione. Le uniche situazioni in cui può essere necessario modificare il codice si verificano quando:

* Il codice ha esito negativo quando vengono restituite proprietà sconosciute in una risposta API. Per impostazione predefinita, l'applicazione deve ignorare le proprietà che non riconosce.

* Il codice rende persistenti le richieste API e tenta di inviarle nuovamente alla nuova versione dell'API. Ad esempio, questa situazione può verificarsi se l'applicazione mantiene i token di continuazione restituiti dall'API di ricerca. Per altre informazioni, cercare `@search.nextPageParameters` nel [riferimento all'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

* Il codice fa riferimento a una versione API che precede 2019-05-06 ed è soggetta a una o più delle modifiche di rilievo apportate a tale versione. La sezione [aggiornamento a 2019-05-06](#upgrade-to-2019-05-06) fornisce maggiori dettagli. 

Se si verifica una di queste situazioni, potrebbe essere necessario modificare il codice di conseguenza. In caso contrario, non dovrebbe essere necessario apportare modifiche a meno che non si voglia iniziare a usare le funzionalità aggiunte nella nuova versione.

## <a name="upgrade-to-2020-06-30"></a>Aggiornamento a 2020-06-30

La versione 2020-06-30 è la nuova versione disponibile a livello generale dell'API REST. Non sono state apportate modifiche di rilievo, ma esistono alcune differenze di comportamento. 

Le funzionalità sono ora disponibili a livello generale in questa versione dell'API, tra cui:

* [Archivio informazioni](knowledge-store-concept-intro.md), archiviazione permanente di contenuti arricchiti creati tramite skillsets, creati per l'analisi downstream e l'elaborazione tramite altre applicazioni. Con questa funzionalità, una pipeline di arricchimento di intelligenza artificiale basata sull'indicizzatore può popolare un archivio informazioni oltre a un indice di ricerca. Se è stata usata la versione di anteprima di questa funzionalità, è equivalente alla versione disponibile a livello generale. L'unica modifica del codice richiesta è la modifica della versione API.

Le modifiche del comportamento includono quanto segue:

* L' [algoritmo di classificazione BM25](index-ranking-similarity.md) sostituisce l'algoritmo di classificazione precedente con la tecnologia più recente. I nuovi servizi utilizzeranno questo algoritmo automaticamente. Per i servizi esistenti, è necessario impostare i parametri per l'utilizzo del nuovo algoritmo.

* I risultati ordinati per i valori null sono stati modificati in questa versione, con i valori Null visualizzati per primi se l'ordinamento è `asc` e l'ultimo se l'ordinamento è `desc` . Se è stato scritto codice per gestire il modo in cui vengono ordinati i valori null, tenere presente questa modifica.

## <a name="upgrade-to-2019-05-06"></a>Aggiornamento a 2019-05-06

La versione 2019-05-06 è la versione disponibile a livello generale precedente dell'API REST. Le funzionalità che sono diventate disponibili a livello generale in questa versione dell'API includono:

* Il [completamento automatico](index-add-suggesters.md) è una funzionalità di typeahead che completa un input di termine parzialmente specificato.
* I [tipi complessi](search-howto-complex-data-types.md) forniscono il supporto nativo per i dati di oggetti strutturati nell'indice di ricerca.
* [Modalità di analisi di JsonLines](search-howto-index-json-blobs.md), parte dell'indicizzazione BLOB di Azure, crea un documento di ricerca per entità JSON separato da una nuova riga.
* L' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) fornisce l'indicizzazione che sfrutta i motori di arricchimento di intelligenza artificiale dei servizi cognitivi.

### <a name="breaking-changes"></a>Modifiche di rilievo

Il codice esistente scritto con le versioni precedenti dell'API si interrompe in API-Version = 2019-05-06 Se il codice contiene le funzionalità seguenti:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer per Azure Cosmos DB-DataSource è ora "Type": "cosmosdb"

Se si utilizza un [indicizzatore di Cosmos DB](search-howto-index-cosmosdb.md ), è necessario modificare `"type": "documentdb"` in `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Gli errori di risultato dell'esecuzione dell'indicizzatore non hanno più lo stato

La struttura di errore per l'esecuzione dell'indicizzatore aveva in precedenza un `status` elemento. Questo elemento è stato rimosso perché non forniva informazioni utili.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>L'API dell'origine dati dell'indicizzatore non restituisce più le stringhe di connessione

Dalle versioni API 2019-05-06 e 2019-05-06-Preview in poi, l'API dell'origine dati non restituisce più le stringhe di connessione nella risposta di qualsiasi operazione REST. Nelle versioni precedenti dell'API, per le origini dati create con POST, Azure ricerca cognitiva ha restituito **201** seguito dalla risposta OData, che conteneva la stringa di connessione in testo normale.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Il riconoscimento delle entità denominate skill cognitive è ora interrotto

Se è stata chiamata la competenza [Riconoscimento entità nome](cognitive-search-skill-named-entity-recognition.md) nel codice, la chiamata avrà esito negativo. La funzionalità di sostituzione è il [riconoscimento delle entità](cognitive-search-skill-entity-recognition.md). Dovrebbe essere possibile sostituire il riferimento a skill senza altre modifiche. La firma API è identica per entrambe le versioni. 

### <a name="upgrading-complex-types"></a>Aggiornamento di tipi complessi

La versione API 2019-05-06 ha aggiunto il supporto formale per i tipi complessi. Se il codice ha implementato raccomandazioni precedenti per l'equivalenza dei tipi complessi in 2017-11-11-Preview o 2016-09-01-Preview, sono stati introdotti alcuni limiti nuovi e modificati a partire dalla versione 2019-05-06 di cui è necessario essere consapevoli:

+ I limiti relativi alla profondità dei sottocampi e al numero di raccolte complesse per indice sono stati ridotti. Se sono stati creati indici che superano questi limiti usando le versioni API di anteprima, qualsiasi tentativo di aggiornarli o ricrearli usando l'API versione 2019-05-06 avrà esito negativo. Se si applica questo problema, sarà necessario riprogettare lo schema in base ai nuovi limiti e quindi ricompilare l'indice.

+ È presente un nuovo limite a partire da API-Version 2019-05-06 sul numero di elementi di raccolte complesse per documento. Se sono stati creati indici con documenti che superano questi limiti usando le versioni API di anteprima, qualsiasi tentativo di reindicizzare i dati con API-Version 2019-05-06 avrà esito negativo. Se si applica questo problema, sarà necessario ridurre il numero di elementi di raccolta complessi per documento prima di reindicizzare i dati.

Per altre informazioni, vedere [limiti dei servizi per Azure ricerca cognitiva](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Come aggiornare una struttura di tipi complessi obsoleti

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

Vedere la documentazione di riferimento dell'API REST di ricerca. Se si verificano problemi, chiedere assistenza per [stack overflow](https://stackoverflow.com/) o [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST del servizio di ricerca](https://docs.microsoft.com/rest/api/searchservice/)