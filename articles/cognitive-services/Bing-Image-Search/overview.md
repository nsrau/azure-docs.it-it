---
title: Informazioni su Ricerca immagini Bing | Microsoft Docs
description: Informazioni su come usare l'API Ricerca immagini Bing per cercare immagini nel Web.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336520"
---
# <a name="what-is-bing-image-search"></a>Informazioni su Ricerca immagini Bing

L'API Ricerca immagini Bing offre un'esperienza simile a quella di [Immagini di Bing](https://www.bing.com/images). Consente infatti di inviare una query di ricerca utente a Bing e di ottenere un elenco di immagini pertinenti.

Se si deve compilare una pagina di risultati della ricerca comprendente solo immagini per trovare le immagini pertinenti alla query di ricerca dell'utente, chiamare questa API invece della più generica [API Ricerca Web](../bing-web-search/search-the-web.md). Se oltre alle immagini sono necessari altri tipi di contenuto, ad esempio pagine Web, notizie e video, chiamare l'API Ricerca Web.

## <a name="suggesting--using-search-terms"></a>Suggerimenti e uso dei termini di ricerca

Se si fornisce una casella di ricerca in cui l'utente immette il termine di ricerca, usare l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per migliorare l'esperienza. Mentre l'utente digita, l'API restituisce le stringhe di query suggerite, in base ai termini di ricerca parziali.

Dopo che l'utente ha immesso il termine di ricerca, l'URL codifica il termine prima di impostare il parametro di query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Se ad esempio l'utente immette *sailing dinghies*, impostare `q` su `sailing+dinghies` o `sailing%20dinghies`.

## <a name="getting-images"></a>Acquisizione delle immagini

Per ottenere dal Web le immagini relative al termine di ricerca dell'utente, inviare la richiesta GET seguente:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Tutte le richieste devono essere eseguite da un server. Non è possibile effettuare chiamate da un client.

Se è la prima volta che si chiama un'API Bing, non includere l'intestazione dell'ID client. Includere l'ID client solo se in precedenza è già stata chiamata un'API Bing e Bing ha restituito un ID client per la combinazione utente e dispositivo.

Per ottenere immagini da un dominio specifico, usare l'operatore query [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

La risposta contiene una risposta [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contenente un elenco di immagini considerate da Bing pertinenti alla query. Ogni oggetto [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) dell'elenco include l'URL, le dimensioni del file, le dimensioni fisiche e il formato di codifica dell'immagine. L'oggetto immagine include anche l'URL di un'anteprima dell'immagine e le dimensioni dell'anteprima.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

È possibile visualizzare un collage delle anteprime delle immagini oppure un subset delle anteprime. Se si visualizza un subset, offrire all'utente la possibilità di vedere le immagini rimanenti. È necessario visualizzare le immagini nell'ordine indicato nella risposta.

È anche possibile espandere le anteprime quando l'utente vi passa sopra il cursore. Se si espande l'immagine, assicurarsi di assegnarle un attributo, ad esempio estraendo l'host da [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) e visualizzandolo sotto l'immagine. Per informazioni sul ridimensionamento dell'anteprima, vedere [Resizing and Cropping Thumbnails](./resize-and-crop-thumbnails.md) (Ridimensionamento e ritaglio di anteprime).

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se l'utente fa clic sull'anteprima, è possibile usare [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) per consentire all'utente di visualizzare l'immagine con dimensioni normali. Assicurarsi di assegnare un attributo all'immagine.

Se `shoppingSourcesCount` o `recipeSourcesCount` sono maggiori di zero, aggiungere all'anteprima un badge, ad esempio un carrello, per indicare che per l'elemento nell'immagine esiste la possibilità di acquisto o una ricetta.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Per ottenere informazioni dettagliate sull'immagine, ad esempio le pagine Web che includono l'immagine o le persone riconosciute nell'immagine, usare [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Per informazioni dettagliate, vedere [Informazioni dettagliate sulle immagini](./image-insights.md).

## <a name="filtering-images"></a>Applicazione di filtri alle immagini

 Per impostazione predefinita, l'API Ricerca immagini Bing restituisce tutte le immagini pertinenti alla query, ma, se sono necessarie solo immagini con uno sfondo trasparente o immagini di una dimensione specifica, è consigliabile usare i parametri di query seguenti per filtrare le immagini restituite da Bing.  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect): filtra le immagini in base alle proporzioni (ad esempio, immagini standard o widescreen)
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color): filtra le immagini in base al colore dominante o al bianco e nero
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness): filtra le immagini in base alla durata (ad esempio, immagini individuate da Bing la scorsa settimana)
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width): filtrano le immagini in base alla larghezza e all'altezza
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent): filtra le immagini in base al contenuto (ad esempio, immagini che mostrano solo il viso di una persona)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype): filtra le immagini in base al tipo (ad esempio, ClipArt, GIF animate o sfondi trasparenti)
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license): filtra le immagini in base al tipo di licenza associata al sito
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size): filtra le immagini in base alla dimensione, ad esempio immagini piccole fino a 200x200 pixel

Per ottenere immagini da un dominio specifico, usare l'operatore query [site:](http://msdn.microsoft.com/library/ff795613.aspx).

> [!NOTE]
> A seconda della query, se si usa l'operatore `site:`, è possibile che la risposta contenga contenuto per adulti indipendentemente dall'impostazione di [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch). È consigliabile usare `site:` solo se si è a conoscenza del contenuto del sito e lo scenario prevede la possibilità di contenuto per adulti.

L'esempio seguente illustra come ottenere immagini di piccole dimensioni da ContosoSailing.com individuate da Bing la scorsa settimana.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>Trasformazione tramite Pivot della query

Se Bing può segmentare la query di ricerca originale, l'oggetto [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contiene il campo `pivotSuggestions`. Se ad esempio la query originale fosse *Microsoft Surface*, Bing potrebbe segmentare la query in *Microsoft* e *Surface*.  

L'esempio seguente illustra i suggerimenti di pivot per *Microsoft Surface*.  

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

Sarebbe quindi possibile mostrare all'utente i termini di query facoltativi qualora siano di interesse per l'utente.

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente con la prima richiesta, vedere la [guida introduttiva per C#](quickstarts/csharp.md).

Acquisire familiarità con la documentazione di riferimento sull'[API Ricerca immagini Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference). La documentazione di riferimento contiene l'elenco di endpoint, intestazioni e parametri di query da usare per richiedere i risultati della ricerca. Include anche le definizioni degli oggetti della risposta.

Per migliorare l'esperienza utente con la casella di ricerca, vedere [API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md). Quando l'utente immette i termini della query, è possibile chiamare questa API per ottenere i termini della query pertinenti usati da altri utenti.

Per non violare nessuna delle regole relative all'uso dei risultati della ricerca, vedere [Bing Use and Display Requirements (Requisiti per l'uso e la visualizzazione di Bing)](./useanddisplayrequirements.md).

Quando si chiama l'API Ricerca immagini Bing, Bing restituisce un elenco di risultati. L'elenco è un subset del numero totale di risultati pertinenti alla query. Il campo `totalEstimatedMatches` della risposta contiene una stima del numero di immagini disponibili da visualizzare. Per informazioni dettagliate su come Sfogliare le immagini rimanenti, vedere [Paging Images (Paging di immagini)](./paging-images.md).