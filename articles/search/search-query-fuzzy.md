---
title: Ricerca fuzzy
titleSuffix: Azure Cognitive Search
description: Implementare un'esperienza di ricerca "vuoi dire" per correggere automaticamente un termine o un errore di battitura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262435"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Ricerca fuzzy per correggere errori di ortografia e errori di battitura

Ricerca cognitiva di Azure supporta la ricerca fuzzy, un tipo di query che compensa gli errori di battitura e i termini con errori di ortografia nella stringa di input. Lo fa scansionando termini con una composizione simile. Espandere la ricerca per coprire le corrispondenze vicine ha l'effetto di correggere automaticamente un errore di battitura quando la discrepanza è solo di pochi caratteri smarriti. 

## <a name="what-is-fuzzy-search"></a>Che cos'è la ricerca fuzzy?

È un esercizio di espansione che produce una corrispondenza a termini con una composizione simile. Quando viene specificata una ricerca fuzzy, il motore crea un grafico (basato sulla [teoria deterministica degli automi finiti)](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)di termini composti in modo simile, per tutti i termini interi nella query. Ad esempio, se la query include tre termini "università di Washington", `search=university~ of~ washington~` viene creato un grafico per ogni termine nella query (non è presente alcuna rimozione di parole stop nella ricerca fuzzy, quindi "of" ottiene un grafico).

Il grafico è costituito da un massimo di 50 espansioni, o permutazioni, di ogni termine, che catturano sia le varianti corrette che le varianti errate nel processo. Il motore restituisce quindi le corrispondenze più rilevanti nella risposta. 

Per un termine come "università", il grafico potrebbe avere "imprevedibile, universty, università, universo, inverso". Tutti i documenti che corrispondono a quelli nel grafico sono inclusi nei risultati. A differenza di altre query che analizzano il testo per gestire diverse forme della stessa parola ("topi" e "mouse"), i confronti in una query fuzzy vengono eseguiti al valore nominale senza alcuna analisi linguistica sul testo. "Universo" e "inverso", che sono semanticamente diversi, corrisponderanno perché le discrepanze sintattiche sono piccole.

Una corrispondenza ha esito positivo se le discrepanze sono limitate a due o meno modifiche, in cui una modifica è un carattere inserito, eliminato, sostituito o trasposto. L'algoritmo di correzione delle stringhe che specifica il differenziale è la metrica di [distanza Damerau-Levenshtein,](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) descritta come "il numero minimo di operazioni (inserimenti, eliminazioni, sostituzioni o trasposizioni di due caratteri adiacenti) necessarie per modificare una parola nell'altra". 

Ricerca cognitiva di Azure:In Azure Cognitive Search:

+ La query fuzzy si applica a termini interi, ma è possibile supportare frasi tramite costruzioni AND. Ad esempio, "Unviersty" di "Wshington" corrisponderebbe all'Università di Washington.

+ La distanza di default di una modifica è 2. Un valore `~0` di indica nessuna espansione (solo il termine esatto `~1` è considerato una corrispondenza), ma è possibile specificare un grado di differenza o una modifica. 

+ Una query fuzzy può espandere un termine fino a 50 permutazioni aggiuntive. Questo limite non è configurabile, ma è possibile ridurre in modo efficace il numero di espansioni riducendo la distanza di modifica a 1.

+ Le risposte consistono in documenti contenenti una corrispondenza pertinente (fino a 50).

Collettivamente, i grafici vengono inviati come criteri di corrispondenza rispetto ai token nell'indice. Come si può immaginare, la ricerca fuzzy è intrinsecamente più lenta rispetto ad altri moduli di query. Le dimensioni e la complessità dell'indice possono determinare se i vantaggi sono sufficienti per compensare la latenza della risposta.

> [!NOTE]
> Poiché la ricerca fuzzy tende ad essere lenta, potrebbe essere utile esaminare alternative come l'indicizzazione di ngrammi, con la sua progressione di brevi sequenze di caratteri (due e tre sequenze di caratteri per gettoni bigram e trigramma). A seconda della lingua e della superficie di query, n-gram potrebbe offrire prestazioni migliori. Il compromesso è che l'indicizzazione n-gram è molto di archiviazione intensiva e genera indici molto più grandi.
>
> Un'altra alternativa, che si potrebbe considerare se si desidera gestire solo i casi più eclatanti, sarebbe una [mappa sinonimi](search-synonyms.md). Ad esempio, mappando "ricerca" a "serach, serch, sarch" o "retrieve" a "retreive".

## <a name="indexing-for-fuzzy-search"></a>Indicizzazione per la ricerca fuzzy

Gli analizzatori non vengono utilizzati durante l'elaborazione delle query per creare un grafico di espansione, ma ciò non significa che gli analizzatori devono essere ignorati in scenari di ricerca fuzzy. Dopo tutto, gli analizzatori vengono utilizzati durante l'indicizzazione per creare token rispetto ai quali viene eseguita la corrispondenza, se la query è in formato libero, ricerca filtrata o una ricerca fuzzy con un grafico come input. 

