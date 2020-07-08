---
title: Tipi di query e composizione
titleSuffix: Azure Cognitive Search
description: Nozioni di base per la creazione di una query di ricerca in Azure ricerca cognitiva, usando i parametri per filtrare, selezionare e ordinare i risultati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 8f170d541ec314020702ab53606eed4d660cea9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130807"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Tipi di query e composizione in ricerca cognitiva di Azure

In ricerca cognitiva di Azure, una query è una specifica completa di un'operazione di round trip. Nella richiesta sono presenti parametri che forniscono istruzioni di esecuzione per il motore, nonché parametri che formano la risposta in arrivo. Non specificato ( `search=*` ), senza criteri di corrispondenza e utilizzando parametri null o predefiniti, una query viene eseguita su tutti i campi ricercabili come operazione di ricerca full-text, restituendo un set di risultati senza punteggio in ordine arbitrario.

L'esempio seguente è una query rappresentativa costruita nell' [API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Questo esempio è destinato all' [Indice demo degli hotel](search-get-started-portal.md) e include parametri comuni che consentono di ottenere un'idea dell'aspetto di una query.

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

+ **`queryType`** imposta il parser, che è il [parser di query semplice predefinito](search-query-simple-examples.md) (ottimale per la ricerca full-text) o il [parser di query Lucene completo](search-query-lucene-examples.md) usato per i costrutti di query avanzati, come le espressioni regolari, la ricerca di prossimità, la ricerca fuzzy e il carattere jolly, per citarne alcuni.

+ **`search`** fornisce i criteri di corrispondenza, in genere termini interi o frasi, ma spesso accompagnati da operatori booleani. I termini singoli autonomi sono query *termine*. Le query in più parti racchiuse tra virgolette sono query di *frasi* . La ricerca può essere indefinita, ad esempio, **`search=*`** ma senza criteri di corrispondenza, il set di risultati è composto da documenti arbitrariamente selezionati.

+ **`searchFields`** vincola l'esecuzione di query a campi specifici. Qualsiasi campo attribuito come *ricercabile* nello schema dell'indice è un candidato per questo parametro.

Anche le risposte sono deformate dai parametri inclusi nella query:

+ **`select`** Specifica i campi da restituire nella risposta. In un'istruzione SELECT è possibile utilizzare solo i campi contrassegnati come *recuperabili* nell'indice.

+ **`top`** Restituisce il numero specificato di documenti con la migliore corrispondenza. In questo esempio vengono restituiti solo 10 riscontri. Per eseguire la pagina dei risultati, è possibile usare top e Skip (non mostrati).

+ **`count`** indica il numero totale di documenti nell'intero indice corrispondenti, che possono essere maggiori di quelli restituiti. 

+ **`orderby`** viene utilizzato se si desidera ordinare i risultati in base a un valore, ad esempio una classificazione o una località. In caso contrario, per impostazione predefinita viene usato il Punteggio di pertinenza per classificare i risultati.

In ricerca cognitiva di Azure l'esecuzione delle query è sempre rispetto a un indice, autenticato usando una chiave API fornita nella richiesta. In REST, entrambi vengono forniti nelle intestazioni della richiesta.

### <a name="how-to-run-this-query"></a>Come eseguire questa query

Prima di scrivere codice, è possibile usare gli strumenti di query per apprendere la sintassi e sperimentare parametri diversi. L'approccio più rapido è lo strumento del portale integrato, [Esplora ricerche](search-explorer.md).

Se è stata seguita questa [Guida introduttiva per creare l'indice demo degli Alberghi](search-get-started-portal.md), è possibile incollare questa stringa di query nella barra di ricerca di Explorer per eseguire la prima query:`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Modo in cui le operazioni di query sono abilitate dall'indice

La progettazione degli indici e la progettazione delle query sono strettamente associate in Azure ricerca cognitiva. Un aspetto essenziale da conoscere fin dall'inizio è che lo *schema dell'indice*, con gli attributi in ogni campo, determina il tipo di query che è possibile compilare. 

Gli attributi dell'indice in un campo impostano le operazioni consentite - se un campo è *ricercabile* nell'indice *recuperabile* nei risultati *ordinabile*, * filtrabile*e così via. Nella stringa di query di esempio, `"$orderby": "Rating"` funziona solo perché il campo rating è contrassegnato come *ordinabile* nello schema dell'indice. 

![Definizione di indice per l'esempio di Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definizione di indice per l'esempio di Hotel")

Lo screenshot precedente è un elenco parziale degli attributi di indice per l'esempio di Hotel. È possibile visualizzare lo schema dell'intero indice nel portale. Per ulteriori informazioni sugli attributi dell’indice, vedere [Creare indice API REST](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Alcune funzionalità di query sono abilitate a livello di indice anziché in base al campo. Queste funzionalità includono: [mappe sinonimi](search-synonyms.md), [analizzatori personalizzati](index-add-custom-analyzers.md), [costrutti del suggerimento (per il completamento automatico e query suggerite)](index-add-suggesters.md), [logica di assegnazione dei punteggi per i risultati di rango](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementi di una richiesta di query

Le query vengano sempre indirizzate a un singolo indice. Non è possibile unire gli indici o creare strutture di dati personalizzate o temporanee come destinazione della query. 

Gli elementi obbligatori in una richiesta di query includono i componenti seguenti:

+ Raccolta di endpoint di servizio e documenti di indice, espressa come URL contenente componenti fissi e definiti dall'utente:**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(Solo REST) è necessario perché più di una versione dell'API è disponibile in qualsiasi momento. 
+ **`api-key`**, ovvero una query o una chiave API di amministrazione, autentica la richiesta al servizio.
+ **`queryType`**, semplice o completo, che può essere omesso se si usa la sintassi semplice predefinita predefinita.
+ **`search`** o **`filter`** fornisce i criteri di corrispondenza, che possono essere non specificati se si desidera eseguire una ricerca vuota. Entrambi i tipi di query sono descritti in termini di parser semplice, ma anche le query avanzate richiedono il parametro di ricerca per il passaggio di espressioni di query complesse.

Tutti gli altri parametri di ricerca sono facoltativi. Per l'elenco completo degli attributi, vedere [Creare l’indice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Per informazioni dettagliate sul modo in cui i parametri vengono usati durante l'elaborazione, vedere funzionamento [della ricerca full-text in Azure ricerca cognitiva](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Scegliere le API e gli strumenti

La tabella seguente elenca le API e i metodi basati su strumenti per inviare query.

| Metodologia | Descrizione |
|-------------|-------------|
| [Esplora ricerche (portale)](search-explorer.md) | Fornisce opzioni e una barra di ricerca per selezioni indice e versione API. I risultati vengono restituiti come documenti JSON. Consigliato per l'esplorazione, il test e la convalida. <br/>[Altre informazioni.](search-get-started-portal.md#query-index) | 
| [Postazione o altri strumenti REST](search-get-started-postman.md) | Gli strumenti di test Web sono un'ottima scelta per formulare le chiamate REST. L'API REST supporta tutte le operazioni possibili in Azure ricerca cognitiva. Questo articolo illustra come configurare un'intestazione e un corpo di richiesta HTTP per l'invio di richieste ad Azure ricerca cognitiva.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Client che può essere usato per eseguire query su un indice ricerca cognitiva di Azure.  <br/>[Altre informazioni.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Cerca documenti (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Metodi GET o POST su un indice, usando i parametri di query per un input aggiuntivo.  |

## <a name="choose-a-parser-simple--full"></a>Scegliere un parser: semplice | completo

Azure ricerca cognitiva si basa su Apache Lucene e offre una scelta tra due parser di query per la gestione di query tipiche e specializzate. Le richieste che usano il parser semplice vengono formulate tramite il [la sintassi di query semplice](query-simple-syntax.md), selezionata come predefinita per la velocità e l'efficienza nelle query di testo in formato libero. Questa sintassi supporta diversi operatori di ricerca comuni, tra cui AND, OR, NOT, frase, suffisso e operatori di precedenza.

La [sintassi di query Lucene completa](query-Lucene-syntax.md#bkmk_syntax), che viene abilitata quando si aggiunge `queryType=full` alla richiesta, espone il linguaggio di query espressivo e ampiamente usato sviluppato come parte di [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). La Sintassi completa estende la sintassi semplice. Qualsiasi query scritta per la sintassi semplice viene eseguita con il parser di Lucene full. 

Gli esempi seguenti illustrano il punto: eseguire una query, ma con impostazioni queryType diverse, genera risultati diversi. Nella prima query il metodo `^3` after `historic` viene considerato come parte del termine di ricerca. Il risultato in primo piano per questa query è "Marquis Plaza & Suites", che ha l' *oceano* nella descrizione

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

La stessa query che usa il parser Lucene completo interpreta `^3` come un richiamo di termine nel campo. Il passaggio dei parser comporta la modifica del rango, con risultati che contengono il termine spostamento *cronologico* verso l'alto.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipi di query

Azure ricerca cognitiva supporta un'ampia gamma di tipi di query. 

| Tipo di query | Utilizzo | Altre informazioni ed esempi |
|------------|--------|-------------------------------|
| Ricerca di testo in formato libero | Parametro di ricerca ed entrambi i parser| La ricerca full-text scansiona uno o più termini in tutti i campi *ricercabili* dell'indice e funziona come un motore di ricerca, ad esempio Google o Bing. L'esempio nella sezione introduttiva è di ricerca full-text.<br/><br/>La ricerca full-text viene sottoposta ad analisi lessicale usando l'analizzatore Lucene standard (per impostazione predefinita) per ridurre il maiuscolo di tutti i termini, rimuovere parole non significative come "The". È possibile eseguire l'override dell'impostazione predefinita con gli [analizzatori non in lingua inglese](index-add-language-analyzers.md#language-analyzer-list) o con [analizzatori indipendenti dal linguaggio specializzati](index-add-custom-analyzers.md#AnalyzerTable) che modificano l'analisi lessicale. Ad esempio [parola chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) gestisce l'intero contenuto di un campo come un token singolo. Ciò è utile per i dati come i codici postali, gli ID e alcuni nomi di prodotto. | 
| Ricerca filtrata | [Espressione di filtro OData](query-odata-filter-orderby-syntax.md) ed entrambi i parser | Le query filtro valutano un'espressione booleana su tutti i campi *filtrabili* in un indice. Contrariamente alla ricerca, una query filtro corrisponde al contenuto esatto di un campo, inclusa la distinzione tra maiuscole e minuscole nei campi della stringa. Un'altra differenza è che le query filtro vengono espresse nella sintassi di OData. <br/>[Esempio di espressione filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Ricerca geografica | [Tipo Edm.Geographypoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) sul campo, l'espressione filtro ed entrambi i parser | Le coordinate archiviate in un campo con un Edm.geographypoint sono utilizzate per "ricerca nelle vicinanze" o per controlli di ricerca basati su mappa. <br/>[Esempio di ricerca geografica](search-query-simple-examples.md#example-5-geo-search)|
| Ricerca immagini | espressione di filtro e parser semplice | In ricerca cognitiva di Azure, le query di intervallo vengono compilate utilizzando il parametro Filter. <br/>[Esempio di filtro di intervallo](search-query-simple-examples.md#example-4-range-filters) | 
| [Ricerca nel campo](query-lucene-syntax.md#bkmk_fields) | Parametro di ricerca ed parser completo | Compilare un'espressione di query composta destinata a un singolo campo. <br/>[Esempio di ricerca nel campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [Ricerca fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Parametro di ricerca ed parser completo | Corrispondenze in base alle esigenze di ortografia o di costruzione simile. <br/>[Esempio di ricerca fuzzy](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [ricerca per prossimità](query-lucene-syntax.md#bkmk_proximity) | Parametro di ricerca ed parser completo | Trova i termini vicini tra loro in un documento. <br/>[Esempio di ricerca per prossimità](search-query-lucene-examples.md#example-4-proximity-search) |
| [incremento del termine](query-lucene-syntax.md#bkmk_termboost) | Parametro di ricerca ed parser completo | Classifica un documento superiore se contiene il termine con boosting, rispetto alle altre che non lo contengono. <br/>[Esempio di aumento della priorità dei termini](search-query-lucene-examples.md#example-5-term-boosting) |
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

+ Modificare **`searchMode=any`** (impostazione predefinita) in **`searchMode=all`** per richiedere corrispondenze su tutti i criteri anziché su uno dei criteri. Questo è particolarmente vero quando gli operatori booleani sono inclusi nella query.

+ Modificare la tecnica di query se l'analisi lessicale o del testo è necessaria, ma il tipo di query preclude l'elaborazione linguistica. Nella ricerca full-text, il testo o l'analisi lessicale corregge la correzione automatica per gli errori di ortografia, le forme di parola plurale e i verbi irregolari o i sostantivi. Per alcune query, ad esempio fuzzy o la ricerca con caratteri jolly, l'analisi lessicale non fa parte della pipeline di analisi della query. Per alcuni scenari, le espressioni regolari vengono usati come soluzione alternativa. 

### <a name="paging-results"></a>Risultati di paging
Azure ricerca cognitiva semplifica l'implementazione del paging dei risultati della ricerca. Usando i **`top`** parametri e **`skip`** , è possibile eseguire agevolmente le richieste di ricerca che consentono di ricevere il set totale di risultati della ricerca in subset gestibili e ordinati che consentono di eseguire facilmente procedure ottimali per l'interfaccia utente. Quando si ricevono questi subset di risultati più piccoli, è anche possibile ottenere il numero di documenti nel set di totale dei risultati della ricerca.

Per altre informazioni sui risultati della ricerca di paging, vedere l'articolo [come eseguire la pagina dei risultati della ricerca in Azure ricerca cognitiva](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordinamento dei risultati
Quando si ricevono i risultati per una query di ricerca, è possibile richiedere che Azure ricerca cognitiva soddisfi i risultati ordinati in base ai valori in un campo specifico. Per impostazione predefinita, Azure ricerca cognitiva Ordina i risultati della ricerca in base al rango del Punteggio di ricerca di ogni documento, che è derivato da [tf-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se si vuole che Azure ricerca cognitiva restituisca i risultati ordinati in base a un valore diverso dal punteggio di ricerca, è possibile usare il **`orderby`** parametro search. È possibile specificare il valore del **`orderby`** parametro per includere i nomi dei campi e le chiamate alla [** `geo.distance()` funzione**](query-odata-filter-orderby-syntax.md) per i valori geospaziali. Ogni espressione può essere seguita da `asc` per indicare che i risultati vengono richiesti in ordine crescente e **`desc`** per indicare che i risultati vengono richiesti in ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente.


### <a name="hit-highlighting"></a>Evidenziazione dei risultati
In ricerca cognitiva di Azure, l'accento sulla parte esatta dei risultati della ricerca che corrispondono alla query di ricerca è semplificato usando i **`highlight`** **`highlightPreTag`** parametri, e **`highlightPostTag`** . È possibile specificare i campi *ricercabili* con il testo corrispondente evidenziato, nonché specificare i tag di stringa esatti da accodare all'inizio e alla fine del testo corrispondente restituito da Azure ricerca cognitiva.

## <a name="see-also"></a>Vedere anche

+ [Funzionamento della ricerca full-text in Azure ricerca cognitiva (architettura di analisi delle query)](search-lucene-query-architecture.md)
+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in .NET](search-query-dotnet.md)
+ [Come eseguire query in REST](search-create-index-rest-api.md)
