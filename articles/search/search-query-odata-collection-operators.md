---
title: Informazioni di riferimento sull'operatore di raccolta OData
titleSuffix: Azure Cognitive Search
description: Quando si creano espressioni di filtro nelle query di Ricerca cognitiva di Azure, usare gli operatori "any" e "all" nelle espressioni lambda quando il filtro si trova in un campo di raccolta o complesso.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113244"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operatori di raccolta OData `any` in Ricerca cognitiva di Azure e`all`

Quando si scrive [un'espressione](query-odata-filter-orderby-syntax.md) di filtro OData da usare con Ricerca cognitiva di Azure, è spesso utile filtrare in base ai campi della raccolta. È possibile ottenere `any` questo `all` utilizzando gli operatori e .

## <a name="syntax"></a>Sintassi

Il seguente File EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione OData che utilizza `any` o `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

Esistono tre forme di espressione che filtrano le raccolte.

- I primi due scorrono un campo della raccolta, applicando un predicato specificato sotto forma di espressione lambda a ogni elemento della raccolta.
  - Espressione che `all` `true` utilizza restituisce se il predicato è true per ogni elemento della raccolta.
  - Espressione che `any` `true` utilizza restituisce se il predicato è true per almeno un elemento della raccolta.
- La terza forma di `any` filtro di raccolta utilizza senza un'espressione lambda per verificare se un campo di raccolta è vuoto. Se l'insieme contiene elementi, restituisce `true`. Se l'insieme è `false`vuoto, restituisce .

**Un'espressione lambda** in un filtro di raccolta è simile al corpo di un ciclo in un linguaggio di programmazione. Definisce una variabile, denominata variabile di **intervallo**, che contiene l'elemento corrente della raccolta durante l'iterazione. Definisce inoltre un'altra espressione booleana che è i criteri di filtro da applicare alla variabile di intervallo per ogni elemento della raccolta.

## <a name="examples"></a>Esempi

Documenti di `tags` corrispondenza il cui campo contiene esattamente la stringa "wifi":

    tags/any(t: t eq 'wifi')

Abbina i documenti in `ratings` cui ogni elemento del campo è compreso tra 3 e 5, inclusi:

    ratings/all(r: r ge 3 and r le 5)

Abbina i documenti in cui `locations` una delle coordinate geografiche nel campo si trova all'interno del poligono specificato:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Abbina i `rooms` documenti in cui il campo è vuoto:

    not rooms/any()

Abbina i documenti dove `rooms/amenities` per tutte le `rooms/baseRate` stanze, il campo contiene "tv" ed è inferiore a 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitazioni

Non tutte le funzionalità delle espressioni di filtro sono disponibili all'interno del corpo di un'espressione lambda. Le limitazioni variano a seconda del tipo di dati del campo di raccolta che si desidera filtrare. Nella tabella seguente sono riepilogate le limitazioni.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Per altre informazioni su queste limitazioni ed esempi, vedere [Risoluzione dei problemi relativi ai filtri](search-query-troubleshoot-collection-filters.md)di raccolta in Ricerca cognitiva di Azure .For more details on these limitations as well as examples, see Troubleshooting collection filters in Azure Cognitive Search . Per informazioni più approfondite sull'esistenza di queste limitazioni, vedere Informazioni sui filtri di [raccolta in Ricerca cognitiva](search-query-understand-collection-filters.md)di Azure .

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
