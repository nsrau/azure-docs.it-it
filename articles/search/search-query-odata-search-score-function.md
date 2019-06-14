---
title: Riferimento alle funzioni di OData search.score - ricerca di Azure
description: Funzione search.score OData nelle query di ricerca di Azure.
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079691"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` in ricerca di Azure (funzione)

Quando si invia una query in ricerca di Azure senza la [ **$orderby** parametro](search-query-odata-orderby.md), i risultati restituiti verranno ordinati in ordine decrescente dal punteggio di pertinenza. Anche quando si usa **$orderby**, verrà utilizzato il punteggio di pertinenza per interrompere i collegamenti per impostazione predefinita. Tuttavia, talvolta è utile utilizzare il punteggio di pertinenza come un criterio di ordinamento iniziale e ad altri criteri come lo spareggio. Il `search.score` funzione consente di eseguire questa operazione.

## <a name="syntax"></a>Sintassi

La sintassi per `search.score` in **$orderby** è `search.score()`. La funzione `search.score` non accetta parametri. Può essere utilizzato con il `asc` oppure `desc` identificatore di ordinamento, proprio come qualsiasi altra clausola il **$orderby** parametro. Può trovarsi ovunque nell'elenco dei criteri di ordinamento.

## <a name="example"></a>Esempio

Ordinare gli hotel in ordine decrescente tramite `search.score` e `rating`e quindi in ordine crescente in base distanza dalle coordinate specificate in modo che tra i due hotel con classificazioni identici, il più vicino è elencato per primo:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