In genere, quando si assegnano analizzatori in base al campo, la decisione di ottimizzare la catena di analisi si basa sul caso d'uso primario (un filtro o una ricerca full-text) anziché su moduli di query specializzati come la ricerca fuzzy. Per questo motivo, non esiste una raccomandazione analizzatore specifica per la ricerca fuzzy. 

Tuttavia, se le query di test non producono le corrispondenze previste, è possibile provare a variare l'analizzatore di indicizzazione, impostandolo su un [analizzatore del linguaggio,](index-add-language-analyzers.md)per verificare se si ottengono risultati migliori. Alcune lingue, in particolare quelle con mutazioni vocaliche, possono beneficiare dell'inflessione e delle forme di parola irregolari generate dai processori del linguaggio naturale Microsoft. In alcuni casi, l'utilizzo dell'analizzatore di linguaggio corretto può fare la differenza se un termine è tokenizzato in modo compatibile con il valore fornito dall'utente.

## <a name="how-to-use-fuzzy-search"></a>Come utilizzare la ricerca fuzzy

Le query fuzzy vengono costruite utilizzando la sintassi di query Lucene completa, richiamando il parser di [query Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Impostare il parser Lucene`queryType=full`completo sulla query ( ).

1. Facoltativamente, definire l'ambito della richiesta`searchFields=<field1,field2>`per campi specifici, utilizzando questo parametro ( ). 

1. Aggiungere l'operatore`~`tilde ( ) alla`search=<string>~`fine dell'intero termine ( ).

   Includere un parametro facoltativo, un numero compreso tra 0 e`~1`2 (impostazione predefinita), se si desidera specificare la distanza di modifica ( ). Ad esempio, "blue~" o "blue~1" restituirà "blue", "blues" e "glue".

In Ricerca cognitiva di Azure, oltre al termine e alla distanza (massimo 2), non sono disponibili parametri aggiuntivi da impostare nella query.

> [!NOTE]
> Durante l'elaborazione delle query, le query fuzzy non vengono sottoposte a [analisi lessicale.](search-lucene-query-architecture.md#stage-2-lexical-analysis) L'input della query viene aggiunto direttamente all'albero delle query ed espanso per creare un grafico dei termini. L'unica trasformazione eseguita è l'uso di maiuscole e minuscole.

## <a name="testing-fuzzy-search"></a>Test della ricerca fuzzy

Per test semplici, è consigliabile [eseguire Esplora ricerche](search-explorer.md) o [Postman](search-get-started-postman.md) per l'iterazione su un'espressione di query. Entrambi gli strumenti sono interattivi, il che significa che è possibile passare rapidamente attraverso più varianti di un termine e valutare le risposte che tornano.

Quando i risultati sono ambigui, [l'evidenziazione dei risultati](search-pagination-page-layout.md#hit-highlighting) può aiutarti a identificare la corrispondenza nella risposta. 

> [!Note]
> L'uso dell'evidenziazione dei risultati per identificare le corrispondenze fuzzy ha limitazioni e funziona solo per la ricerca fuzzy di base. Se l'indice dispone di profili di punteggio o se si sovrappone la query con una sintassi aggiuntiva, l'evidenziazione dei risultati potrebbe non riuscire a identificare la corrispondenza. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Esempio 1: ricerca fuzzy con il termine esatto

Si supponga che la `"Description"` stringa seguente esista in un campo in un documento di ricerca:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Inizia con una ricerca fuzzy su "special" e aggiungi l'evidenziazione dei risultati al campo Descrizione:

    search=special~&highlight=Description

Nella risposta, poiché è stata aggiunta l'evidenziazione dei risultati, la formattazione viene applicata a "speciale" come termine corrispondente.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Riprovare la richiesta, digitando erroneamente "speciale" estraendo diverse lettere ("pe"):

    search=scial~&highlight=Description

Finora, nessuna modifica alla risposta. Utilizzando il valore predefinito di 2 gradi di distanza, la rimozione di due caratteri "pe" da "speciale" consente comunque una corrispondenza di successo su quel termine.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Provando un'altra richiesta, modificare ulteriormente il termine di ricerca eliminando un ultimo carattere per un totale di tre eliminazioni (da "speciale" a "scal"):

    search=scal~&highlight=Description

Si noti che viene restituita la stessa risposta, ma ora invece di corrispondere su "special", la corrispondenza fuzzy è su "SQL".

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

Il punto di questo esempio espanso è quello di illustrare la chiarezza che l'evidenziazione dei risultati può portare a risultati ambigui. In tutti i casi, viene restituito lo stesso documento. Se ti fossi affidato gli ID documento per verificare una corrispondenza, potresti aver perso il passaggio da "speciale" a "SQL".

## <a name="see-also"></a>Vedere anche

+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure (architettura di analisi delle query)How full text search works in Azure Cognitive Search (query parsing architecture)](search-lucene-query-architecture.md)
+ [Esplora ricerche](search-explorer.md)
+ [Come eseguire query in .NET](search-query-dotnet.md)
+ [Come eseguire query in REST](search-create-index-rest-api.md)
