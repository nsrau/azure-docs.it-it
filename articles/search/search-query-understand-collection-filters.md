---
title: Informazioni sui filtri di raccolta OData
titleSuffix: Azure Cognitive Search
description: Informazioni sul funzionamento dei filtri di raccolta OData in Azure ricerca cognitiva query.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 9a57e1d16b13d822b6f5b541a7f838b0dd3a69ad
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794385"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Informazioni sui filtri di raccolta OData in Azure ricerca cognitiva

Per [filtrare](query-odata-filter-orderby-syntax.md) i campi di raccolta in ricerca cognitiva di Azure, è possibile usare gli [operatori`any` e `all`](search-query-odata-collection-operators.md) insieme alle **espressioni lambda**. Le espressioni lambda sono espressioni booleane che fanno riferimento a una **variabile di intervallo**. Gli operatori `any` e `all` sono analoghi a un ciclo di `for` nella maggior parte dei linguaggi di programmazione, con la variabile di intervallo che assume il ruolo di variabile del ciclo e l'espressione lambda come corpo del ciclo. La variabile di intervallo accetta il valore "Current" della raccolta durante l'iterazione del ciclo.

Almeno questo è il funzionamento concettuale. In realtà, Azure ricerca cognitiva implementa i filtri in modo molto diverso per il funzionamento dei cicli `for`. Idealmente, questa differenza sarebbe invisibile per l'utente, ma in determinate situazioni non lo è. Il risultato finale è la presenza di regole da seguire per la scrittura di espressioni lambda.

Questo articolo illustra il motivo per cui esistono le regole per i filtri di raccolta esplorando il modo in cui Azure ricerca cognitiva esegue questi filtri. Se si stanno scrivendo filtri avanzati con espressioni lambda complesse, questo articolo può risultare utile per comprendere le possibili caratteristiche dei filtri e il motivo.

