---
title: Anteprima di moreLikeThis in Ricerca di Azure - Ricerca di Azure
description: Documentazione preliminare per la funzionalità moreLikeThis in anteprima esposta nell'API REST di Ricerca di Azure.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d18069335bb20f78a5bcda85eb6fcb2a5abe75f7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024679"
---
# <a name="morelikethis-in-azure-search-preview"></a>Anteprima di moreLikeThis in Ricerca di Azure

`moreLikeThis=[key]` è un parametro di query nella [API di ricerca di documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents) che trova documenti simili al documento specificato dalla chiave del documento. Quando viene effettuata una richiesta di ricerca con `moreLikeThis`, viene generata una query con i termini di ricerca estratti dal documento specificato che lo descrivono al meglio. La query generata viene quindi usata per eseguire la richiesta di ricerca. Per impostazione predefinita, è considerato il contenuto di tutti i campi ricercabili, meno tutti i campi con restrizioni che è specificato utilizzando il `searchFields` parametro. Il parametro `moreLikeThis` non può essere usato con il parametro di ricerca `search=[string]`.

Per impostazione predefinita, il contenuto di tutti i campi ricercabili livello superiore è considerato. Se si desidera specificare quali campi specifici in alternativa, è possibile usare il `searchFields` parametro. 

> [!NOTE]
> `moreLikeThis` anteprima non funziona in campi secondari disponibili per la ricerca in un [tipo complesso](search-howto-complex-data-types.md).

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

## <a name="feature-availability"></a>Disponibilità delle funzionalità

Il `moreLikeThis` parametro è disponibile solo in anteprima le API REST (`api-version=2019-05-06-Preview`).

## <a name="next-steps"></a>Passaggi successivi

È possibile usare qualsiasi strumento di test web per sperimentare questa funzionalità.  È consigliabile usare Postman per questo esercizio.

> [!div class="nextstepaction"]
> [Esplorare le API REST di ricerca di Azure con Postman](search-fiddler.md)