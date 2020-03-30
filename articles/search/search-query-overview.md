---
title: Tipi di query e composizione
titleSuffix: Azure Cognitive Search
description: Nozioni di base per la creazione di una query di ricerca in Ricerca cognitiva di Azure, usando i parametri per filtrare, selezionare e ordinare i risultati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282822"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Query types and composition in Azure Cognitive Search

In Ricerca cognitiva di Azure una query è una specifica completa di un'operazione di andata e ritorno. I parametri nella richiesta forniscono criteri di corrispondenza per la ricerca di documenti in un indice, quali campi includere o escludere, le istruzioni di esecuzione passate al motore e le direttive per modellare la risposta. Non specificato`search=*`( ), una query viene eseguita su tutti i campi ricercabili come operazione di ricerca full-text, restituendo un set di risultati senza punteggio in ordine arbitrario.

L'esempio seguente è una query rappresentativa costruita [nell'API REST.](https://docs.microsoft.com/rest/api/searchservice/search-documents) Questo esempio è destinato [all'indice demo hotels](search-get-started-portal.md) e include parametri comuni.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** imposta il parser, che è il parser di [query semplice predefinito](search-query-simple-examples.md) (ottimale per la ricerca full-text) o il parser di query [Lucene completo](search-query-lucene-examples.md) utilizzato per costrutti di query avanzati come espressioni regolari, ricerca di prossimità, fuzzy e ricerca con caratteri jolly, per citarne alcuni.

+ **`search`** fornisce i criteri di corrispondenza, di solito testo ma spesso accompagnato da operatori booleani. I termini singoli autonomi sono query *termine*. Le query in più parti racchiuse tra virgolette sono query *frasi chiave*. La ricerca può essere **`search=*`** definita, come in , ma è più probabile che sia costituita da termini, frasi e operatori simili a quelli dell'esempio.

+ **`searchFields`** vincola l'esecuzione delle query a campi specifici. Qualsiasi campo attribuito come *ricercabile* nello schema dell'indice è un candidato per questo parametro.

Le risposte sono anche delineate dai parametri da includere nella query. Nell'esempio, il set di risultati **`select`** è costituito da campi elencati nell'istruzione. In un'istruzione $select è possibile utilizzare solo i campi contrassegnati come *recuperabili.* Inoltre, solo **`top`** i 10 risultati vengono restituiti **`count`** in questa query, mentre indica quanti documenti corrispondono complessivo, che può essere maggiore di quello restituito. In questa query, le righe vengono ordinate in base alla classificazione in ordine decrescente.

In Ricerca cognitiva di Azure l'esecuzione delle query viene sempre eseguita in base a un indice, autenticato usando una chiave API fornita nella richiesta. In REST, entrambi vengono forniti nelle intestazioni della richiesta.

### <a name="how-to-run-this-query"></a>Come eseguire questa query

Per eseguire questa query, utilizzare [Esplora ricerche e l'indice demo hotels](search-get-started-portal.md). 

È possibile incollare questa stringa di query nella barra di ricerca della finestra di esplorazione: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Modo in cui le operazioni di query sono abilitate dall'indice

La progettazione dell'indice e la progettazione delle query sono strettamente accoppiate in Ricerca cognitiva di Azure.Index design and query design are tightly coupled in Azure Cognitive Search. Un aspetto essenziale da conoscere fin dall'inizio è che lo *schema dell'indice*, con gli attributi in ogni campo, determina il tipo di query che è possibile compilare. 

Gli attributi dell'indice in un campo impostano le operazioni consentite - se un campo è *ricercabile* nell'indice *recuperabile* nei risultati *ordinabile*, * filtrabile*e così via. Nella stringa di `"$orderby": "Rating"` query di esempio, funziona solo perché il campo Rating è contrassegnato come *ordinabile* nello schema dell'indice. 

![Definizione dell'indice per l'esempio di hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definizione dell'indice per l'esempio di hotel")

La schermata precedente è un elenco parziale di attributi di indice per l'esempio hotels. È possibile visualizzare lo schema dell'intero indice nel portale. Per ulteriori informazioni sugli attributi dell’indice, vedere [Creare indice API REST](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Alcune funzionalità di query sono abilitate a livello di indice anziché in base al campo. Queste funzionalità includono: [mappe dei sinonimi](search-synonyms.md), [analizzatori personalizzati](index-add-custom-analyzers.md), [costrutti di suggerimento (per il completamento automatico e le query suggerite),](index-add-suggesters.md)la logica di [punteggio per la classificazione](index-add-scoring-profiles.md)dei risultati.

## <a name="elements-of-a-query-request"></a>Elementi di una richiesta di query

Le query vengano sempre indirizzate a un singolo indice. Non è possibile unire gli indici o creare strutture di dati personalizzate o temporanee come destinazione della query. 

Gli elementi obbligatori in una richiesta di query includono i componenti seguenti:

+ Endpoint del servizio e raccolta di documenti di indice, espressi come URL contenenti componenti fissi e definiti dall'utente:Service endpoint and index documents collection, expressed as a URL containing fixed and user-defined components:**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(solo REST) è necessario perché più di una versione dell'API è sempre disponibile. 
+ **`api-key`**, una query o una chiave API admin, autentica la richiesta al servizio.
+ **`queryType`**, semplice o completo, che può essere omesso se si utilizza la sintassi semplice predefinita predefinita incorporata.
+ **`search`** o **`filter`** fornisce i criteri di corrispondenza, che possono essere non specificati se si desidera eseguire una ricerca vuota. Entrambi i tipi di query sono descritti in termini di parser semplice, ma anche le query avanzate richiedono il parametro di ricerca per il passaggio di espressioni di query complesse.

Tutti gli altri parametri di ricerca sono facoltativi. Per l'elenco completo degli attributi, vedere [Creare l’indice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Per informazioni più da vicino su come vengono usati i parametri durante l'elaborazione, vedere Funzionamento della [ricerca full-text in Ricerca cognitiva](search-lucene-query-architecture.md)di Azure.For a closer look at how parameters are used during processing, see How full-text search works in Azure Cognitive Search .

## <a name="choose-apis-and-tools"></a>Scegliere API e strumenti

La tabella seguente elenca le API e i metodi basati su strumenti per inviare query.

| Metodologia | Descrizione |
|-------------|-------------|
| [Esplora ricerche (portale)](search-explorer.md) | Fornisce opzioni e una barra di ricerca per selezioni indice e versione API. I risultati vengono restituiti come documenti JSON. Consigliato per l'esplorazione, il test e la convalida. <br/>[Scopri di più.](search-get-started-portal.md#query-index) | 
| [Postino o altri strumenti REST](search-get-started-postman.md) | Gli strumenti di test Web sono un'ottima scelta per formulare le chiamate REST. L'API REST supporta tutte le possibili operazioni in Ricerca cognitiva di Azure.The REST API supports every possible operation in Azure Cognitive Search. In this article, learn how to set up an HTTP request header and body for sending requests to Azure Cognitive Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Client che può essere usato per eseguire query su un indice di Ricerca cognitiva di Azure.Client that can be used to query an Azure Cognitive Search index.  <br/>[Scopri di più.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Cerca documenti (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Metodi GET o POST su un indice, usando i parametri di query per un input aggiuntivo.  |

## <a name="choose-a-parser-simple--full"></a>Scegliere un parser: semplice | completo

Ricerca cognitiva di Azure si trova sopra Apache Lucene e offre la possibilità di scegliere tra due parser di query per la gestione di query tipiche e specializzate. Le richieste che usano il parser semplice vengono formulate tramite il [la sintassi di query semplice](query-simple-syntax.md), selezionata come predefinita per la velocità e l'efficienza nelle query di testo in formato libero. Questa sintassi supporta diversi operatori di ricerca comuni, tra cui AND, OR, NOT, frase, suffisso e operatori di precedenza.

La [sintassi di query Lucene completa](query-Lucene-syntax.md#bkmk_syntax), che viene abilitata quando si aggiunge `queryType=full` alla richiesta, espone il linguaggio di query espressivo e ampiamente usato sviluppato come parte di [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). La Sintassi completa estende la sintassi semplice. Qualsiasi query scritta per la sintassi semplice viene eseguita con il parser di Lucene full. 

Gli esempi seguenti illustrano il punto: eseguire una query, ma con impostazioni queryType diverse, genera risultati diversi. Nella prima query, `^3` `historic` il valore dopo viene considerato come parte del termine di ricerca. Il risultato più nella classifica di questa query è "Marquis Plaza & Suites", che ha *l'oceano* nella sua descrizione

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

La stessa query che utilizza il `^3` parser Lucene completo interpreta come un termine di termini nel campo booster. Il passaggio da un parser all'altro cambia il rango, con i risultati che contengono il termine *storico* che si sposta verso l'alto.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipi di query

Ricerca cognitiva di Azure supporta un'ampia gamma di tipi di query. 

| Tipo di query | Uso | Altre informazioni ed esempi |
|------------|--------|-------------------------------|
| Ricerca di testo in formato libero | Parametro di ricerca ed entrambi i parser| La ricerca full-text scansiona uno o più termini in tutti i campi *ricercabili* dell'indice e funziona come un motore di ricerca, ad esempio Google o Bing. L'esempio nella sezione introduttiva è di ricerca full-text.<br/><br/>La ricerca full-text viene sottoposta ad analisi del testo usando l'analizzatore Lucene standard (per impostazione predefinita) per rendere tutti i termini minuscoli, rimuovere parole non significative, ad esempio "il". È possibile sostituire l'impostazione predefinita con [analizzatori di lingua diversa dall'inglese](index-add-language-analyzers.md#language-analyzer-list) oppure [analizzatori specializzati indipendenti dalla lingua](index-add-custom-analyzers.md#AnalyzerTable) che modificano l'analisi del testo. Ad esempio [parola chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) gestisce l'intero contenuto di un campo come un token singolo. Ciò è utile per i dati come i codici postali, gli ID e alcuni nomi di prodotto. | 
| Ricerca filtrata | [Espressione di filtro OData](query-odata-filter-orderby-syntax.md) ed entrambi i parser | Le query filtro valutano un'espressione booleana su tutti i campi *filtrabili* in un indice. Contrariamente alla ricerca, una query filtro corrisponde al contenuto esatto di un campo, inclusa la distinzione tra maiuscole e minuscole nei campi della stringa. Un'altra differenza è che le query filtro vengono espresse nella sintassi di OData. <br/>[Esempio di espressione filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Ricerca geografica | [Tipo Edm.Geographypoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) sul campo, l'espressione filtro ed entrambi i parser | Le coordinate archiviate in un campo con un Edm.geographypoint sono utilizzate per "ricerca nelle vicinanze" o per controlli di ricerca basati su mappa. <br/>[Esempio di ricerca geografica](search-query-simple-examples.md#example-5-geo-search)|
| Ricerca immagini | espressione di filtro e parser semplice | In Ricerca cognitiva di Azure le query di intervallo vengono create usando il parametro di filtro. <br/>[Esempio di filtro di intervallo](search-query-simple-examples.md#example-4-range-filters) | 
| [Ricerca su campo](query-lucene-syntax.md#bkmk_fields) | Parametro di ricerca ed parser completo | Compilare un'espressione di query composta destinata a un singolo campo. <br/>[Esempio di ricerca in campi](search-query-lucene-examples.md#example-2-fielded-search) |
| [ricerca fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Parametro di ricerca ed parser completo | Corrispondenze in base alle esigenze di ortografia o di costruzione simile. <br/>[Esempio di ricerca fuzzy](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [ricerca per prossimità](query-lucene-syntax.md#bkmk_proximity) | Parametro di ricerca ed parser completo | Trova i termini vicini tra loro in un documento. <br/>[Esempio di ricerca per prossimità](search-query-lucene-examples.md#example-4-proximity-search) |
| [termine boosting](query-lucene-syntax.md#bkmk_termboost) | Parametro di ricerca ed parser completo | Classifica un documento superiore se contiene il termine con boosting, rispetto alle altre che non lo contengono. <br/>[Esempio di aumento della priorità dei termini](search-query-lucene-examples.md#example-5-term-boosting) |
| [ricerca di espressioni regolari](query-lucene-syntax.md#bkmk_regex) | Parametro di ricerca ed parser completo | Corrispondenze in base al contenuto di un'espressione regolare. <br/>[Esempio di espressione regolare](search-query-lucene-examples.md#example-6-regex) |
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

+ Modificare **`searchMode=any`** (impostazione **`searchMode=all`** predefinita) per richiedere corrispondenze su tutti i criteri anziché su uno qualsiasi dei criteri. Questo è particolarmente vero quando gli operatori booleani sono inclusi nella query.

+ Modificare la tecnica di query se l'analisi lessicale o del testo è necessaria, ma il tipo di query preclude l'elaborazione linguistica. Nella ricerca full-text, il testo o l'analisi lessicale si correggono automaticamente per errori di ortografia, forme di parole singolari-plurali e persino verbi o sostantivi irregolari. Per alcune query, ad esempio le ricerche fuzzy o con caratteri jolly, l'analisi del testo non fa parte della pipeline di analisi della query. Per alcuni scenari, le espressioni regolari vengono usati come soluzione alternativa. 

### <a name="paging-results"></a>Risultati di paging
Ricerca cognitiva di Azure semplifica l'implementazione del paging dei risultati della ricerca. Utilizzando i **`top`** **`skip`** parametri e , è possibile inviare senza problemi richieste di ricerca che consentono di ricevere il set totale di risultati di ricerca in sottoinsiemi gestibili e ordinati che consentono facilmente buone procedure dell'interfaccia utente di ricerca. Quando si ricevono questi subset di risultati più piccoli, è anche possibile ottenere il numero di documenti nel set di totale dei risultati della ricerca.

Per altre informazioni sul paging dei risultati della ricerca, vedere l'articolo [Come paginare i risultati della ricerca in Ricerca cognitiva](search-pagination-page-layout.md)di Azure .You can learn more about paging search results in the article How to page search results in Azure Cognitive Search .

### <a name="ordering-results"></a>Ordinamento dei risultati
Quando si ricevono risultati per una query di ricerca, è possibile richiedere che Ricerca cognitiva di Azure fornisca i risultati ordinati in base ai valori in un campo specifico. Per impostazione predefinita, Ricerca cognitiva di Azure ordina i risultati della ricerca in base alla classificazione del punteggio di ricerca di ogni documento, derivato da [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se si vuole che Ricerca cognitiva di Azure restituisca i risultati **`orderby`** ordinati in base a un valore diverso dal punteggio di ricerca, è possibile usare il parametro di ricerca. È possibile specificare **`orderby`** il valore del parametro per includere i nomi dei campi e le chiamate alla [** `geo.distance()` funzione**](query-odata-filter-orderby-syntax.md) per i valori geospaziali. Ogni espressione può `asc` essere seguita da per indicare che **`desc`** i risultati vengono richiesti in ordine crescente e per indicare che i risultati vengono richiesti in ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente.


### <a name="hit-highlighting"></a>Evidenziazione dei risultati
In Ricerca cognitiva di Azure l'analisi della parte esatta dei risultati della **`highlight`** **`highlightPreTag`** ricerca **`highlightPostTag`** che corrispondono alla query di ricerca è semplificata tramite i parametri , e . È possibile specificare i campi ricercabili a cui devono essere enfatizzati il testo corrispondente, nonché specificare i tag di stringa esatti da aggiungere all'inizio e alla fine del testo corrispondente restituito da Ricerca cognitiva di Azure.You can specify which *searchable* fields should have their matched text emphasizeed, as well specifying the exact string tags to append to the start and end of the matched text that Azure Cognitive Search returns.

## <a name="see-also"></a>Vedere anche

+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure (architettura di analisi delle query)How full text search works in Azure Cognitive Search (query parsing architecture)](search-lucene-query-architecture.md)
+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in .NET](search-query-dotnet.md)
+ [Come eseguire query in REST](search-create-index-rest-api.md)
