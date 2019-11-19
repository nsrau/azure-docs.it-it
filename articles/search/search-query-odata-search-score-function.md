---
title: Riferimento alla funzione di ricerca OData. Score
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per l'uso della funzione search. Score in Azure ricerca cognitiva query.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113130"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Funzione `search.score` OData in Azure ricerca cognitiva

Quando si invia una query ad Azure ricerca cognitiva senza il [parametro **$OrderBy** ](search-query-odata-orderby.md), i risultati restituiti verranno ordinati in ordine decrescente in base al Punteggio di pertinenza. Anche quando si usa **$OrderBy**, per impostazione predefinita viene usato il Punteggio di pertinenza per interrompere i vincoli. Tuttavia, a volte è utile usare il Punteggio di pertinenza come criterio di ordinamento iniziale e altri criteri come il tie-breaker. La funzione `search.score` consente di eseguire questa operazione.

## <a name="syntax"></a>Sintassi

La sintassi per `search.score` in **$orderby** è `search.score()`. La funzione `search.score` non accetta parametri. Può essere usato con l'identificatore di ordinamento `asc` o `desc`, come per qualsiasi altra clausola nel parametro **$OrderBy** . Può trovarsi in qualsiasi punto dell'elenco dei criteri di ordinamento.

## <a name="example"></a>Esempio

Ordinare gli hotel in ordine decrescente in base a `search.score` e `rating`, quindi in ordine crescente in base alla distanza tra le coordinate specificate, in modo che tra due alberghi con valutazioni identiche, il più vicino venga elencato per primo:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API est di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
