---
title: Riferimento alla funzione di ricerca OData. Score-ricerca di Azure
description: Funzione di ricerca OData. Score nelle query di ricerca di Azure.
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647526"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Funzione `search.score` OData in ricerca di Azure

Quando si invia una query a ricerca di Azure senza il [parametro **$OrderBy** ](search-query-odata-orderby.md), i risultati restituiti verranno ordinati in ordine decrescente in base al Punteggio di pertinenza. Anche quando si usa **$OrderBy**, per impostazione predefinita viene usato il Punteggio di pertinenza per interrompere i vincoli. Tuttavia, a volte è utile usare il Punteggio di pertinenza come criterio di ordinamento iniziale e altri criteri come il tie-breaker. La `search.score` funzione consente di eseguire questa operazione.

## <a name="syntax"></a>Sintassi

La sintassi per `search.score` in **$orderby** è `search.score()`. La funzione `search.score` non accetta parametri. Può essere usato con l' `asc` identificatore di ordinamento o `desc` , come per qualsiasi altra clausola nel parametro **$OrderBy** . Può trovarsi in qualsiasi punto dell'elenco dei criteri di ordinamento.

## <a name="example"></a>Esempio

Ordinare gli hotel in ordine decrescente `search.score` in `rating`base a e, quindi in ordine crescente in base alla distanza tra le coordinate specificate, in modo che tra due alberghi con valutazioni identiche, il più vicino venga elencato per primo:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
