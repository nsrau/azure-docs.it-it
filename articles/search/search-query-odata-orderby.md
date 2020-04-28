---
title: Riferimento order-by OData
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per il linguaggio per l'uso di order-by in Azure ricerca cognitiva query.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113157"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Sintassi di $orderby OData in Azure ricerca cognitiva

 È possibile usare il [parametro OData **$OrderBy** ](query-odata-filter-orderby-syntax.md) per applicare un ordinamento personalizzato per i risultati della ricerca in ricerca cognitiva di Azure. Questo articolo descrive in dettaglio la sintassi dei **$OrderBy** . Per informazioni generali su come usare **$OrderBy** quando si presentano i risultati della ricerca, vedere [come usare i risultati della ricerca in Azure ricerca cognitiva](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintassi

Il parametro **$OrderBy** accetta un elenco delimitato da virgole di un massimo di 32 **clausole ORDER-BY**. La sintassi di una clausola ORDER-BY è descritta dal seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

Ogni clausola ha criteri di ordinamento, seguiti facoltativamente da una direzione`asc` di ordinamento (per `desc` l'ordine crescente o decrescente). Se non si specifica una direzione, il valore predefinito è Ascending. I criteri di ordinamento possono essere il percorso di un `sortable` campo o una chiamata alle [`geo.distance`](search-query-odata-geo-spatial-functions.md) [`search.score`](search-query-odata-search-score-function.md) funzioni o.

Se più documenti hanno gli stessi criteri di ordinamento e `search.score` la funzione non viene usata (ad esempio, se si ordina in `Rating` base a un campo numerico e tre documenti hanno una classificazione pari a 4), i vincoli verranno interrotti dal punteggio del documento in ordine decrescente. Quando i punteggi dei documenti sono uguali, ad esempio quando nella richiesta non è specificata una query di ricerca full-text, l'ordine relativo dei documenti collegati è indeterminato.

È possibile specificare più criteri di ordinamento. L'ordine delle espressioni determina l'ordinamento finale. Ad esempio, per eseguire l'ordinamento decrescente in base al punteggio, seguito dalla classificazione, `$orderby=search.score() desc,Rating desc`la sintassi è.

La sintassi per `geo.distance` in **$orderby** è la stessa che in **$filter**. Quando si usa `geo.distance` in **$orderby**, il campo a cui si applica deve essere di tipo `Edm.GeographyPoint` e deve anche essere `sortable`.

La sintassi per `search.score` in **$orderby** è `search.score()`. La funzione `search.score` non accetta parametri.

## <a name="examples"></a>Esempi

Ordinare gli alberghi per tariffa di base crescente:

    $orderby=BaseRate asc

Ordinare gli alberghi per classificazione decrescente, quindi per tariffa di base crescente. Tenere presente che l'ordinamento predefinito è crescente:

    $orderby=Rating desc,BaseRate

Ordinare gli hotel in ordine decrescente in base alla classificazione, quindi ascendere per distanza dalle coordinate specificate:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Ordinare gli hotel in ordine decrescente in base alla ricerca. Punteggio e classificazione, quindi in ordine crescente per distanza dalle coordinate specificate. Tra due alberghi con punteggi e punteggi di pertinenza identici, quello più vicino è elencato per primo:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Come usare i risultati della ricerca in Azure ricerca cognitiva](search-pagination-page-layout.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche nei documenti &#40;API REST di Azure ricerca cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
