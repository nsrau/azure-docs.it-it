---
title: Panoramica di somiglianza e punteggio
titleSuffix: Azure Cognitive Search
description: Vengono illustrati i concetti di somiglianza e punteggio e vengono descritte le operazioni che uno sviluppatore può eseguire per personalizzare il risultato del punteggio.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 5b3df38e8feef2a7b9bbc090e11a669164010f32
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213202"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Somiglianza e punteggio in Ricerca cognitiva di Azure

Il punteggio fa riferimento al calcolo di un punteggio di ricerca per ogni elemento restituito nei risultati della ricerca delle query di ricerca full-text. Il punteggio è un indicatore della rilevanza di un elemento nel contesto dell'operazione di ricerca attuale. Maggiore è il punteggio, più rilevante sarà l'elemento. Nei risultati della ricerca gli elementi vengono classificati dal maggiore al minore, in base al punteggio di ricerca calcolato per ogni elemento. 

Per impostazione predefinita, vengono restituiti i primi 50 elementi nella risposta, ma è possibile usare il parametro **$top** per restituire un numero minore o maggiore di elementi, fino a un massimo di 1000 elementi in una singola risposta, e il parametro **$skip** per ottenere il set di risultati successivo.

Un punteggio di ricerca viene calcolato in base alle proprietà statistiche dei dati e della query. Ricerca cognitiva di Azure trova documenti che corrispondono ai termini di ricerca (alcuni o tutti, in base a [searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)), preferendo i documenti che includono molte istanze del termine di ricerca. Il punteggio di ricerca aumenta ancora di più se il termine risulta raro nell'indice di dati, ma comune all'interno del documento. La base di questo approccio al calcolo della rilevanza è nota come *TF-IDF* (Term Frequency-Inverse Document Frequency).

I valori dei punteggi di ricerca possono essere ripetuti in un set di risultati. Quando più occorrenze hanno lo stesso punteggio di ricerca, l'ordine degli stessi elementi con punteggio non è definito e non è quindi stabile. Eseguire di nuovo la query. È possibile che la posizione degli elementi cambi, soprattutto se si usa il servizio gratuito o un servizio fatturabile con più repliche. Se due elementi hanno punteggio identico, non vi è alcuna garanzia su quale elemento verrà visualizzato per primo.

Se si desidera interrompere il legame tra i punteggi ripetuti, è possibile aggiungere una clausola **$orderby** al primo ordine per punteggio, quindi eseguire l'ordinamento in base a un altro campo ordinabile, ad esempio `$orderby=search.score() desc,Rating desc`. Per altre informazioni, vedere [$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby).

> [!NOTE]
> Un elemento `@search.score = 1.00` indica un set di risultati senza punteggio o non classificato. Il punteggio è uniforme in tutti i risultati. I risultati senza punteggio si verificano in caso di una query di ricerca fuzzy, con caratteri jolly, di espressione regolare o espressione **$filter**. 

## <a name="scoring-profiles"></a>Profili di punteggio

È possibile personalizzare il modo in cui vengono classificati i diversi campi definendo un *profilo di punteggio* personalizzato. I profili di punteggio offrono maggiore controllo sulla classificazione degli elementi nei risultati della ricerca. Ad esempio, è possibile aumentare la priorità degli elementi in base al rispettivo potenziale di profitto, alzare di livello elementi più recenti o evidenziare elementi che sono rimasti troppo a lungo in magazzino. 

Un profilo di punteggio fa parte della definizione dell'indice, costituita da campi ponderati, funzioni e parametri. Per altre informazioni sulla definizione, vedere [Profili di punteggio](index-add-scoring-profiles.md).

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>Statistiche di assegnazione dei punteggi e sessioni permanenti

Ai fini della scalabilità, Ricerca cognitiva di Azure distribuisce ogni indice orizzontalmente tramite un processo di partizionamento orizzontale, il che significa che le parti di un indice sono fisicamente separate.

Per impostazione predefinita, il punteggio di un documento viene calcolato in base alle proprietà statistiche dei dati *all'interno di una partizione*. Questo approccio in genere non rappresenta un problema per un set di dati di grandi dimensioni e offre prestazioni migliori rispetto ai casi in cui è necessario calcolare il punteggio in base alle informazioni su tutte le partizioni. Ciò premesso, usando questa ottimizzazione delle prestazioni è possibile che due documenti molto simili (o addirittura identici) abbiano punteggi della rilevanza diversi qualora si trovino in partizioni diverse.

