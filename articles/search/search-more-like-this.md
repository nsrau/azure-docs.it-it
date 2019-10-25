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
ms.openlocfilehash: b7959beca8a7787a331388b77ebe4060c3675e6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793466"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (anteprima) in Azure ricerca cognitiva

> [!Note]
> moreLikeThis è in anteprima e non è destinato all'uso in produzione. Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Non è attualmente disponibile alcun supporto di .NET SDK.

`moreLikeThis=[key]` è un parametro di query nell' [API cerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents) che consente di trovare documenti simili al documento specificato dalla chiave del documento. Quando viene effettuata una richiesta di ricerca con `moreLikeThis`, viene generata una query con i termini di ricerca estratti dal documento specificato che lo descrivono al meglio. La query generata viene quindi usata per eseguire la richiesta di ricerca. Per impostazione predefinita, viene considerato il contenuto di tutti i campi ricercabili, meno tutti i campi limitati specificati con il parametro `searchFields`. Il parametro `moreLikeThis` non può essere usato con il parametro di ricerca `search=[string]`.

Per impostazione predefinita, viene considerato il contenuto di tutti i campi ricercabili di primo livello. Se invece si desidera specificare campi specifici, è possibile utilizzare il parametro `searchFields`. 

Non è possibile usare moreLikeThis nei sottocampi disponibili per la ricerca in un [tipo complesso](search-howto-complex-data-types.md).

## <a name="examples"></a>esempi 

Di seguito è riportato un esempio di una query moreLikeThis. La query trova i documenti i cui campi di descrizione sono più simili al campo del documento di origine come specificato nel parametro `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Passaggi successivi

Per provare questa funzionalità, è possibile usare qualsiasi strumento di test Web.  Per questo esercizio, è consigliabile usare l'impostore.

> [!div class="nextstepaction"]
> [Esplora le API REST di Azure ricerca cognitiva usando il postore](search-get-started-postman.md)