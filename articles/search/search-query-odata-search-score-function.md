---
title: Riferimento alla funzione OData search.score
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per l'uso della funzione search.score nelle query di Ricerca cognitiva di Azure.Syntax and reference documentation for using the search.score function in Azure Cognitive Search queries.
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113130"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Funzione `search.score` OData in Ricerca cognitiva di AzureOData function in Azure Cognitive Search

Quando si invia una query a Ricerca cognitiva di Azure senza il [parametro **$orderby** ](search-query-odata-orderby.md), i risultati restituiti verranno ordinati in ordine decrescente in base al punteggio di pertinenza. Anche quando si utilizza **$orderby**, il punteggio di pertinenza verrà utilizzato per interrompere i legami per impostazione predefinita. Tuttavia, a volte è utile utilizzare il punteggio di pertinenza come criterio di ordinamento iniziale e alcuni altri criteri come tie-breaker. La `search.score` funzione consente di eseguire questa operazione.

## <a name="syntax"></a>Sintassi

La sintassi per `search.score` in **$orderby** è `search.score()`. La funzione `search.score` non accetta parametri. Può essere utilizzato `asc` con `desc` l'identificatore o sort-sort, proprio come qualsiasi altra clausola nel **parametro $orderby.** Può essere visualizzato in qualsiasi punto dell'elenco dei criteri di ordinamento.

## <a name="example"></a>Esempio

Ordinare gli hotel in `search.score` `rating`ordine decrescente per e , quindi in ordine crescente in base alla distanza dalle coordinate specificate in modo che tra due hotel con valutazioni identiche, quello più vicino sia elencato per primo:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Documenti di ricerca &#40;&#41;API EST di Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
