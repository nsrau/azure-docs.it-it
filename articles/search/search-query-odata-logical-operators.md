---
title: Riferimento all'operatore logico OData
titleSuffix: Azure Cognitive Search
description: Documentazione di sintassi e riferimento per l'uso di operatori logici OData e, o e non, nelle query di Ricerca cognitiva di Azure.Syntax and reference documentation for using OData logical operators, and, or, and not, in Azure Cognitive Search queries.
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113178"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operatori logici OData in `and` `or`Ricerca cognitiva di Azure - , ,`not`

[Le espressioni di filtro OData](query-odata-filter-orderby-syntax.md) in Ricerca `true` `false`cognitiva di Azure sono espressioni booleane che restituiscono o . È possibile scrivere un filtro complesso scrivendo una serie di [filtri più semplici](search-query-odata-comparison-operators.md) e componendoli utilizzando gli operatori logici dell'algebra [booleana](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: operatore binario `true` che restituisce se le sottoespressioni `true`sinistra e destra restituiscono .
- `or`: operatore binario `true` che restituisce se una delle relative sottoespressioni sinistra o destra restituisce `true`.
- `not`: operatore unario che `true` restituisce se la `false`relativa sottoespressione restituisce e viceversa.

Questi, insieme agli [ `any` operatori `all` ](search-query-odata-collection-operators.md)di raccolta e , consentono di costruire filtri in grado di esprimere criteri di ricerca molto complessi.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione OData che utilizza gli operatori logici.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

Esistono due forme di espressioni`and`/`or`logiche: binarie ( ), dove`not`sono presenti due sottoespressioni e unarie ( ), in cui è presente una sola. Le sottoespressioni possono essere espressioni booleane di qualsiasi tipo:

- Campi o variabili di intervallo di tipo`Edm.Boolean`
- Funzioni che restituiscono `Edm.Boolean`valori `geo.intersects` di tipo , ad esempio`search.ismatch`
- [Espressioni di confronto](search-query-odata-comparison-operators.md), ad esempio`rating gt 4`
- [Espressioni di](search-query-odata-collection-operators.md)insiemi , ad esempio`Rooms/any(room: room/Type eq 'Deluxe Room')`
- I valori `true` letterali booleani o `false`.
- Altre espressioni logiche costruite utilizzando `and`, `or`, e `not`.

> [!IMPORTANT]
> Esistono alcune situazioni in cui non tutti i `and` / `or`tipi di sottoespressione possono essere utilizzati con , in particolare all'interno di espressioni lambda. Per informazioni dettagliate, vedere Operatori di [raccolte OData in Ricerca cognitiva](search-query-odata-collection-operators.md#limitations) di Azure.See OData collection operators in Azure Cognitive Search for details.

### <a name="logical-operators-and-null"></a>Operatori logici e`null`

La maggior parte delle espressioni booleane, ad esempio funzioni e confronti, non può produrre `null` valori e gli operatori logici non possono essere applicati direttamente al `null` valore letterale (ad esempio, `x and null` non è consentito). Tuttavia, i `null`campi booleani possono essere , `and` `or`pertanto `not` è necessario tenere presente il funzionamento degli operatori , e in presenza di null. Questo è riepilogato nella `b` tabella seguente, `Edm.Boolean`dove è un campo di tipo :

| Expression | Risultato `b` quando è`null` |
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

Quando un `b` campo booleano viene visualizzato da solo in un'espressione `b eq true`di `b` filtro, si comporta come se fosse stato scritto, quindi se è `null`, l'espressione restituisce `false`. Allo stesso `not b` modo, `not (b eq true)`si comporta come `true`, quindi restituisce . In questo `null` modo, i campi `false`si comportano come . Questo è coerente con il modo in `and` cui `or`si comportano quando vengono combinati con altre espressioni utilizzando e , come illustrato nella tabella precedente. Nonostante ciò, un `false` `b eq false`confronto diretto con `false`( ) verrà comunque valutato . In altre `null` parole, non `false`è uguale a , anche se si comporta come nelle espressioni booleane.

## <a name="examples"></a>Esempi

Abbina i `rating` documenti in cui il campo è compreso tra 3 e 5, inclusi:

    rating ge 3 and rating le 5

Abbina i documenti in `ratings` cui tutti gli elementi del campo sono minori di 3 o maggiori di 5:

    ratings/all(r: r lt 3 or r gt 5)

Abbina i `location` documenti in cui il campo si trova all'interno del poligono specificato e il documento non contiene il termine "pubblico".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Abbina i documenti per gli hotel a Vancouver, Canada, dove c'è una camera deluxe con una tariffa base inferiore a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
