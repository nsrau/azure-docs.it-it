---
title: Ricerca fuzzy
titleSuffix: Azure Cognitive Search
description: Implementare un'esperienza di ricerca "si intendeva" per correggere automaticamente un termine o un errore di ortografia.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: faa98f1c52cfe2dd0e19f085f4d33dedb6f01851
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934889"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Ricerca fuzzy per correggere errori di ortografia e digitazioni

Azure ricerca cognitiva supporta la ricerca fuzzy, un tipo di query che compensa gli errori di digitazione e i termini digitati in maniera non corretta nella stringa di input. Questa operazione viene eseguita analizzando i termini con una composizione simile. Espandendo la ricerca per coprire quasi le corrispondenze si verifica l'effetto della correzione automatica di un errore di digitazione quando la discrepanza è solo un numero ridotto di caratteri. 

## <a name="what-is-fuzzy-search"></a>Che cos'è la ricerca fuzzy?

Si tratta di un esercizio di espansione che produce una corrispondenza nei termini con una composizione simile. Quando si specifica una ricerca fuzzy, il motore compila un grafo (basato sulla [teoria di automa finito deterministico](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) di termini composti in modo analogo, per tutti i termini completi della query. Se, ad esempio, la query include tre termini "University of Washington", viene creato un grafo per ogni termine nella query `search=university~ of~ washington~` (non è presente alcuna rimozione di parole non significative nella ricerca fuzzy, quindi "of" ottiene un grafo).

Il grafico è costituito da un massimo di 50 di espansioni, o permutazioni, di ogni termine, acquisendo le varianti corrette e non corrette nel processo. Il motore restituisce quindi le corrispondenze più rilevanti nella risposta. 

Per un termine come "University", il grafo potrebbe avere "unversty, Universty, University, Universe, inverse". Tutti i documenti che corrispondono a quelli nel grafico sono inclusi nei risultati. Diversamente dalle altre query che analizzano il testo per gestire forme diverse della stessa parola ("topi" e "mouse"), i confronti in una query fuzzy vengono eseguiti in corrispondenza del valore del volto senza alcuna analisi linguistica sul testo. "Universe" e "inverse", che sono semanticamente diversi, corrisponderanno perché le discrepanze sintattiche sono ridotte.

Una corrispondenza ha esito positivo se le discrepanze sono limitate a due o meno modifiche, in cui una modifica è un carattere inserito, eliminato, sostituito o trasposto. L'algoritmo di correzione di stringa che specifica il differenziale è la metrica della [distanza Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) , descritta come il numero minimo di operazioni (inserimenti, eliminazioni, sostituzioni o trasposizioni di due caratteri adiacenti) necessarie per modificare una parola nell'altra ". 

In ricerca cognitiva di Azure:

+ La query fuzzy si applica a termini interi, ma è possibile supportare frasi tramite e costruzioni. Ad esempio, "Unviersty ~ di ~" wSHINGTON ~ "corrisponderà a" University of Washington ".

+ La distanza predefinita di una modifica è 2. Il valore `~0` indica nessuna espansione (solo il termine esatto viene considerato una corrispondenza), ma è possibile specificare `~1` per un grado di differenza o una modifica. 

+ Una query fuzzy può espandere un termine fino a 50 permutazioni aggiuntive. Questo limite non è configurabile, ma è possibile ridurre efficacemente il numero di espansioni diminuendo la distanza di modifica a 1.

+ Le risposte sono costituite da documenti contenenti una corrispondenza pertinente (fino a 50).

Collettivamente, i grafici vengono inviati come criteri di corrispondenza per i token nell'indice. Come si può immaginare, la ricerca fuzzy è intrinsecamente più lenta rispetto ad altri moduli di query. Le dimensioni e la complessità dell'indice possono determinare se i vantaggi sono sufficienti per compensare la latenza della risposta.

> [!NOTE]
> Poiché la ricerca fuzzy tende a essere lenta, potrebbe essere utile esaminare le alternative, ad esempio l'indicizzazione n-Gram, con la progressione di sequenze di caratteri brevi (due e tre sequenze di caratteri per i token bigramma e trigramma). A seconda della lingua e della superficie di query, n-Gram potrebbe offrire prestazioni migliori. Il compromesso è che l'indicizzazione di n-grammi è molto complessa e genera indici molto più grandi.
>
> Un'altra alternativa, che è possibile considerare se si desidera gestire solo i casi più eclatanti, sarebbe una [mappa di sinonimi](search-synonyms.md). Ad esempio, il mapping di "Search" a "Serach, Serch, sArch" o "retrieve" a "recuperare".

## <a name="indexing-for-fuzzy-search"></a>Indicizzazione per ricerca fuzzy

Gli analizzatori non vengono utilizzati durante l'elaborazione di query per creare un grafico di espansione, ma ciò non significa che gli analizzatori debbano essere ignorati negli scenari di ricerca fuzzy. Dopo tutto, gli analizzatori vengono usati durante l'indicizzazione per creare token in base ai quali viene eseguita la corrispondenza, se la query è in formato libero, ricerca filtrata o ricerca fuzzy con un grafo come input. 

In genere, quando si assegnano analizzatori in base ai singoli campi, la decisione di ottimizzare la catena di analisi si basa sul caso di utilizzo principale (un filtro o una ricerca full-text) invece che su moduli di query specializzati come la ricerca fuzzy. Per questo motivo, non esiste una raccomandazione di analizzatore specifica per la ricerca fuzzy. 

Tuttavia, se le query di test non producono le corrispondenze attese, è possibile provare a variare Indexing Analyzer, impostandolo su un [analizzatore del linguaggio](index-add-language-analyzers.md), per verificare se si ottengono risultati migliori. Alcuni linguaggi, in particolare quelli con mutazioni vocali, possono trarre vantaggio dalla flessione e dalle forme di Word irregolari generate dai processori di linguaggio naturale Microsoft. In alcuni casi, l'utilizzo dell'analizzatore del linguaggio appropriato può determinare una differenza se un termine viene suddiviso in token in modo che sia compatibile con il valore fornito dall'utente.

## <a name="how-to-use-fuzzy-search"></a>Come utilizzare la ricerca fuzzy

Le query fuzzy vengono create usando la sintassi di query Lucene completa, richiamando il [parser di query Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Impostare il parser Lucene completo nella query ( `queryType=full` ).

1. Facoltativamente, definire l'ambito della richiesta a campi specifici utilizzando questo parametro ( `searchFields=<field1,field2>` ). 

1. Aggiungere l'operatore tilde ( `~` ) alla fine del termine intero ( `search=<string>~` ).

   Includere un parametro facoltativo, un numero compreso tra 0 e 2 (impostazione predefinita) se si desidera specificare la distanza di modifica ( `~1` ). Ad esempio, "blue~" o "blue~1" restituirà "blue", "blues" e "glue".

In ricerca cognitiva di Azure, oltre al termine e alla distanza (massimo 2), non sono presenti parametri aggiuntivi da impostare per la query.

> [!NOTE]
> Durante l'elaborazione delle query, le query fuzzy non vengono sottoposte ad [analisi lessicali](search-lucene-query-architecture.md#stage-2-lexical-analysis). L'input della query viene aggiunto direttamente alla struttura della query ed espanso per creare un grafico dei termini. L'unica trasformazione eseguita è la combinazione di maiuscole e minuscole.

## <a name="testing-fuzzy-search"></a>Test della ricerca fuzzy

Per i test semplici, è consigliabile usare [Esplora ricerche](search-explorer.md) [o l'](search-get-started-postman.md) utente per l'iterazione in un'espressione di query. Entrambi gli strumenti sono interattivi, il che significa che è possibile scorrere rapidamente più varianti di un termine e valutare le risposte che vengono restituite.

Quando i risultati sono ambigui, l' [evidenziazione dei riscontri](search-pagination-page-layout.md#hit-highlighting) consente di identificare la corrispondenza nella risposta. 

> [!Note]
> L'uso dell'evidenziazione dei riscontri per identificare le corrispondenze fuzzy presenta alcune limitazioni e funziona solo per la ricerca fuzzy di base. Se l'indice contiene profili di punteggio o se si esegue il layer della query con una sintassi aggiuntiva, l'evidenziazione dei risultati potrebbe non riuscire a identificare la corrispondenza. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Esempio 1: ricerca fuzzy con il termine esatto

Si supponga che la stringa seguente sia presente in un `"Description"` campo di un documento di ricerca: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Iniziare con una ricerca fuzzy su "Special" e aggiungere l'evidenziazione dei risultati al campo Description:

```console
search=special~&highlight=Description
```

Nella risposta, poiché è stata aggiunta l'evidenziazione dei riscontri, la formattazione viene applicata a "speciale" come termine corrispondente.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Riprovare la richiesta, eseguendo un errore di ortografia "speciale" prendendo in mano diverse lettere ("PE"):

```console
search=scial~&highlight=Description
```

Fino a questo punto, non è stata apportata alcuna modifica alla risposta. Se si usa il valore predefinito di 2 gradi di distanza, la rimozione di due caratteri "PE" da "speciale" consente ancora una corrispondenza corretta per quel periodo.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Se si tenta di eseguire un'altra richiesta, modificare ulteriormente il termine di ricerca prendendo un ultimo carattere per un totale di tre eliminazioni (da "speciale" a "SCA"):

```console
search=scal~&highlight=Description
```

Si noti che viene restituita la stessa risposta, ma ora invece della corrispondenza con "Special", la corrispondenza fuzzy si trova in "SQL".

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

Il punto di questo esempio espanso è illustrare la chiarezza che l'evidenziazione dei riscontri può portare a risultati ambigui. In tutti i casi, viene restituito lo stesso documento. Se si è fatto affidamento sugli ID documento per verificare una corrispondenza, è possibile che si sia perso il passaggio da "speciale" a "SQL".

## <a name="see-also"></a>Vedere anche

+ [Funzionamento della ricerca full-text in Azure ricerca cognitiva (architettura di analisi delle query)](search-lucene-query-architecture.md)
+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in .NET](./search-get-started-dotnet.md)
+ [Come eseguire query in REST](./search-get-started-powershell.md)