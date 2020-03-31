---
title: Funzione di query moreLikeThis (anteprima)
titleSuffix: Azure Cognitive Search
description: Descrive la funzionalità moreLikeThis (anteprima), disponibile nelle versioni di anteprima dell'API REST di Ricerca cognitiva di Azure.Describes the moreLikeThis (preview) feature, which is available in preview versions of the Azure Cognitive Search REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873812"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (anteprima) in Ricerca cognitiva di AzuremoreLikeThis (preview) in Azure Cognitive Search

> [!IMPORTANT] 
> Questa funzionalità è attualmente in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Attualmente non è disponibile alcun supporto per il portale o .NET SDK.

`moreLikeThis=[key]`è un parametro di query [nell'API Cerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents) che trova documenti simili al documento specificato dalla chiave del documento. Quando viene effettuata una richiesta di ricerca con `moreLikeThis`, viene generata una query con i termini di ricerca estratti dal documento specificato che lo descrivono al meglio. La query generata viene quindi usata per eseguire la richiesta di ricerca. Per impostazione predefinita, il contenuto di tutti i campi ricercabili `searchFields` viene considerato, meno i campi con restrizioni specificati utilizzando il parametro . Il parametro `moreLikeThis` non può essere usato con il parametro di ricerca `search=[string]`.

Per impostazione predefinita, viene considerato il contenuto di tutti i campi di ricerca di primo livello. Se invece si desidera specificare campi `searchFields` specifici, è possibile utilizzare il parametro . 

Non è `MoreLikeThis` possibile utilizzare su sottocampi ricercabili in un [tipo complesso](search-howto-complex-data-types.md).

## <a name="examples"></a>Esempi

In tutti gli esempi seguenti viene usato l'esempio hotels from [Quickstart: Create a search index in the Azure portal](search-get-started-portal.md).

### <a name="simple-query"></a>Query semplice

La query seguente trova i documenti i cui campi di descrizione `moreLikeThis` sono più simili al campo del documento di origine come specificato dal parametro:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

In questo esempio, la richiesta cerca hotel `HotelId` simili a quello con 29.
Anziché utilizzare HTTP GET, `MoreLikeThis` è anche possibile richiamare utilizzando HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Applicare filtri

`MoreLikeThis`può essere combinato con altri `$filter`parametri di query comuni come . Ad esempio, la query può essere limitata solo agli hotel la cui categoria è "Budget" e in cui la valutazione è superiore a 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Selezionare i campi e limitare i risultati

Il `$top` selettore può essere utilizzato per limitare il `MoreLikeThis` numero di risultati da restituire in una query. Inoltre, i campi `$select`possono essere selezionati con . Qui vengono selezionati i primi tre hotel insieme al loro ID, nome e valutazione: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Passaggi successivi

È possibile utilizzare qualsiasi strumento di test Web per sperimentare questa funzionalità.  Si consiglia di utilizzare Postman per questo esercizio.

> [!div class="nextstepaction"]
> [Esplorare le API REST di Ricerca cognitiva di Azure usando PostmanExplore Azure Cognitive Search REST APIs using Postman](search-get-started-postman.md)
