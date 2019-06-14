---
title: Riferimento agli operatori raccolta OData - ricerca di Azure
description: Operatori di raccolta OData, tutti e le espressioni lambda nelle query di ricerca di Azure.
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079938"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Operatori di raccolta OData in ricerca di Azure - `any` e `all`

Quando si scrive un' [espressione di filtro OData](query-odata-filter-orderby-syntax.md) per usare ricerca di Azure, è spesso utile filtrare i campi della raccolta. È possibile ottenere questo risultato utilizzando il `any` e `all` operatori.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione di OData che utilizza `any` o `all`.

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
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

Esistono tre tipi di espressione che filtra le raccolte.

- I primi due eseguire l'iterazione su un campo di insieme, applica un predicato specificato sotto forma di un'espressione lambda per ogni elemento della raccolta.
  - Un'espressione che utilizza `all` restituisce `true` se il predicato è true per ogni elemento della raccolta.
  - Un'espressione che utilizza `any` restituisce `true` se il predicato è true per almeno un elemento della raccolta.
- Il terzo form della raccolta di filtro utilizzi `any` senza un'espressione lambda per verificare se un campo di raccolta è vuoto. Se la raccolta contiene elementi, viene restituito `true`. Se la raccolta è vuota, verrà restituito `false`.

Oggetto **espressione lambda** in una raccolta di filtri sono simile al corpo di un ciclo in un linguaggio di programmazione. Definisce una variabile, denominato il **variabile di intervallo**, che contiene l'elemento della raccolta durante l'iterazione corrente. Definisce anche un'altra espressione booleana che indica i criteri di filtro da applicare alla variabile di intervallo per ogni elemento della raccolta.

## <a name="examples"></a>Esempi

Corrispondenza documenti la cui `tags` campo contiene esattamente alla stringa "wifi":

    tags/any(t: t eq 'wifi')

Corrispondenza documenti dove ogni elemento del `ratings` campo sia compresa tra 3 e 5, inclusi:

    ratings/all(r: r ge 3 and r le 5)

Documenti di corrispondenza in cui qualsiasi dell'area geografica coordina nel `locations` campo si trova all'interno del poligono specificato:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Corrispondenza documenti dove il `rooms` campo è vuoto:

    not rooms/any()

Documenti corrispondenti in cui per tutte le sale riunioni, il `rooms/amenities` campo contiene "tv" e `rooms/baseRate` è minore di 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitazioni

Non tutte le funzionalità delle espressioni di filtro sono disponibile all'interno del corpo di un'espressione lambda. Le limitazioni differiscono a seconda del tipo di dati del campo della raccolta che si desidera filtrare. Nella tabella seguente sono riepilogate le limitazioni.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Per altre informazioni su queste limitazioni, nonché esempi, vedere [risoluzione dei problemi di filtri di raccolta in ricerca di Azure](search-query-troubleshoot-collection-filters.md). Per informazioni più dettagliate sui motivi per cui queste limitazioni, vedere [informazioni sui filtri in ricerca di Azure per raccolta](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
