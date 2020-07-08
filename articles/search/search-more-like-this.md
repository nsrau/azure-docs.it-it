---
title: funzionalità query moreLikeThis (anteprima)
titleSuffix: Azure Cognitive Search
description: Descrive la funzionalità moreLikeThis (anteprima), disponibile nelle versioni di anteprima dell'API REST di Azure ricerca cognitiva.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9fb43a0d39beacf02a6949228eaa32a719164987
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85552239"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (anteprima) in Azure ricerca cognitiva

> [!IMPORTANT] 
> Questa funzionalità è attualmente in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità è disponibile nell' [API REST versione 2020-06-30-Preview](search-api-preview.md) . Attualmente non è disponibile alcun portale o supporto per .NET SDK.

`moreLikeThis=[key]`è un parametro di query nell' [API cerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents) che consente di trovare documenti simili al documento specificato dalla chiave del documento. Quando viene effettuata una richiesta di ricerca con `moreLikeThis`, viene generata una query con i termini di ricerca estratti dal documento specificato che lo descrivono al meglio. La query generata viene quindi usata per eseguire la richiesta di ricerca. Per impostazione predefinita, viene considerato il contenuto di tutti i campi ricercabili, meno tutti i campi limitati specificati usando il `searchFields` parametro. Il parametro `moreLikeThis` non può essere usato con il parametro di ricerca `search=[string]`.

Per impostazione predefinita, viene considerato il contenuto di tutti i campi ricercabili di primo livello. Se invece si desidera specificare campi specifici, è possibile utilizzare il `searchFields` parametro. 

Non è possibile utilizzare `MoreLikeThis` sui sottocampi disponibili per la ricerca in un [tipo complesso](search-howto-complex-data-types.md).

## <a name="examples"></a>Esempi

Tutti gli esempi seguenti usano l'esempio di Hotel da [Guida introduttiva: creare un indice di ricerca nel portale di Azure](search-get-started-portal.md).

### <a name="simple-query"></a>Query semplice

La query seguente trova i documenti i cui campi di descrizione sono più simili al campo del documento di origine, come specificato dal `moreLikeThis` parametro:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

In questo esempio, la richiesta Cerca un hotel simile a quello con `HotelId` 29.
Anziché usare HTTP GET, è anche possibile richiamare `MoreLikeThis` tramite HTTP Post:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Applicare filtri

`MoreLikeThis`può essere combinato con altri parametri comuni di query come `$filter` . Ad esempio, la query può essere limitata solo a Alberghi la cui categoria è' budget ' e dove la classificazione è superiore a 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>Selezionare i campi e limitare i risultati

Il `$top` selettore può essere utilizzato per limitare il numero di risultati che devono essere restituiti in una `MoreLikeThis` query. Inoltre, è possibile selezionare i campi con `$select` . I primi tre hotel sono selezionati insieme al relativo ID, nome e classificazione: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>Passaggi successivi

Per provare questa funzionalità, è possibile usare qualsiasi strumento di test Web.  Per questo esercizio, è consigliabile usare l'impostore.

> [!div class="nextstepaction"]
> [Esplora le API REST di Azure ricerca cognitiva usando il postore](search-get-started-postman.md)
