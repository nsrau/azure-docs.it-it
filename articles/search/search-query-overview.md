---
title: Concetti fondamentali sulle query in Ricerca di Azure | Microsoft Docs
description: Concetti fondamentali per creare una query di ricerca in Ricerca di Azure, usando parametri per filtrare, selezionare e ordinare risultati.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366447"
---
# <a name="query-fundamentals-in-azure-search"></a>Concetti fondamentali sulle query in Ricerca di Azure

In Ricerca di Azure una definizione di query è una specifica completa di una richiesta che include queste parti: endpoint dell'URL di servizio, indice di destinazione, chiave API usata per autenticare la richiesta, versione dell'API e stringa di query. 

La composizione della stringa di query include i parametri definiti nell'[API di ricerca nei documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents). Il più importante è il parametro **search=**, che potrebbe non avere definizione (`search=*`) ma che più probabilmente contiene termini, frasi e operatori come nell'esempio seguente:

```
"search": "seattle townhouse +\"lake\""
```

Altri parametri vengono usati per indirizzare l'elaborazione delle query o migliorare la risposta. I parametri possono essere usati, ad esempio, per definire l'ambito di una ricerca in campi specifici, impostare una modalità di ricerca per modulare la precisione della differenza tra precisione e richiamo e l'aggiunta di un conteggio per poter tenere traccia dei risultati. 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

Sebbene la definizione di una query sia fondamentale, lo schema dell'indice è ugualmente importante in quanto specifica le operazioni consentite in base a ogni campo. Durante lo sviluppo dell'indice, gli attributi sui campi determinano le operazioni consentite. Ad esempio, per essere idoneo alla ricerca full-text e all'inclusione nei risultati di ricerca, un campo deve essere contrassegnato come *disponibile per la ricerca* e *recuperabile*.

Al momento della query, l'esecuzione avviene sempre rispetto a un indice e l'autenticazione viene eseguita con una chiave API fornita nella richiesta. Non è possibile unire gli indici o creare strutture di dati personalizzate o temporanee come destinazione della query.  

I risultati della query vengono trasmessi come documenti JSON nell'API REST. Se si utilizzano le API .NET la serializzazione è integrata. È possibile plasmare i risultati impostando parametri per la query, selezionando campi specifici per il risultato

In breve, la sostanza della richiesta di query specifica l'ambito e le operazioni: quali campi includere nella ricerca, quali includere nel set di risultati, se ordinare o filtrare e così via. Se non viene specificata, una query viene eseguita rispetto a tutti i campi disponibili per la ricerca come un'operazione di ricerca full-text che restituisce un set di risultati senza punteggio in un ordine arbitrario.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>Tipi di query: ricerca e filtro

Ricerca di Azure offre numerose opzioni per creare query estremamente avanzate. I due tipi di query principali che si useranno sono `search` e `filter`. 

+ Le query `search` cercano uno o più termini in tutti i campi *disponibili per la ricerca* nell'indice e funzionano come si ritiene che funzionerebbe un motore di ricerca come Google o Bing. Gli esempi nell'introduzione usano il parametro `search`.

+ Le query `filter` valutano un'espressione booleana su tutti i campi *filtrabili* in un indice. Contrariamente a `search`, una query `filter` corrisponde al contenuto esatto di un campo, inclusa la distinzione tra maiuscole e minuscole nei campi della stringa.

È possibile usare la ricerca e il filtro insieme o separatamente. Un filtro autonomo, senza una stringa di query, è utile quando l'espressione del filtro è in grado di qualificare i documenti che interessano. Senza una stringa di query, non ci sono un'analisi lessicale o linguistica, un'assegnazione del punteggio e una classificazione. Si noti che la stringa di ricerca è vuota.

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

Usati insieme, il filtro viene applicato per primo all'intero indice, quindi la ricerca viene eseguita sui risultati del filtro. I filtri quindi possono essere un'utile tecnica per migliorare le prestazioni delle query perché riducono il set di documenti che la query di ricerca deve elaborare.

