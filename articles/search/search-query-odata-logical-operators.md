---
title: Guida di riferimento agli operatori logici OData-ricerca di Azure
description: Operatori logici OData, and, or e not, nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: bf4939a40a2fdf1c8fc6cf97beca0184b1604c98
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648004"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Operatori logici OData in ricerca di `and`Azure `or`:,,`not`

Le [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) in ricerca di Azure sono espressioni booleane `true` che `false`restituiscono o. È possibile scrivere un filtro complesso scrivendo una serie di [filtri più semplici](search-query-odata-comparison-operators.md) e componendoli usando gli operatori logici di [algebra booleana](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Operatore binario che restituisce `true` se entrambe le sottoespressioni di `true`sinistra e destra restituiscono.
- `or`: Operatore binario che restituisce `true` se una delle sottoespressioni a sinistra o a destra restituisce. `true`
- `not`: Operatore unario che restituisce `true` se la relativa sottoespressione restituisce `false`, e viceversa.

Insieme agli [ `any` operatori di raccolta e `all` ](search-query-odata-collection-operators.md), consentono di creare filtri che possano esprimere criteri di ricerca molto complessi.

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
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Vedere informazioni di [riferimento sulla sintassi di espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il EBNF completo.

Esistono due forme di espressioni`and`logiche: Binary (/`or`), in cui sono presenti due sottoespressioni e unario (`not`), dove è presente un solo elemento. Le espressioni secondarie possono essere espressioni booleane di qualsiasi tipo:

- Campi o variabili di intervallo di tipo`Edm.Boolean`
- Funzioni che restituiscono valori di `Edm.Boolean`tipo, `geo.intersects` ad esempio o`search.ismatch`
- [Espressioni di confronto](search-query-odata-comparison-operators.md), ad esempio`rating gt 4`
- [Espressioni di raccolta](search-query-odata-collection-operators.md), ad esempio`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Valori letterali `true` booleani o `false`.
- Altre espressioni logiche costruite `and`con `or`, e `not`.

> [!IMPORTANT]
> Esistono situazioni in cui non tutti i tipi di espressione secondaria possono essere usati con, `and`in / `or`particolare nelle espressioni lambda. Per informazioni dettagliate, vedere [operatori di raccolte OData in ricerca di Azure](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Operatori logici e`null`

La maggior parte delle espressioni booleane quali funzioni e confronti non può produrre `null` valori e gli operatori logici non possono essere applicati direttamente `null` al valore `x and null` letterale (ad esempio, non è consentito). Tuttavia, i `and`campi booleani possono `null`essere, pertanto è necessario conoscere il comportamento degli operatori, `or`e `not` in presenza di valori null. Questa operazione viene riepilogata nella tabella seguente, dove `b` è un campo di tipo `Edm.Boolean`:

| Expression | Risultato quando `b` è`null` |
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

Quando `b` un campo booleano viene visualizzato da solo in un'espressione di filtro, si comporta come se fosse stato scritto `b eq true`, quindi se `b` è `null`, l'espressione restituisce. `false` Analogamente `not b` , si comporta come `not (b eq true)`, `true`quindi restituisce. In questo modo, `null` i campi si comportano come. `false` Questo comportamento è coerente con il modo in cui si comportano `and` in `or`combinazione con altre espressioni usando e, come illustrato nella tabella precedente. Nonostante questo, un confronto diretto a `false` (`b eq false` `false`) restituirà comunque. In altre parole, `null` non è uguale a `false`, anche se si comporta come in espressioni booleane.

## <a name="examples"></a>Esempi

Trova la corrispondenza dei `rating` documenti in cui il campo è compreso tra 3 e 5 inclusi:

    rating ge 3 and rating le 5

Trova la `ratings` corrispondenza dei documenti in cui tutti gli elementi del campo sono minori di 3 o maggiori di 5:

    ratings/all(r: r lt 3 or r gt 5)

Trova la corrispondenza dei `location` documenti in cui il campo si trova all'interno del poligono specificato e il documento non contiene il termine "public".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Trova la corrispondenza con i documenti per gli alberghi a Vancouver, Canada in cui è disponibile una stanza Deluxe con una tariffa di base inferiore a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
