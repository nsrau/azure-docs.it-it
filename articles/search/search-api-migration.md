---
title: Aggiornare all'ultima versione dell'API REST del servizio di Ricerca di Azure - Ricerca di Azure
description: Esaminare le differenze nelle versioni API e ottenere informazioni su quali azioni sono necessarie per la migrazione del codice esistente per la versione più recente dell'API REST del servizio di Ricerca di Azure.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 85a8ddf4ce87d7ac8ce460c0aff56311a2ea4578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540685"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Aggiornare all'ultima versione dell'API REST di Ricerca di Azure
Se si usa una versione precedente del [API REST del servizio ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/), questo articolo consente di aggiornare l'applicazione per usare la versione dell'API disponibile a livello generale più recente, 2019-05-06.

Versione 2019-05-06 dell'API REST contiene alcune modifiche rispetto alle versioni precedenti. Le versioni sono abbastanza compatibili tra loro, pertanto la modifica del codice richiede un impegno minimo, a seconda della versione in uso prima. [Passaggi per l'aggiornamento](#UpgradeSteps) illustra le modifiche al codice necessarie per l'uso di nuove funzionalità.

> [!NOTE]
> Un'istanza del servizio ricerca di Azure supporta versioni una gamma di API REST, incluse quelle precedenti. È possibile continuare a usare le versioni dell'API, ma è consigliabile eseguire la migrazione del codice per la versione più recente in modo che è possibile accedere a nuove funzionalità.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Quali sono le novità nella versione 2019-05-06
Versione 2019-05-06 è la versione disponibile a livello generale più recente dell'API REST del servizio di ricerca di Azure. Le funzionalità che hanno eseguito la transizione allo stato disponibile a livello generale in questa versione dell'API includono:

* [Completamento automatico](index-add-suggesters.md) è una funzionalità di completamento automatico che viene completato un input di termine parzialmente specificato.

* [I tipi complessi](search-howto-complex-data-types.md) fornisce il supporto nativo per i dati oggetto strutturato in un indice di ricerca di Azure.

* [Modalità di analisi JsonLines](search-howto-index-json-blobs.md), parte del Blob di Azure l'indicizzazione, crea un documento di ricerca per ogni entità JSON che è separata da un carattere di nuova riga.

* [Ricerca cognitiva](cognitive-search-concept-intro.md) offre l'indicizzazione che sfrutta i motori di arricchimento di intelligenza artificiale di servizi cognitivi.

Diverse versioni di funzionalità di anteprima coincidono con questo aggiornamento disponibile a livello generale. Per esaminare l'elenco delle nuove funzionalità di anteprima, vedere [-versione dell'api REST di ricerca 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Modifiche di rilievo

Si interrompe il codice esistente che contiene le seguenti funzionalità di versione api-version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indicizzatore per Azure Cosmos DB: origine dati è adesso "type": "cosmosdb"

Se si usa un' [indicizzatore di Cosmos DB](search-howto-index-cosmosdb.md ), è necessario modificare `"type": "documentdb"` a `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Errori dei risultati esecuzione dell'indicizzatore non è più necessario stato

La struttura di errore per l'esecuzione dell'indicizzatore in precedenza era un `status` elemento. Questo elemento è stato rimosso perché non è quello di fornire informazioni utili.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Zdroj dat indexer API non restituisce più stringhe di connessione

Dall'API versioni 2019-05-06 e 2019-05-06-Preview e versioni successive, l'origine dati API non restituisce più stringhe di connessione nella risposta di qualsiasi operazione REST. Nelle versioni API precedenti, per le origini di dati create con POST, ricerca di Azure ha restituito **201** seguita da risposta OData, che conteneva la stringa di connessione in testo normale.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Denominato riconoscimento di entità sono stato sospeso competenze cognitive

Se si chiama [riconoscimento di entità nome](cognitive-search-skill-named-entity-recognition.md) competenza nel codice, la chiamata avrà esito negativo. Funzionalità di sostituzione [Entity Recognition](cognitive-search-skill-entity-recognition.md). Dovrebbe essere possibile sostituire il riferimento di competenza senza altre modifiche. La firma dell'API è lo stesso per entrambe le versioni. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Se esegue l'aggiornamento da una precedente versione GA, 2017-11-11 o 2016-09-01, è probabilmente non sarà necessario apportare modifiche al codice, oltre a modificare il numero di versione. Le uniche situazioni in cui può essere necessario modificare il codice si verificano quando:

* Il codice ha esito negativo quando vengono restituite proprietà sconosciute in una risposta API. Per impostazione predefinita, l'applicazione deve ignorare le proprietà che non riconosce.

* Il codice rende persistenti le richieste API e tenta di inviarle nuovamente alla nuova versione dell'API. Ad esempio, questa situazione può verificarsi se l'applicazione mantiene i token di continuazione restituiti dall'API di ricerca. Per altre informazioni, cercare `@search.nextPageParameters` nel [riferimento all'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Se una delle situazioni seguenti si applica al caso dell'utente, può essere necessario modificare il codice in modo appropriato. In caso contrario, non sono necessarie modifiche a meno che non si desidera iniziare a usare il [nuove funzionalità](#WhatsNew) della versione 2019-05-06.

Se esegue l'aggiornamento dalla versione API di anteprima, vale anche il codice precedente, ma è necessario essere consapevoli che alcune funzionalità di anteprima non sono disponibili nella versione 2019-05-06:

* [Query "Altri elementi simili"](search-more-like-this.md)
* [L'indicizzazione di blob CSV](search-howto-index-csv-blobs.md)
* [Supporto dell'API MongoDB per gli indicizzatori di Cosmos DB](search-howto-index-cosmosdb.md)

Se il codice Usa queste funzionalità, non sarà in grado di eseguire l'aggiornamento all'API versione 2019-05-06 senza rimuovere l'utilizzo di essi.

> [!IMPORTANT]
> Le API di anteprima sono destinate alle attività di test e valutazione e non devono essere usate negli ambienti di produzione.
> 

### <a name="upgrading-complex-types"></a>L'aggiornamento di tipi complessi

Se il codice Usa i tipi complessi con le versioni precedenti dell'API preview 2017-11-11-Preview o 2016-09-01-Preview, esistono alcuni limiti nuove e modificate nella versione 2019-05-06 di cui è necessario essere a conoscenza:

+ I limiti per la profondità dei campi secondari e il numero di raccolte complesse per ogni indice abbia stato ridotto. Se è stato creato gli indici che superano questi limiti tramite le api-versioni di anteprima, qualsiasi tentativo di aggiornare o crearli di nuovo usando l'API versione 2019-05-06 avrà esito negativo. Se ciò si applica all'utente, è necessario riprogettare lo schema per rientrare i nuovi limiti e quindi ricompilare l'indice.

+ È previsto un limite di nuovo nella versione api-version 2019-05-06 sul numero di elementi delle raccolte complesse per ogni documento. Se gli indici è stato creato con i documenti che superano questi limiti tramite le api-versioni di anteprima, qualsiasi tentativo di reindicizzare i dati usando l'api-version 2019-05-06 avrà esito negativo. Se si applica all'utente, è necessario per ridurre il numero di elementi di raccolta complessa per ciascun documento prima la reindicizzazione dei dati.

Per altre informazioni, vedere [limiti del servizio di ricerca di Azure](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Come aggiornare una struttura di tipo complesso precedente

Se il codice Usa i tipi complessi con una delle versioni precedenti dell'API di anteprima, è possibile che si stia utilizzando un formato di definizione di indice che si presenta come segue:

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

Un formato ad albero più recente per la definizione dei campi dell'indice è stato introdotto nella versione 2017-11-11-anteprima dell'API. Nel nuovo formato, ogni campo complesso dispone di una raccolta di campi in cui vengono definiti i campi secondari. Nell'API versione 2019-05-06, questo nuovo formato viene usato esclusivamente e si prova a creare o aggiornare un indice utilizzando il formato precedente avrà esito negativo. Se si dispone di indici creati usando il formato precedente, è necessario usare API versione 2017-11-11-anteprima per l'aggiornamento al nuovo formato prima che possono essere gestite usando l'API versione 2019-05-06.

Con i passaggi seguenti usando l'API versione 2017-11-11-Preview, è possibile aggiornare gli indici "flat" nel nuovo formato:

1. Eseguire una richiesta GET per recuperare l'indice. Se è già nel nuovo formato, è terminata.

2. Convertire l'indice di formato "flat" nel nuovo formato. È possibile scrivere codice per questo oggetto perché al momento della stesura di questo articolo non è disponibile alcun codice di esempio.

3. Eseguire una richiesta PUT per aggiornare l'indice nel nuovo formato. Assicurarsi di non modificare gli eventuali altri dettagli dell'indice, ad esempio l'individuabilità/filterability dei campi, dal momento che questo non è consentito dall'API di indice di aggiornamento.

> [!NOTE]
> Non è possibile gestire gli indici creati con il vecchio formato "flat" dal portale di Azure. Aggiornare gli indici dalla rappresentazione "flat" alla relativa rappresentazione in "albero" non appena possibile.

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione di riferimento API REST del servizio ricerca di Azure. Se si verificano problemi, richiedere assistenza sul [StackOverflow](https://stackoverflow.com/) oppure [contattare il supporto tecnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Servizio di ricerca riferimenti all'API REST](https://docs.microsoft.com/rest/api/searchservice/)