La sintassi per le espressioni di filtro è un subset del [linguaggio di filtro OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Per le query di ricerca è possibile usare la [sintassi semplificata](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) o la [sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search), illustrate di seguito.


## <a name="choose-a-syntax-simple-or-full"></a>Scegliere una sintassi: semplice o completa

Ricerca di Azure si basa su Apache Lucene e consente di scegliere tra due parser di query per la gestione di query tipiche e specializzate. Le richieste di ricerca tipiche vengono formulate usando la [sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) predefinita. Questa sintassi supporta diversi operatori di ricerca comuni, tra cui AND, OR, NOT, frase, suffisso e operatori di precedenza.

La [sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), che viene abilitata quando si aggiunge **queryType=full** alla richiesta, espone il linguaggio di query espressivo e ampiamente usato sviluppato come parte di [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). L'uso di questa sintassi di query consente di eseguire query specializzate:

+ [query con ambito campo](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [ricerca fuzzy](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [ricerca di prossimità](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [aumento priorità termini](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [ricerca basata su espressioni regolari](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [ricerca con caratteri jolly](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

Gli operatori booleani sono praticamente gli stessi in entrambe le sintassi, con formati aggiuntivi in Lucene completo:

+ [operatori booleani nella sintassi semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [operatori booleani nella sintassi Lucene completa](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>Elementi obbligatori ed elementi facoltativi

Quando si inviano richieste di ricerca in Ricerca di Azure, è possibile specificare una serie di parametri insieme alle parole effettive digitate nella casella di ricerca dell'applicazione. Questi parametri di query consentono di ottenere un controllo più approfondito dell'[esperienza di ricerca full-text](search-lucene-query-architecture.md).

Gli elementi obbligatori in una richiesta di query includono i componenti seguenti:

+ raccolta di documenti indice ed endpoint di servizio, espressi qui come un URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`;
+ versione API (solo REST), espressa come **api-version=**;
+ chiave API query o admin, espressa come **api-key=;**
+ stringa di query espressa come **search=**, che può rimanere non specificata se si desidera eseguire una ricerca vuota. È anche possibile inviare solo un'espressione di filtro come **$filter=**.
+ **queryType=**, semplice o completo, che può essere omesso se si desidera usare la sintassi semplice predefinita.

Tutti gli altri parametri di ricerca sono facoltativi.

## <a name="apis-and-tools-for-testing"></a>API e strumenti di test

La tabella seguente elenca le API e i metodi basati su strumenti per inviare query.

| Metodologia | DESCRIZIONE |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Client che può essere usato per eseguire una query in un indice di Ricerca di Azure.  <br/>[Altre informazioni.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Cerca documenti (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Metodi GET o POST su un indice, usando i parametri di query per un input aggiuntivo.  |
| [Fiddler, Postman o altro strumento di test HTTP](search-fiddler.md) | Spiega come configurare l'intestazione e il corpo della richiesta per l'invio di query a Ricerca di Azure.  |
| [Esplora ricerche nel portale di Azure](search-explorer.md) | Fornisce opzioni e una barra di ricerca per selezioni indice e versione API. I risultati vengono restituiti come documenti JSON. <br/>[Altre informazioni.](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>Gestire i risultati di ricerca

I parametri sulla query possono essere usati per strutturare il set di risultati nei modi seguenti:

+ limitando o inviando in batch il numero di documenti nel risultato (50 per impostazione predefinita);
+ selezionando i campi da includere nei risultati;
+ impostando un tipo di ordinamento;
+ aggiungendo evidenziazioni per attirare l'attenzione sui termini corrispondenti nel corpo dei risultati di ricerca;

### <a name="tips-for-unexpected-results"></a>Suggerimenti per risultati imprevisti

Occasionalmente, la sostanza dei risultati, e non la struttura, è imprevista. Quando i risultati della query non sono quelli previsti, è possibile provare queste modifiche per capire se i risultati migliorano:

+ Cambiare `searchMode=any` (valore predefinito) in `searchMode=all` per richiedere corrispondenze su tutti i criteri e non su alcuni. Questo è particolarmente vero quando gli operatori booleani sono inclusi nella query.

+ Modificare la tecnica di query se l'analisi lessicale o del testo è necessaria, ma il tipo di query preclude l'elaborazione linguistica. Nella ricerca full-text, l'analisi del testo o lessicale corregge automaticamente gli errori di ortografia, il singolare/plurale delle parole e persino i sostantivi o i verbi irregolari. Per alcune query, ad esempio le ricerche fuzzy o con caratteri jolly, l'analisi del testo non fa parte della pipeline di analisi della query. Per alcuni scenari, le espressioni regolari vengono usati come soluzione alternativa. 

### <a name="paging-results"></a>Risultati di paging
Ricerca di Azure rende più facile implementare il paging dei risultati della ricerca. Tramite i parametri `top` e `skip` è possibile eseguire in modo uniforme le richieste di ricerca che consentono di ricevere il set totale di risultati della ricerca in subset gestibili e ordinati in grado di abilitare facilmente ottime procedure di ricerca nell'interfaccia utente. Quando si ricevono questi subset di risultati più piccoli, è anche possibile ottenere il numero di documenti nel set di totale dei risultati della ricerca.

Altre informazioni sul paging dei risultati della ricerca sono disponibili nell'articolo [Come impaginare i risultati della ricerca in Ricerca di Azure](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordinamento dei risultati
Quando si ricevono i risultati di una query di ricerca, è possibile richiedere che Ricerca di Azure presenti i risultati ordinati in base ai valori di un campo specifico. Per impostazione predefinita, Ricerca di Azure ordina i risultati della ricerca in base alle priorità del punteggio di ricerca di ciascun documento, che deriva da [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se si vuole che Ricerca di Azure restituisca i risultati ordinati in base a un valore diverso dal punteggio di ricerca, è possibile usare il parametro di ricerca `orderby` . È possibile specificare il valore del parametro `orderby` per includere i nomi dei campi e le chiamate alla [`geo.distance()` funzione](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) per ottenere valori geospaziali. Ogni espressione può essere seguita da `asc` per indicare che i risultati vengono richiesti in ordine crescente e `desc` per indicare che i risultati vengono richiesti in ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente.


### <a name="hit-highlighting"></a>Evidenziazione dei risultati
In Ricerca di Azure è semplice mettere in evidenza la parte esatta dei risultati della ricerca che corrispondono alla query di ricerca usando i parametri `highlight`, `highlightPreTag` e `highlightPostTag`. È possibile specificare quali campi *ricercabili* devono avere il testo corrispondente evidenziato e specificare anche i tag della stringa esatta da aggiungere all'inizio e alla fine del testo corrispondente restituito da Ricerca di Azure.

## <a name="see-also"></a>Vedere anche 

+ [Come funziona la ricerca full-text in Ricerca di Azure (architettura di analisi delle query)](search-lucene-query-architecture.md)
+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in .NET](search-query-dotnet.md)
+ [Come eseguire query in REST](search-query-rest-api.md)
