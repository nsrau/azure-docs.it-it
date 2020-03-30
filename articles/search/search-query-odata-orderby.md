---
title: Riferimento ordine per IData
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento del linguaggio per l'uso di query order-by in Ricerca cognitiva di Azure.Syntax and language reference documentation for using order-by in Azure Cognitive Search queries.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113157"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby syntax in Azure Cognitive Search

 È possibile usare il [parametro OData **$orderby** ](query-odata-filter-orderby-syntax.md) per applicare un ordinamento personalizzato per i risultati della ricerca in Ricerca cognitiva di Azure.You can use the OData $orderby parameter to apply a custom sort sort for search results in Azure Cognitive Search. In questo articolo viene descritta la sintassi di **$orderby** in dettaglio. Per altre informazioni generali su come usare **$orderby** quando si presentano i risultati della ricerca, vedere Come usare i risultati della [ricerca in Ricerca cognitiva](search-pagination-page-layout.md)di Azure.For more general information about how to use $orderby when presenting search results, see How to work with search results in Azure Cognitive Search .

## <a name="syntax"></a>Sintassi

Il parametro **$orderby** accetta un elenco separato da virgole di un massimo di 32 **clausole order-by**. La sintassi di una clausola order-by è descritta dal seguente tasto EBNF[(Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

Ogni clausola ha criteri di ordinamento,`asc` seguiti `desc` facoltativamente da una direzione di ordinamento (per crescente o decrescente). Se non si specifica una direzione, il valore predefinito è crescente. I criteri di ordinamento possono `sortable` essere il percorso [`geo.distance`](search-query-odata-geo-spatial-functions.md) di [`search.score`](search-query-odata-search-score-function.md) un campo o una chiamata alle funzioni o .

Se più documenti hanno gli `search.score` stessi criteri di ordinamento e la funzione `Rating` non viene utilizzata (ad esempio, se si ordina in base a un campo numerico e tre documenti hanno tutti una valutazione di 4), i legami verranno interrotti per punteggio del documento in ordine decrescente. Quando i punteggi dei documenti sono gli stessi (ad esempio, quando non è presente alcuna query di ricerca full-text specificata nella richiesta), l'ordine relativo dei documenti legati è indeterminato.

È possibile specificare più criteri di ordinamento. L'ordine delle espressioni determina l'ordinamento finale. Ad esempio, per ordinare in senso decrescente in `$orderby=search.score() desc,Rating desc`base al punteggio, seguito da Valutazione, la sintassi sarebbe .

La sintassi per `geo.distance` in **$orderby** è la stessa che in **$filter**. Quando si usa `geo.distance` in **$orderby**, il campo a cui si applica deve essere di tipo `Edm.GeographyPoint` e deve anche essere `sortable`.

La sintassi per `search.score` in **$orderby** è `search.score()`. La `search.score` funzione non accetta parametri.

## <a name="examples"></a>Esempi

Ordinare gli alberghi per tariffa di base crescente:

    $orderby=BaseRate asc

Ordinare gli alberghi per classificazione decrescente, quindi per tariffa di base crescente. Tenere presente che l'ordinamento predefinito è crescente:

    $orderby=Rating desc,BaseRate

Ordina gli hotel in ordine decrescente in base alla classificazione, quindi in ascendente in base alla distanza dalle coordinate specificate:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Ordinare gli hotel in ordine decrescente in base a search.score e valutazione, quindi in ordine crescente in base alla distanza dalle coordinate specificate. Tra due hotel con punteggi e valutazioni di pertinenza identici, quello più vicino è elencato per primo:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Come usare i risultati della ricerca in Ricerca cognitiva di AzureHow to work with search results in Azure Cognitive Search](search-pagination-page-layout.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
