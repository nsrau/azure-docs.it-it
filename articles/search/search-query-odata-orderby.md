---
title: Guida di riferimento per l'ordine di OData-ricerca di Azure
description: Informazioni di riferimento sul linguaggio OData per la sintassi order-by nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 8ee44549931100a1affa5e2bb9e5cda904c05ed1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647530"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Sintassi di $orderby OData in ricerca di Azure

 È possibile usare il [parametro OData **$OrderBy** ](query-odata-filter-orderby-syntax.md) per applicare un ordinamento personalizzato per i risultati della ricerca in ricerca di Azure. Questo articolo descrive in dettaglio la sintassi dei **$OrderBy** . Per informazioni più generali su come usare **$OrderBy** quando si presentano i risultati della ricerca, vedere [come usare i risultati della ricerca in ricerca di Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintassi

Il parametro **$OrderBy** accetta un elenco delimitato da virgole di un massimo di 32 **clausole ORDER-BY**. La sintassi di una clausola ORDER-BY è descritta dal seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Vedere informazioni di [riferimento sulla sintassi di espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il EBNF completo.

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

- [Come usare i risultati della ricerca in ricerca di Azure](search-pagination-page-layout.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