Se si preferisce calcolare il punteggio in base alle proprietà statistiche in tutte le partizioni, è possibile aggiungere *scoringStatistics=global* come [parametro di query](https://docs.microsoft.com/rest/api/searchservice/search-documents) oppure aggiungere *"scoringStatistics": "global"* come parametro relativo al corpo della [richiesta di query](https://docs.microsoft.com/rest/api/searchservice/search-documents).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
L'uso di scoringStatistics garantirà che tutte le partizioni nella stessa replica restituiscano gli stessi risultati. Detto questo, le varie repliche possono essere leggermente diverse l'una dall'altra perché vengono sempre aggiornate in base alle ultime modifiche apportate all'indice. In alcuni scenari potrebbe essere necessario che gli utenti ottengano risultati più coerenti durante una "sessione di query". In tal caso, è possibile fornire un elemento `sessionId` come parte delle query. Tale elemento `sessionId` è una stringa univoca creata per fare riferimento a una sessione utente univoca.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
Purché venga usato lo stesso elemento `sessionId`, viene eseguito un tentativo di ricerca per la stessa replica, aumentando la coerenza dei risultati che verranno visualizzati dagli utenti. 

> [!NOTE]
> Il riutilizzo ripetuto degli stessi valori `sessionId` può interferire con il bilanciamento del carico delle richieste tra le repliche e influire negativamente sulle prestazioni del servizio di ricerca. Il valore usato come sessionId non può iniziare con un carattere '_'.

## <a name="similarity-ranking-algorithms"></a>Algoritmi di classificazione di somiglianza

Ricerca cognitiva di Azure supporta due algoritmi di classificazione di somiglianza diversi: Un algoritmo di *somiglianza classica* e l'implementazione ufficiale dell'algoritmo *Okapi BM25* (attualmente in anteprima). L'algoritmo di somiglianza classica è l'algoritmo predefinito, ma a partire dal 15 luglio tutti i nuovi servizi creati dopo tale data useranno il nuovo algoritmo BM25. Sarà l'unico algoritmo disponibile nei nuovi servizi.

Per il momento, è possibile specificare quale algoritmo di classificazione di somiglianza si vuole usare. Per altre informazioni, vedere [Algoritmi di classificazione](index-ranking-similarity.md).

Il frammento di video seguente offre una rapida spiegazione degli algoritmi di classificazione usati in Ricerca cognitiva di Azure. È possibile guardare il video completo per informazioni più dettagliate.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>parametro featuresMode (anteprima)

Le richieste dei [documenti di ricerca](https://docs.microsoft.com/rest/api/searchservice/preview-api/search-documents) hanno un nuovo parametro [featuresMode](https://docs.microsoft.com/rest/api/searchservice/preview-api/search-documents#featuresmode) che può fornire dettagli aggiuntivi sulla pertinenza a livello di campo. Mentre `@searchScore` viene calcolato per tutto il documento (quanto pertinente è questo documento nel contesto di questa query), tramite featuresMode è possibile ottenere informazioni sui singoli campi, come espresso in una `@search.features` struttura. La struttura contiene tutti i campi utilizzati nella query, ovvero campi specifici tramite **searchFields** in una query o tutti i campi attribuiti come **ricercabili** in un indice. Per ogni campo si ottengono i valori seguenti:

+ Numero di token univoci trovati nel campo
+ Punteggio di somiglianza o misura della somiglianza del contenuto del campo rispetto al termine della query
+ Frequenza dei termini oppure numero di volte in cui il termine della query è stato trovato nel campo

Per una query destinata ai campi "Description" e "title", una risposta che include `@search.features` potrebbe essere simile alla seguente:

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

È possibile utilizzare questi punti dati nelle soluzioni di assegnazione dei [punteggi personalizzate](https://github.com/Azure-Samples/search-ranking-tutorial) oppure utilizzare le informazioni per eseguire il debug dei problemi di rilevanza della ricerca.


## <a name="see-also"></a>Vedere anche

 [Profili di punteggio](index-add-scoring-profiles.md) [Riferimento all'API REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [API di ricerca de documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [.NET SDK di Ricerca cognitiva di Azure](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
