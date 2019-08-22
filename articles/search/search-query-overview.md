---
title: Tipi di query e composizione - Ricerca di Azure
description: Concetti fondamentali per creare una query di ricerca in Ricerca di Azure, usando parametri per filtrare, selezionare e ordinare risultati.
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec2018
ms.openlocfilehash: 30c3b233a1454d04fb281e049376b2b3aafe1879
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647962"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Come comporre una query in Ricerca di Azure

In Ricerca di Azure, una query è una specifica completa di un'operazione di andata e ritorno. I parametri nella richiesta forniscono i criteri di corrispondenza per la ricerca di documenti in un indice, le istruzioni di esecuzione per il motore e le direttive per la forma della risposta. 

Una richiesta di query è un costrutto avanzato che specifica quali campi sono inclusi nell'ambito, come eseguire una ricerca, quali campi restituire, se ordinare o filtrare e così via. Se non viene specificata, una query viene eseguita rispetto a tutti i campi disponibili per la ricerca come un'operazione di ricerca full-text che restituisce un set di risultati senza punteggio in un ordine arbitrario.

## <a name="apis-and-tools-for-testing"></a>API e strumenti di test

La tabella seguente elenca le API e i metodi basati su strumenti per inviare query.

| Metodologia | Descrizione |
|-------------|-------------|
| [Esplora ricerche (portale)](search-explorer.md) | Fornisce opzioni e una barra di ricerca per selezioni indice e versione API. I risultati vengono restituiti come documenti JSON. <br/>[Altre informazioni.](search-get-started-portal.md#query-index) | 
| [Postman o Fiddler](search-get-started-postman.md) | Gli strumenti di test Web sono un'ottima scelta per formulare le chiamate REST. L'API REST supporta tutte le operazioni possibili in Ricerca di Azure. In questo articolo viene spiegato come configurare l'intestazione e il corpo di una richiesta HTTP per l'invio di richieste a Ricerca di Azure.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Client che può essere usato per eseguire una query in un indice di Ricerca di Azure.  <br/>[Altre informazioni.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Cerca documenti (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Metodi GET o POST su un indice, usando i parametri di query per un input aggiuntivo.  |

## <a name="a-first-look-at-query-requests"></a>Una prima occhiata alle richieste di query

Gli esempi sono utili per introdurre nuovi concetti. Come una query rappresentativa costruita nell’[API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents), questo esempio fa riferiemnto all’[indice demo immobiliare](search-get-started-portal.md) e include i parametri comuni.

```
{
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"",
    "searchFields": "description, city",
    "count": "true",
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "daysOnMarket"
}
```

+ **`queryType`** imposta il parser, che in Ricerca di Azure può essere il [parser predefinito di query semplice](search-query-simple-examples.md) (ottimale per la ricerca full-text), o il [parser di query Lucene completo](search-query-lucene-examples.md) usato per i costrutti di query avanzate, come le espressioni regolari, ricerca per prossimità, fuzzy e ricerca con caratteri jolly, per citarne alcuni.

+ **`search`** fornisce la corrispondenza ai criteri, in genere testo ma spesso accompagnato da operatori booleani. I termini singoli autonomi sono query *termine*. Le query in più parti racchiuse tra virgolette sono query *frasi chiave*. La ricerca può essere non definita, come in **`search=*`** , ma è più probabile che sia costituita da termini, frasi e operatori simili a quanto visualizzato nell'esempio.

+ **`searchFields`** è facoltativo, usato per limitare l'esecuzione di query a campi specifici.

Le risposte sono anche delineate dai parametri da includere nella query. Nell'esempio, il set di risultati è costituito da campi elencati nell’istruzione **`select`** . In questa query, vengono restituiti solo i primi 10 risultati ma **`count`** indica il numero di documenti corrispondenti complessivi. In questa query le righe vengono ordinate per daysOnMarket.

Nella Ricerca di Azure, l'esecuzione della query avviene sempre rispetto a un indice e l'autenticazione viene eseguita con una chiave API fornita nella richiesta. In REST, entrambi vengono forniti nelle intestazioni della richiesta.

### <a name="how-to-run-this-query"></a>Come eseguire questa query

Per eseguire questa query, utilizzare [Cerca explorer e l'indice di demo immobiliare](search-get-started-portal.md). 

È possibile incollare questa stringa di query nella barra di ricerca della finestra di esplorazione: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Modo in cui le operazioni di query sono abilitate dall'indice

La progettazione di indici e query di progettazione sono strettamente collegati in Ricerca di Azure. Un aspetto essenziale da conoscere fin dall'inizio è che lo *schema dell'indice*, con gli attributi in ogni campo, determina il tipo di query che è possibile compilare. 

Gli attributi dell'indice in un campo impostano le operazioni consentite - se un campo è *ricercabile* nell'indice *recuperabile* nei risultati *ordinabile*,  *filtrabile*e così via. Nella stringa di query di esempio, `"$orderby": "daysOnMarket"` funziona solo perché il campo daysOnMarket è contrassegnato come *ordinabile* nello schema dell'indice. 

![Indicizzare la definizione per l'esempio immobiliare](./media/search-query-overview/realestate-sample-index-definition.png "Indicizzare la definizione per l'esempio immobiliare")

Lo screenshot precedente è un elenco parziale degli attributi di indice per l'esempio di mercato immobiliare. È possibile visualizzare lo schema dell'intero indice nel portale. Per ulteriori informazioni sugli attributi dell’indice, vedere [Creare indice API REST](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Alcune funzionalità di query sono abilitate a livello di indice anziché in base al campo. Queste funzionalità includono: [mappe sinonimi](search-synonyms.md), [analizzatori personalizzati](index-add-custom-analyzers.md), [costrutti del suggerimento (per il completamento automatico e query suggerite)](index-add-suggesters.md), [logica di assegnazione dei punteggi per i risultati di rango](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementi di una richiesta di query

Le query vengano sempre indirizzate a un singolo indice. Non è possibile unire gli indici o creare strutture di dati personalizzate o temporanee come destinazione della query. 

Gli elementi obbligatori in una richiesta di query includono i componenti seguenti:

+ Servizio endpoint e la raccolta di documenti di indice, espresso come un URL che contiene i componenti fissi e definito dall'utente: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Solo REST) è necessario perché più di una versione dell'API è disponibile in qualsiasi momento. 
+ **`api-key`** , una query o una chiave api amministratore, autentica la richiesta al servizio.
+ **`queryType`** , semplice o completo, che può essere omesso se si desidera usare la sintassi semplice predefinita.
+ **`search`** o **`filter`** fornisce il criterio di corrispondenza che può non essere specificato se si desidera eseguire una ricerca vuota. Entrambi i tipi di query sono descritti in termini di parser semplice, ma anche le query avanzate richiedono il parametro di ricerca per il passaggio di espressioni di query complesse.

Tutti gli altri parametri di ricerca sono facoltativi. Per l'elenco completo degli attributi, vedere [Creare l’indice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Per informazioni dettagliate sul modo in cui i parametri vengono utilizzati durante l'elaborazione, vedere [come funziona la ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Scegliere un parser: semplice | completo

Ricerca di Azure si basa su Apache Lucene e consente di scegliere tra due parser di query per la gestione di query tipiche e specializzate. Le richieste che usano il parser semplice vengono formulate tramite il [la sintassi di query semplice](query-simple-syntax.md), selezionata come predefinita per la velocità e l'efficienza nelle query di testo in formato libero. Questa sintassi supporta diversi operatori di ricerca comuni, tra cui AND, OR, NOT, frase, suffisso e operatori di precedenza.

La [sintassi di query Lucene completa](query-Lucene-syntax.md#bkmk_syntax), che viene abilitata quando si aggiunge `queryType=full` alla richiesta, espone il linguaggio di query espressivo e ampiamente usato sviluppato come parte di [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). La Sintassi completa estende la sintassi semplice. Qualsiasi query scritta per la sintassi semplice viene eseguita con il parser di Lucene full. 

Gli esempi seguenti illustrano il punto: eseguire una query, ma con impostazioni queryType diverse, genera risultati diversi. Nella prima query, `^3` viene considerato come parte del termine di ricerca.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

La stessa query che utilizza il parser di Lucene completo interpreta l'aumento di priorità nel campo su "ramo", che consente di migliorare la classificazione delle ricerche di risultati che contengono tale termine specifico.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipi di query

La Ricerca di Azure supporta un'ampia gamma di tipi di query. 

| Tipo di query | Utilizzo | Altre informazioni ed esempi |
|------------|--------|-------------------------------|
| Ricerca di testo in formato libero | Parametro di ricerca ed entrambi i parser| La ricerca full-text scansiona uno o più termini in tutti i campi *ricercabili* dell'indice e funziona come un motore di ricerca, ad esempio Google o Bing. L'esempio nella sezione introduttiva è di ricerca full-text.<br/><br/>La ricerca full-text viene sottoposta ad analisi del testo usando l'analizzatore Lucene standard (per impostazione predefinita) per rendere tutti i termini minuscoli, rimuovere parole non significative, ad esempio "il". È possibile sostituire l'impostazione predefinita con [analizzatori di lingua diversa dall'inglese](index-add-language-analyzers.md#language-analyzer-list) oppure [analizzatori specializzati indipendenti dalla lingua](index-add-custom-analyzers.md#AnalyzerTable) che modificano l'analisi del testo. Ad esempio [parola chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) gestisce l'intero contenuto di un campo come un token singolo. Ciò è utile per i dati come i codici postali, gli ID e alcuni nomi di prodotto. | 
| Ricerca filtrata | [Espressione di filtro OData](query-odata-filter-orderby-syntax.md) ed entrambi i parser | Le query filtro valutano un'espressione booleana su tutti i campi *filtrabili* in un indice. Contrariamente alla ricerca, una query filtro corrisponde al contenuto esatto di un campo, inclusa la distinzione tra maiuscole e minuscole nei campi della stringa. Un'altra differenza è che le query filtro vengono espresse nella sintassi di OData. <br/>[Esempio di espressione filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Ricerca geografica | [Tipo Edm.Geographypoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) sul campo, l'espressione filtro ed entrambi i parser | Le coordinate archiviate in un campo con un Edm.geographypoint sono utilizzate per "ricerca nelle vicinanze" o per controlli di ricerca basati su mappa. <br/>[Esempio di ricerca geografica](search-query-simple-examples.md#example-5-geo-search)|
| Ricerca immagini | espressione di filtro e parser semplice | In Ricerca di Azure, le query di intervallo vengono create usando il parametro di filtro. <br/>[Esempio di filtro di intervallo](search-query-simple-examples.md#example-4-range-filters) | 
| [Ricerca nel campo](query-lucene-syntax.md#bkmk_fields) | Parametro di ricerca ed parser completo | Compilare un'espressione di query composta destinata a un singolo campo. <br/>[Esempio di ricerca nel campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [ricerca fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Parametro di ricerca ed parser completo | Corrispondenze in base alle esigenze di ortografia o di costruzione simile. <br/>[Esempio di ricerca fuzzy](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [ricerca di prossimità](query-lucene-syntax.md#bkmk_proximity) | Parametro di ricerca ed parser completo | Trova i termini vicini tra loro in un documento. <br/>[Esempio di ricerca per prossimità](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento priorità termini](query-lucene-syntax.md#bkmk_termboost) | Parametro di ricerca ed parser completo | Classifica un documento superiore se contiene il termine con boosting, rispetto alle altre che non lo contengono. <br/>[Esempio di aumento della priorità dei termini](search-query-lucene-examples.md#example-5-term-boosting) |
| [ricerca basata su espressioni regolari](query-lucene-syntax.md#bkmk_regex) | Parametro di ricerca ed parser completo | Corrispondenze in base al contenuto di un'espressione regolare. <br/>[Esempio di espressione regolare](search-query-lucene-examples.md#example-6-regex) |
|  [ricerca con caratteri jolly o prefisso](query-lucene-syntax.md#bkmk_wildcard) | Parametro di ricerca ed parser completo | Corrispondenze basate su un prefisso e una tilde (`~`) o un singolo carattere (`?`). <br/>[Esempio di ricerca con caratteri jolly](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Gestire i risultati di ricerca 

I risultati della query vengono trasmessi come documenti JSON nell'API REST. Se si utilizzano le API .NET la serializzazione è integrata. È possibile plasmare i risultati impostando parametri per la query, selezionando campi specifici per il risultato.

I parametri sulla query possono essere usati per strutturare il set di risultati nei modi seguenti:

+ limitando o inviando in batch il numero di documenti nel risultato (50 per impostazione predefinita);
+ selezionando i campi da includere nei risultati;
+ impostando un tipo di ordinamento;
+ aggiungendo evidenziazioni per attirare l'attenzione sui termini corrispondenti nel corpo dei risultati di ricerca;

### <a name="tips-for-unexpected-results"></a>Suggerimenti per risultati imprevisti

Occasionalmente, la sostanza dei risultati, e non la struttura, è imprevista. Quando i risultati della query non sono quelli previsti, è possibile provare queste modifiche per capire se i risultati migliorano:

+ Cambiare **`searchMode=any`** (predefinito) in **`searchMode=all`** per richiedere corrispondenze su tutti i criteri e non su alcuni. Questo è particolarmente vero quando gli operatori booleani sono inclusi nella query.

+ Modificare la tecnica di query se l'analisi lessicale o del testo è necessaria, ma il tipo di query preclude l'elaborazione linguistica. Nella ricerca full-text, il testo o l'analisi lessicale corregge la correzione automatica per gli errori di ortografia, le forme di parola plurale e i verbi irregolari o i sostantivi. Per alcune query, ad esempio le ricerche fuzzy o con caratteri jolly, l'analisi del testo non fa parte della pipeline di analisi della query. Per alcuni scenari, le espressioni regolari vengono usati come soluzione alternativa. 

### <a name="paging-results"></a>Risultati di paging
Ricerca di Azure rende più facile implementare il paging dei risultati della ricerca. Tramite i parametri **`top`** e **`skip`** è possibile eseguire in modo uniforme le richieste di ricerca che consentono di ricevere il set totale di risultati della ricerca in subset gestibili e ordinati in grado di abilitare facilmente ottime procedure di ricerca nell'interfaccia utente. Quando si ricevono questi subset di risultati più piccoli, è anche possibile ottenere il numero di documenti nel set di totale dei risultati della ricerca.

Altre informazioni sul paging dei risultati della ricerca sono disponibili nell'articolo [Come impaginare i risultati della ricerca in Ricerca di Azure](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordinamento dei risultati
Quando si ricevono i risultati di una query di ricerca, è possibile richiedere che Ricerca di Azure presenti i risultati ordinati in base ai valori di un campo specifico. Per impostazione predefinita, Ricerca di Azure ordina i risultati della ricerca in base alle priorità del punteggio di ricerca di ciascun documento, che deriva da [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se si vuole che Ricerca di Azure restituisca i risultati ordinati in base a un valore diverso dal punteggio di ricerca, è possibile usare il parametro di ricerca **`orderby`** . È possibile specificare il valore del parametro **`orderby`** per includere i nomi dei campi e le chiamate alla [ **`geo.distance()` funzione**](query-odata-filter-orderby-syntax.md) per ottenere valori geospaziali. Ogni espressione può essere seguita da `asc` per indicare che i risultati vengono richiesti in ordine crescente e **`desc`** per indicare che i risultati vengono richiesti in ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente.


### <a name="hit-highlighting"></a>Evidenziazione dei risultati
In Ricerca di Azure è semplice mettere in evidenza la parte esatta dei risultati della ricerca che corrispondono alla query di ricerca usando i parametri **`highlight`** , **`highlightPreTag`** , e **`highlightPostTag`** . È possibile specificare quali campi *ricercabili* devono avere il testo corrispondente evidenziato e specificare anche i tag della stringa esatta da aggiungere all'inizio e alla fine del testo corrispondente restituito da Ricerca di Azure.

## <a name="see-also"></a>Vedere anche

+ [Come funziona la ricerca full-text in Ricerca di Azure (architettura di analisi delle query)](search-lucene-query-architecture.md)
+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in .NET](search-query-dotnet.md)
+ [Come eseguire query in REST](search-create-index-rest-api.md)
