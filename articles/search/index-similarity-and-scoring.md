---
title: Panoramica della somiglianza e del Punteggio
titleSuffix: Azure Cognitive Search
description: Vengono illustrati i concetti di somiglianza e assegnazione dei punteggi e le operazioni che uno sviluppatore può eseguire per personalizzare il risultato del punteggio.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 4b02039c86f43e6bebed58dfff475816f09a3da1
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890134"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Somiglianza e assegnazione dei punteggi in Azure ricerca cognitiva

Il Punteggio fa riferimento al calcolo di un punteggio di ricerca per ogni elemento restituito nei risultati della ricerca per le query di ricerca full-text. Il punteggio è un indicatore della rilevanza di un elemento nel contesto dell'operazione di ricerca attuale. Maggiore è il punteggio, più rilevante sarà l'elemento. Nei risultati della ricerca gli elementi vengono classificati dal maggiore al minore, in base al punteggio di ricerca calcolato per ogni elemento. 

Per impostazione predefinita, nella risposta vengono restituiti i primi 50, ma è possibile usare il parametro **$Top** per restituire un numero minore o maggiore di elementi (fino a 1000 in una singola risposta) e **$Skip** per ottenere il set successivo di risultati.

Un punteggio di ricerca viene calcolato in base alle proprietà statistiche dei dati e della query. Azure ricerca cognitiva trova i documenti corrispondenti ai termini di ricerca (alcuni o tutti, in base a [searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)), privilegiando i documenti che contengono molte istanze del termine di ricerca. Il Punteggio di ricerca aumenta ancora di più se il termine è raro nell'indice dati, ma comune all'interno del documento. La base per questo approccio alla rilevanza di calcolo è nota come *tf-IDF o* frequenza dei termini, frequenza inversa del documento.

I valori dei punteggi di ricerca possono essere ripetuti in un set di risultati. Quando più riscontri hanno lo stesso punteggio di ricerca, l'ordine degli stessi elementi con punteggio non è definito e non è stabile. Eseguire di nuovo la query e potrebbe essere visualizzata la posizione di spostamento degli elementi, soprattutto se si usa il servizio gratuito o un servizio fatturabile con più repliche. Se due elementi hanno punteggio identico, non vi è alcuna garanzia su quale elemento verrà visualizzato per primo.

Se si vuole suddividere il legame tra i punteggi ripetuti, è possibile aggiungere una clausola **$OrderBy** al primo ordine per punteggio, quindi ordinare in base a un altro campo ordinabile (ad esempio, `$orderby=search.score() desc,Rating desc`). Per ulteriori informazioni, vedere [$OrderBy](https://docs.microsoft.com/azure/search/search-query-odata-orderby).

> [!NOTE]
> `@search.score = 1.00` Indica un set di risultati senza punteggio o non classificato. Il Punteggio è uniforme in tutti i risultati. I risultati senza punteggio si verificano quando il modulo di query è ricerca fuzzy, query con caratteri jolly o Regex o un'espressione **$Filter** . 

## <a name="scoring-profiles"></a>Profili di punteggio

È possibile personalizzare la modalità di classificazione dei diversi campi definendo un *profilo di Punteggio*personalizzato. I profili di punteggio offrono maggiore controllo sulla classificazione degli elementi nei risultati della ricerca. Ad esempio, è possibile aumentare la priorità degli elementi in base al rispettivo potenziale di profitto, alzare di livello elementi più recenti o evidenziare elementi che sono rimasti troppo a lungo in magazzino. 

Un profilo di punteggio fa parte della definizione dell'indice, costituita da campi ponderati, funzioni e parametri. Per ulteriori informazioni sulla definizione di un, vedere [profili di Punteggio](index-add-scoring-profiles.md).

## <a name="scoring-statistics"></a>Statistiche di Punteggio

Per la scalabilità, Azure ricerca cognitiva distribuisce ogni indice orizzontalmente tramite un processo di partizionamento orizzontale, il che significa che le parti di un indice sono fisicamente separate.

Per impostazione predefinita, il Punteggio di un documento viene calcolato in base alle proprietà statistiche dei dati *all'interno di una partizione*. Questo approccio non è in genere un problema per un corpus di dati di grandi dimensioni e offre prestazioni migliori rispetto alla necessità di calcolare il punteggio in base alle informazioni su tutte le partizioni. Ciò premesso, l'uso di questa ottimizzazione delle prestazioni può causare due documenti molto simili (o persino documenti identici) per finire con punteggi di pertinenza diversi se si concludono in partizioni diverse.

Se si preferisce calcolare il punteggio in base alle proprietà statistiche in tutte le partizioni, è possibile aggiungere *scoringStatistics = Global* come [parametro di query](https://docs.microsoft.com/rest/api/searchservice/search-documents) o aggiungere *"scoringStatistics": "Global"* come parametro body della [richiesta di query](https://docs.microsoft.com/rest/api/searchservice/search-documents).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> Per il `scoringStatistics` parametro è necessaria una chiave API di amministrazione.

## <a name="similarity-ranking-algorithms"></a>Algoritmi di classificazione di somiglianza

Azure ricerca cognitiva supporta due algoritmi di classificazione di somiglianza diversi: un algoritmo di *somiglianza classico* e l'implementazione ufficiale dell'algoritmo *OKAPI BM25* (attualmente in anteprima). L'algoritmo di somiglianza classica è l'algoritmo predefinito, ma a partire dal 15 luglio tutti i nuovi servizi creati dopo tale data utilizzeranno il nuovo algoritmo BM25. Sarà l'unico algoritmo disponibile nei nuovi servizi.

Per il momento, è possibile specificare quale algoritmo di classificazione di somiglianza si vuole usare. Per ulteriori informazioni, vedere [algoritmo di classificazione](index-ranking-similarity.md).

Il seguente segmento video si avvicina rapidamente a una spiegazione degli algoritmi di classificazione usati in Azure ricerca cognitiva. È possibile guardare il video completo per informazioni più dettagliate.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="see-also"></a>Vedere anche

 [Scoring Profiles](index-add-scoring-profiles.md) [Riferimento all'API REST](https://docs.microsoft.com/rest/api/searchservice/) per i profili di Punteggio   
 [API di ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Azure ricerca cognitiva .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
