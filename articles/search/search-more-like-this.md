---
title: Anteprima di moreLikeThis in Ricerca di Azure - Ricerca di Azure
description: Documentazione preliminare per la funzionalità moreLikeThis in anteprima esposta nell'API REST di Ricerca di Azure.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7c816c545c6647907aa9d700a4eb6ed91277465
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182318"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis in ricerca di Azure

> [!Note]
> moreLikeThis è in anteprima e non è destinato all'uso in produzione. Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Non è attualmente disponibile alcun supporto di .NET SDK.

`moreLikeThis=[key]`è un parametro di query nell' [API cerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents) che consente di trovare documenti simili al documento specificato dalla chiave del documento. Quando viene effettuata una richiesta di ricerca con `moreLikeThis`, viene generata una query con i termini di ricerca estratti dal documento specificato che lo descrivono al meglio. La query generata viene quindi usata per eseguire la richiesta di ricerca. Per impostazione predefinita, viene considerato il contenuto di tutti i campi ricercabili, meno tutti i campi limitati specificati usando `searchFields` il parametro. Il parametro `moreLikeThis` non può essere usato con il parametro di ricerca `search=[string]`.

Per impostazione predefinita, viene considerato il contenuto di tutti i campi ricercabili di primo livello. Se invece si desidera specificare campi specifici, è possibile utilizzare il `searchFields` parametro. 

Non è possibile usare moreLikeThis nei sottocampi disponibili per la ricerca in un [tipo complesso](search-howto-complex-data-types.md).

## <a name="examples"></a>Esempi 

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
> [Esplorare le API REST di ricerca di Azure con il post](search-get-started-postman.md)