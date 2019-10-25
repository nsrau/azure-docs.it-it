---
title: Guida di riferimento agli operatori logici OData
titleSuffix: Azure Cognitive Search
description: Gli operatori logici OData, and, or e not, in Azure ricerca cognitiva query.
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
ms.openlocfilehash: 4e016047d66e49f17c08d4b92a1c865f4b63e39b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793315"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operatori logici OData in Azure ricerca cognitiva-`and`, `or`, `not`

Le [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) in ricerca cognitiva di Azure sono espressioni booleane che restituiscono `true` o `false`. È possibile scrivere un filtro complesso scrivendo una serie di [filtri più semplici](search-query-odata-comparison-operators.md) e componendoli usando gli operatori logici di [algebra booleana](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: operatore binario che restituisce `true` se entrambe le sottoespressioni di sinistra e destra restituiscono `true`.
- `or`: operatore binario che restituisce `true` se una delle espressioni secondarie a sinistra o a destra restituisce `true`.
- `not`: operatore unario che restituisce `true` se la rispettiva sottoespressione restituisce `false`e viceversa.

Insieme agli [operatori di raccolta `any` e `all`](search-query-odata-collection-operators.md), consentono di creare filtri che possano esprimere criteri di ricerca molto complessi.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione OData che usa gli operatori logici.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

Esistono due forme di espressioni logiche: Binary (`and`/`or`), in cui sono presenti due sottoespressioni e unaria (`not`), in cui è presente un solo elemento. Le espressioni secondarie possono essere espressioni booleane di qualsiasi tipo:

- Campi o variabili di intervallo di tipo `Edm.Boolean`
- Funzioni che restituiscono valori di tipo `Edm.Boolean`, ad esempio `geo.intersects` o `search.ismatch`
- [Espressioni di confronto](search-query-odata-comparison-operators.md), ad esempio `rating gt 4`
- [Espressioni di raccolta](search-query-odata-collection-operators.md), ad esempio `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Valori letterali booleani `true` o `false`.
- Altre espressioni logiche costruite con `and`, `or`e `not`.

> [!IMPORTANT]
> Esistono situazioni in cui non tutti i tipi di espressione secondaria possono essere utilizzati con `and`/`or`, in particolare nelle espressioni lambda. Per informazioni dettagliate, vedere [operatori di raccolte OData in Azure ricerca cognitiva](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Operatori logici e `null`

La maggior parte delle espressioni booleane come le funzioni e i confronti non può produrre valori di `null` e gli operatori logici non possono essere applicati direttamente al valore letterale di `null`, ad esempio `x and null` non è consentito. Tuttavia, i campi booleani possono essere `null`, pertanto è necessario conoscere il comportamento degli operatori `and`, `or`e `not` in presenza di valori null. Questa operazione viene riepilogata nella tabella seguente, dove `b` è un campo di tipo `Edm.Boolean`:

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

Quando un campo booleano `b` viene visualizzato da solo in un'espressione di filtro, si comporta come se fosse stato scritto `b eq true`, pertanto se `b` è `null`, l'espressione restituisce `false`. Analogamente, `not b` si comporta come `not (b eq true)`, quindi restituisce `true`. In questo modo, i campi `null` si comportano come `false`. Questo comportamento è coerente con il comportamento che si verificano quando vengono combinate con altre espressioni usando `and` e `or`, come illustrato nella tabella precedente. Nonostante questo, un confronto diretto con `false` (`b eq false`) restituirà comunque `false`. In altre parole, `null` non è uguale `false`, anche se si comporta come in espressioni booleane.

## <a name="examples"></a>esempi

Trovare la corrispondenza con i documenti in cui il campo `rating` è compreso tra 3 e 5 inclusi:

    rating ge 3 and rating le 5

Trova la corrispondenza dei documenti in cui tutti gli elementi del campo `ratings` sono minori di 3 o maggiori di 5:

    ratings/all(r: r lt 3 or r gt 5)

Trovare la corrispondenza con i documenti in cui il campo `location` si trova all'interno del poligono specificato e il documento non contiene il termine "public".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Trova la corrispondenza con i documenti per gli alberghi a Vancouver, Canada in cui è disponibile una stanza Deluxe con una tariffa di base inferiore a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
