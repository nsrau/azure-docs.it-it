---
title: Riferimento agli operatori logici di OData - ricerca di Azure
description: Gli operatori logici OData e, in alternativa e non, nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079769"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Gli operatori logici di OData in ricerca di Azure - `and`, `or`, `not`

[Le espressioni di filtro OData](query-odata-filter-orderby-syntax.md) in ricerca di Azure sono espressioni booleane che restituiscono `true` o `false`. È possibile scrivere un filtro complesso mediante la scrittura di una serie di [filtri più semplici](search-query-odata-comparison-operators.md) , componendole usando gli operatori logici dalla [algebra booleana](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Un operatore binario che restituisce `true` se entrambi relativi a sinistra e destra le sottoespressioni vengono valutati `true`.
- `or`: Un operatore binario che restituisce `true` se una delle relative sottoespressioni di sinistra o destra restituisce `true`.
- `not`: Un operatore unario che restituisce `true` se viene valutata la sottoespressione `false`e viceversa.

Questi elementi, insieme con il [gli operatori di raccolta `any` e `all` ](search-query-odata-collection-operators.md), consentono di costruire i filtri che è possono esprimere i criteri di ricerca molto complessa.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione di OData che utilizza gli operatori logici.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

Esistono due forme di espressioni logiche: binario (`and`/`or`), in cui sono presenti due sottoespressioni e unari (`not`), in cui è presente una sola. Le sottoespressioni possono essere espressioni booleane di qualsiasi tipo:

- Variabili di campi o l'intervallo di tipo `Edm.Boolean`
- Funzioni che restituiscono valori di tipo `Edm.Boolean`, ad esempio `geo.intersects` o `search.ismatch`
- [Espressioni di confronto](search-query-odata-comparison-operators.md), ad esempio `rating gt 4`
- [Espressioni di raccolta](search-query-odata-collection-operators.md), ad esempio `Rooms/any(room: room/Type eq 'Deluxe Room')`
- I valori letterali booleani `true` o `false`.
- Altre espressioni logiche costruiti usando `and`, `or`, e `not`.

> [!IMPORTANT]
> Esistono alcune situazioni in cui non tutti i tipi di espressione secondaria possono essere utilizzati con `and` / `or`, in particolare all'interno le espressioni lambda. Visualizzare [gli operatori di raccolta OData in ricerca di Azure](search-query-odata-collection-operators.md#limitations) per informazioni dettagliate.

### <a name="logical-operators-and-null"></a>Gli operatori logici e `null`

Non possono produrre la maggior parte delle espressioni booleane, ad esempio le funzioni e i confronti `null` valori e gli operatori logici non possono essere applicato per il `null` direttamente valore letterale (ad esempio, `x and null` non è consentito). Tuttavia, possono essere i campi booleani `null`, pertanto è necessario essere a conoscenza del modo in cui il `and`, `or`, e `not` operatori comportano in presenza di null. Ciò è riepilogato nella tabella seguente, dove `b` è un campo di tipo `Edm.Boolean`:

| Expression | Risultato quando `b` è `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Quando un campo booleano `b` viene visualizzata da sola in un'espressione di filtro, si comporta come se fosse stato scritto `b eq true`, pertanto, se `b` viene `null`, l'espressione restituisce `false`. Analogamente, `not b` si comporta come `not (b eq true)`, quindi restituisce `true`. In questo modo `null` lo stesso comportano campi `false`. Questo comportamento è coerente con il relativo funzionamento in combinazione con altre espressioni tramite `and` e `or`, come illustrato nella tabella precedente. Nonostante ciò, un confronto diretto al `false` (`b eq false`) verrà comunque restituito `false`. In altre parole, `null` non è uguale a `false`, anche se si comporta, ad esempio, nelle espressioni booleane.

## <a name="examples"></a>Esempi

Corrispondenza documenti dove il `rating` campo è compreso tra 3 e 5, inclusi:

    rating ge 3 and rating le 5

Corrispondenza documenti dove tutti gli elementi del `ratings` campo sono meno di 3 o maggiore di 5:

    ratings/all(r: r lt 3 or r gt 5)

Corrispondenza documenti dove il `location` campo si trova all'interno del poligono specificato e il documento non contiene il termine "pubblico".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Documenti per gli hotel in Vancouver, Canada corrispondenti in cui è presente una stanza con una base deluxe velocità inferiore a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
