---
title: Riferimento all'operatore della raccolta OData
titleSuffix: Azure Cognitive Search
description: Quando si creano espressioni di filtro in Azure ricerca cognitiva query, usare gli operatori "any" e "All" nelle espressioni lambda quando il filtro si trova in una raccolta o in un campo di raccolta complesso.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113244"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operatori di raccolta OData in Azure ricerca cognitiva-`any` e `all`

Quando si scrive un' [espressione di filtro OData](query-odata-filter-orderby-syntax.md) da usare con ricerca cognitiva di Azure, è spesso utile filtrare in campi di raccolta. A tale scopo, è possibile usare gli operatori `any` e `all`.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione OData che usa `any` o `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

Sono disponibili tre forme di espressione che filtrano le raccolte.

- I primi due eseguono l'iterazione su un campo della raccolta, applicando un predicato specificato sotto forma di espressione lambda a ogni elemento della raccolta.
  - Un'espressione che usa `all` restituisce `true` se il predicato è true per ogni elemento della raccolta.
  - Un'espressione che usa `any` restituisce `true` se il predicato è true per almeno un elemento della raccolta.
- La terza forma di filtro della raccolta usa `any` senza un'espressione lambda per verificare se un campo di raccolta è vuoto. Se la raccolta contiene elementi, restituisce `true`. Se la raccolta è vuota, restituisce `false`.

Un' **espressione lambda** in un filtro di raccolta è simile al corpo di un ciclo in un linguaggio di programmazione. Definisce una variabile, denominata variabile di **intervallo**, che include l'elemento corrente della raccolta durante l'iterazione. Definisce anche un'altra espressione booleana che rappresenta i criteri di filtro da applicare alla variabile di intervallo per ogni elemento della raccolta.

## <a name="examples"></a>esempi

Trova la corrispondenza dei documenti il cui campo `tags` contiene esattamente la stringa "WiFi":

    tags/any(t: t eq 'wifi')

Trova la corrispondenza dei documenti in cui ogni elemento del campo `ratings` è compreso tra 3 e 5 inclusi:

    ratings/all(r: r ge 3 and r le 5)

Trovare la corrispondenza con i documenti in cui una delle coordinate geografiche nel campo `locations` si trova all'interno del poligono specificato:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Trova la corrispondenza dei documenti in cui il campo `rooms` è vuoto:

    not rooms/any()

Trova la corrispondenza con i documenti in cui per tutte le chat room il campo `rooms/amenities` contiene "TV" e `rooms/baseRate` è inferiore a 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitazioni

Non tutte le funzionalità delle espressioni di filtro sono disponibili all'interno del corpo di un'espressione lambda. Le limitazioni variano a seconda del tipo di dati del campo della raccolta che si desidera filtrare. Nella tabella seguente sono riepilogate le limitazioni.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Per altri dettagli su queste limitazioni ed esempi, vedere [risoluzione dei problemi relativi ai filtri di raccolta in ricerca cognitiva di Azure](search-query-troubleshoot-collection-filters.md). Per informazioni più dettagliate sul motivo per cui sono presenti queste limitazioni, vedere informazioni sui [filtri di raccolta in Azure ricerca cognitiva](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
