---
title: Invio di query all'API Ricerca immagini Bing | Microsoft Docs
description: Informazioni sull'invio e la personalizzazione delle query di ricerca inviate all'API Ricerca immagini Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082589"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>Invio di query all'API Ricerca immagini Bing

L'API Ricerca immagini Bing offre un'esperienza simile a quella di Bing.com/Images. Consente infatti di inviare una query di ricerca utente a Bing e di ottenere un elenco di immagini pertinenti.

## <a name="using-and-suggesting-search-terms"></a>Suggerimenti e uso dei termini di ricerca

Dopo avere immesso un termine di ricerca, l'URL codifica il termine prima di impostare il parametro di query [**q**](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Se, ad esempio, l'utente immette *sailing dinghies*, impostare `q` su `sailing+dinghies` o `sailing%20dinghies`.

Se l'app ha una casella di ricerca in cui immettere i termini di ricerca, è possibile usare l'[API Suggerimenti automatici Bing](../../bing-autosuggest/get-suggested-search-terms.md) per migliorare l'esperienza visualizzando i termini di ricerca suggeriti in tempo reale. Mentre l'utente digita, l'API restituisce le stringhe di query suggerite, in base ai termini di ricerca parziali e ai servizi cognitivi di Azure.

## <a name="pivoting-the-query"></a>Trasformazione tramite Pivot della query

Se Bing può segmentare la query di ricerca originale, l'oggetto [Immagini](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) restituito conterrà `pivotSuggestions`, visualizzabili come termini di ricerca facoltativi per l'utente. Se ad esempio la query originale fosse *Microsoft Surface*, Bing potrebbe segmentare la query in *Microsoft* e *Surface* o fornire pivot suggeriti per ciascuno. Questi possono essere visualizzati come termini facoltativi della query per l'utente.

L'esempio seguente illustra i suggerimenti di pivot per *Microsoft Surface*:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Il campo `pivotSuggestions` contiene l'elenco di segmenti (pivot) in cui è stata suddivisa la query originale. Per ogni pivot, la risposta contiene un elenco di oggetti [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) contenenti le query suggerite. Il campo `text` contiene la query suggerita e il campo `displayText` contiene il termine che sostituisce il pivot nella query originale, ad esempio la data di rilascio di Surface.

È possibile usare i campi `text` e `thumbnail` per mostrare all'utente le stringhe di query dei pivot qualora la stringa di query del pivot sia effettivamente ciò che sta cercando. Rendere selezionabile l'anteprima e il testo con l'URL `webSearchUrl` o con l'URL `searchLink`. Usare `webSearchUrl` per inviare l'utente ai risultati della ricerca di Bing o `searchLink` se si fornisce la propria pagina di risultati.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>Espansione della query

Se Bing può espandere la query per restringere la ricerca originale, l'oggetto [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contiene il campo `queryExpansions`. Se ad esempio la query fosse *Microsoft Surface*, le query espanse potrebbero essere: Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** e Microsoft Surface **Hub**.

L'esempio seguente illustra le query espanse per *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Il campo `queryExpansions` contiene un elenco di oggetto [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj). Il campo `text` contiene la query espansa e il campo `displayText` contiene il termine di espansione. È possibile usare i campi `text` e `thumbnail` per mostrare all'utente le stringhe di query espanse qualora la stringa di query espansa sia effettivamente ciò che sta cercando. Rendere selezionabile l'anteprima e il testo con l'URL `webSearchUrl` o con l'URL `searchLink`. Usare `webSearchUrl` per inviare l'utente ai risultati della ricerca di Bing o `searchLink` se si fornisce la propria pagina di risultati.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

Se non si è ancora provato l'API Ricerca immagini Bing, consultare una [Guida introduttiva](../quickstarts/csharp.md). Se si sta cercando un elemento più complesso, provare l'esercitazione per la creazione di un'[app web a pagina singola](../tutorial-bing-image-search-single-page-app.md).
