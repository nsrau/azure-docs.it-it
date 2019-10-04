---
title: Guida di riferimento agli operatori OData Collection-ricerca di Azure
description: Operatori di raccolta OData, any e all e espressioni lambda nelle query di ricerca di Azure.
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647644"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Operatori di raccolte OData in ricerca di `any` Azure-e`all`

Quando si scrive un' [espressione di filtro OData](query-odata-filter-orderby-syntax.md) da usare con ricerca di Azure, è spesso utile filtrare in campi di raccolta. Per ottenere questo risultato, è `any` possibile `all` usare gli operatori e.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione OData che `any` USA `all`o.

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
> Vedere informazioni di [riferimento sulla sintassi di espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il EBNF completo.

Sono disponibili tre forme di espressione che filtrano le raccolte.

- I primi due eseguono l'iterazione su un campo della raccolta, applicando un predicato specificato sotto forma di espressione lambda a ogni elemento della raccolta.
  - Un'espressione che `all` utilizza `true` restituisce se il predicato è true per ogni elemento della raccolta.
  - Un'espressione che `any` utilizza `true` restituisce se il predicato è true per almeno un elemento della raccolta.
- La terza forma di filtro di raccolta `any` utilizza senza un'espressione lambda per verificare se un campo di raccolta è vuoto. Se la raccolta contiene elementi, restituisce `true`. Se la raccolta è vuota, restituisce `false`.

Un' **espressione lambda** in un filtro di raccolta è simile al corpo di un ciclo in un linguaggio di programmazione. Definisce una variabile, denominata variabile di **intervallo**, che include l'elemento corrente della raccolta durante l'iterazione. Definisce anche un'altra espressione booleana che rappresenta i criteri di filtro da applicare alla variabile di intervallo per ogni elemento della raccolta.

## <a name="examples"></a>Esempi

Trova la corrispondenza `tags` dei documenti il cui campo contiene esattamente la stringa "WiFi":

    tags/any(t: t eq 'wifi')

Trova la `ratings` corrispondenza dei documenti in cui ogni elemento del campo rientra tra 3 e 5 inclusi:

    ratings/all(r: r ge 3 and r le 5)

Trovare la corrispondenza con i documenti in cui una delle `locations` Coordinate geografiche nel campo si trova all'interno del poligono specificato:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Trova la corrispondenza dei `rooms` documenti in cui il campo è vuoto:

    not rooms/any()

Trova la corrispondenza con i documenti in cui `rooms/amenities` per tutte le chat room il `rooms/baseRate` campo contiene "TV" ed è minore di 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitazioni

Non tutte le funzionalità delle espressioni di filtro sono disponibili all'interno del corpo di un'espressione lambda. Le limitazioni variano a seconda del tipo di dati del campo della raccolta che si desidera filtrare. Nella tabella seguente sono riepilogate le limitazioni.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Per altri dettagli su queste limitazioni e per esempi, vedere [risoluzione dei problemi relativi ai filtri di raccolta in ricerca di Azure](search-query-troubleshoot-collection-filters.md). Per informazioni più dettagliate sul motivo per cui sono presenti queste limitazioni, vedere informazioni sui [filtri di raccolta in ricerca di Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