Per informazioni sulle regole per i filtri di raccolta, inclusi esempi, vedere [risoluzione dei problemi relativi ai filtri di raccolta OData in Azure ricerca cognitiva](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Perché i filtri della raccolta sono limitati

Esistono tre motivi per cui non tutte le funzionalità di filtro sono supportate per tutti i tipi di raccolte:

1. Solo determinati operatori sono supportati per determinati tipi di dati. Ad esempio, non è sensato confrontare i valori booleani `true` e `false` utilizzando `lt`, `gt`e così via.
1. Azure ricerca cognitiva non supporta la **ricerca correlata** nei campi di tipo `Collection(Edm.ComplexType)`.
1. Azure ricerca cognitiva Usa indici invertiti per eseguire filtri su tutti i tipi di dati, incluse le raccolte.

Il primo motivo è solo una conseguenza del modo in cui vengono definiti il linguaggio OData e il sistema di tipi EDM. Le ultime due sono descritte in modo più dettagliato nella parte restante di questo articolo.

## <a name="correlated-versus-uncorrelated-search"></a>Ricerca correlata rispetto a ricerca non correlata

Quando si applicano più criteri di filtro su una raccolta di oggetti complessi, i criteri sono **correlati** perché si applicano a *ogni oggetto nella raccolta*. Ad esempio, il filtro seguente restituirà Alberghi con almeno una stanza Deluxe con una tariffa inferiore a 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Se l'applicazione di filtri non è *correlata*, il filtro precedente potrebbe restituire gli hotel in cui una stanza è Deluxe e una stanza diversa ha una tariffa di base inferiore a 100. Non avrebbe senso, perché entrambe le clausole dell'espressione lambda si applicano alla stessa variabile di intervallo, ovvero `room`. Questo è il motivo per cui questi filtri sono correlati.

Per la ricerca full-text, tuttavia, non è possibile fare riferimento a una variabile di intervallo specifica. Se si usa la ricerca in campo per emettere una [query Lucene completa](query-lucene-syntax.md) come questa:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

è possibile che si ottenga un hotel in cui una stanza è Deluxe e una stanza diversa indica "City View" nella descrizione. Ad esempio, il documento seguente con `Id` di `1` corrisponderebbe alla query:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Il motivo è che `Rooms/Type` fa riferimento a tutti i termini analizzati del campo `Rooms/Type` nell'intero documento e allo stesso modo per `Rooms/Description`, come illustrato nelle tabelle seguenti.

Modalità di archiviazione `Rooms/Type` per la ricerca full-text:

| Termini in `Rooms/Type` | ID documento |
| --- | --- |
| Deluxe | 1, 2 |
| standard | 1 |

Modalità di archiviazione `Rooms/Description` per la ricerca full-text:

| Termini in `Rooms/Description` | ID documento |
| --- | --- |
| cortile | 2 |
| city | 1 |
| Garden | 1 |
| Grandi dimensioni | 1 |
| motel | 2 |
| camera | 1, 2 |
| standard | 1 |
| Suite | 1 |
| visualizza | 1 |

Quindi, a differenza del filtro riportato sopra, che indica in sostanza che "corrisponde ai documenti in cui una chat room ha `Type` uguale a" Deluxe Room "e **che la stessa stanza** ha `BaseRate` inferiore a 100", la query di ricerca indica che i documenti corrispondono a quelli in cui `Rooms/Type` ha il termine "Deluxe" e `Rooms/Description` ha la frase "City View". Nel secondo caso, non esiste alcun concetto di singole chat con campi che possono essere correlati.

> [!NOTE]
> Se si desidera visualizzare il supporto per la ricerca correlata aggiunta ad Azure ricerca cognitiva, votare [questo elemento vocale utente](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Indici e raccolte invertiti

È possibile che si sia notato un numero molto inferiore di restrizioni sulle espressioni lambda rispetto a raccolte complesse rispetto a quelle per raccolte semplici come `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`e così via. Questo perché Azure ricerca cognitiva archivia raccolte complesse come raccolte effettive di documenti secondari, mentre le raccolte semplici non vengono archiviate come raccolte.

Si consideri, ad esempio, un campo di raccolta di stringhe filtrabile come `seasons` in un indice per un rivenditore online. Alcuni documenti caricati in questo indice possono avere un aspetto simile al seguente:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

I valori del campo `seasons` vengono archiviati in una struttura denominata **indice invertito**, che ha un aspetto simile al seguente:

| Durata | ID documento |
| --- | --- |
| primavera | 1, 2 |
| estate | 1 |
| rientrano | 1, 2 |
| invernali | 2, 3 |

Questa struttura di dati è progettata per rispondere a una domanda con grande velocità: in quali documenti viene visualizzato un determinato termine? La risposta a questa domanda funziona in modo più simile a un controllo di uguaglianza semplice rispetto a un ciclo su una raccolta. Di fatto, questo è il motivo per cui per le raccolte di stringhe, Azure ricerca cognitiva consente `eq` solo come operatore di confronto all'interno di un'espressione lambda per `any`.

Basandosi sull'uguaglianza, verrà ora esaminato come è possibile combinare più controlli di uguaglianza sulla stessa variabile di intervallo con `or`. Funziona grazie all'algebra e [alla proprietà distributiva dei quantificatori](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Espressione seguente:

    seasons/any(s: s eq 'winter' or s eq 'fall')

Equivale a:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

ognuna delle due sottoespressioni `any` può essere eseguita in modo efficiente usando l'indice invertito. Inoltre, grazie alla [legge di negazione dei quantificatori](https://en.wikipedia.org/wiki/Existential_quantification#Negation), questa espressione:

    seasons/all(s: s ne 'winter' and s ne 'fall')

Equivale a:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

per questo motivo è possibile usare `all` con `ne` e `and`.

> [!NOTE]
> Sebbene i dettagli esulino dall'ambito di questo documento, questi stessi principi si estendono ai [test di distanza e intersezione per le raccolte di punti geospaziali](search-query-odata-geo-spatial-functions.md) . Questo è il motivo per cui, in `any`:
>
> - non è possibile negare `geo.intersects`
> - `geo.distance` deve essere confrontato utilizzando `lt` o `le`
> - le espressioni devono essere combinate con `or`, non `and`
>
> Le regole opposte si applicano a `all`.

È consentita una varietà più ampia di espressioni quando si filtrano le raccolte di tipi di dati che supportano gli operatori `lt`, `gt`, `le`e `ge`, ad esempio `Collection(Edm.Int32)`. In particolare, è possibile utilizzare `and` e `or` in `any`, purché le espressioni di confronto sottostanti vengano combinate in **confronti di intervallo** utilizzando `and`, che vengono quindi combinate ulteriormente utilizzando `or`. Questa struttura di espressioni booleane è denominata [disgiuntiva Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), altrimenti nota come "ORS of le". Viceversa, le espressioni lambda per `all` per questi tipi di dati devono essere in [formato congiuntiva normale (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), altrimenti note come "e di ORS". Il ricerca cognitiva di Azure consente confronti di intervalli, perché possono eseguirli usando indici invertiti in modo efficiente, così come è possibile eseguire la ricerca a breve termine per le stringhe.

In breve, di seguito sono riportate le regole generali per gli elementi consentiti in un'espressione lambda:

- All'interno `any`, i *controlli positivi* sono sempre consentiti, ad esempio l'uguaglianza, i confronti di intervallo, `geo.intersects`o `geo.distance` rispetto a `lt` o `le` (si pensi alla "somiglianza" come uguaglianza quando si tratta della distanza di controllo).
- All'interno `any``or` è sempre consentito. È possibile utilizzare `and` solo per i tipi di dati che possono esprimere i controlli di intervallo e solo se si utilizzano ORs di le (DNF).
- All'interno `all`, le regole sono invertite, ma sono consentite solo *verifiche negative* , è possibile usare `and` sempre ed è possibile usare `or` solo per i controlli di intervallo espressi come e di ORS (CNF).

In pratica, questi sono i tipi di filtri che è più probabile usare comunque. È comunque utile comprendere i limiti di ciò che è possibile però.

Per esempi specifici di quali tipi di filtri sono consentiti e quali non sono, vedere [come scrivere filtri di raccolta validi](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Passaggi successivi  

- [Risoluzione dei problemi relativi ai filtri di raccolta OData in Azure ricerca cognitiva](search-query-troubleshoot-collection-filters.md)
- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
