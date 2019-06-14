---
title: OData order by riferimento - ricerca di Azure
description: Riferimenti al linguaggio di OData per la sintassi di order by nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079756"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Sintassi di OData $orderby in ricerca di Azure

 È possibile usare la [OData **$orderby** parametro](query-odata-filter-orderby-syntax.md) per applicare un ordinamento personalizzato dei risultati di ricerca in ricerca di Azure. Questo articolo descrive la sintassi del **$orderby** in modo dettagliato. Per informazioni più generali sull'utilizzo **$orderby** per presentare i risultati della ricerca, vedere [come usare ricerca di risultati in ricerca di Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintassi

Il **$orderby** parametro accetta un elenco delimitato da virgole di fino a 32 **clausole order by**. La sintassi di una clausola order by è descritta da di EBNF seguenti ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

Ogni clausola ha i criteri di ordinamento, seguiti facoltativamente da una direzione di ordinamento (`asc` ordine crescente o `desc` per ordine decrescente). Se non si specifica una direzione, il valore predefinito è crescente. I criteri di ordinamento possono essere il percorso di un `sortable` campo o una chiamata a uno il [ `geo.distance` ](search-query-odata-geo-spatial-functions.md) o le [ `search.score` ](search-query-odata-search-score-function.md) funzioni.

Se più documenti hanno gli stessi criteri di ordinamento e il `search.score` funzione non viene usata (ad esempio, se Ordina in base un valore numerico `Rating` tutti i campi e tre documenti hanno una classificazione compresa tra 4), valori equivalenti verranno interrotte dal punteggio di documenti in ordine decrescente. Quando i punteggi di documenti sono uguali (ad esempio, quando è presente alcuna query di ricerca full-text specificata nella richiesta), quindi l'ordinamento relativo dei documenti correlati è indeterminato.

È possibile specificare più criteri di ordinamento. L'ordine delle espressioni determina l'ordinamento finale. Ad esempio, per ordinare in modo decrescente dal punteggio, seguita dalla classificazione, la sintassi sarà `$orderby=search.score() desc,Rating desc`.

La sintassi per `geo.distance` in **$orderby** è la stessa che in **$filter**. Quando si usa `geo.distance` in **$orderby**, il campo a cui si applica deve essere di tipo `Edm.GeographyPoint` e deve anche essere `sortable`.

La sintassi per `search.score` in **$orderby** è `search.score()`. La funzione `search.score` non accetta parametri.

## <a name="examples"></a>Esempi

Ordinare gli alberghi per tariffa di base crescente:

    $orderby=BaseRate asc

Ordinare gli alberghi per classificazione decrescente, quindi per tariffa di base crescente. Tenere presente che l'ordinamento predefinito è crescente:

    $orderby=Rating desc,BaseRate

Ordinare gli hotel decrescente, classificazione, quindi crescente di distanza dalle coordinate specificate:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Ordinare gli hotel in ordine decrescente tramite search.score e classificazione e quindi in ordine crescente in base distanza dalle coordinate specificate. Tra i due hotel con i punteggi di pertinenza identiche e le classificazioni, quella più vicina è elencato per primo:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Come usare ricerca di risultati in ricerca di Azure](search-pagination-page-layout.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
