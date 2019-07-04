---
title: Invio di richieste di ricerca all'API Ricerca video Bing
titlesuffix: Azure Cognitive Services
description: Informazioni su come inviare query di ricerca all'API Ricerca video Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/19/2019
ms.author: aahi
ms.openlocfilehash: 71d7ebb8cb797e2166fc91fdfdefdba5f22d82ec
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272691"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Cercare video con l'API Ricerca video Bing

L'API Ricerca video Bing facilita l'integrazione delle funzionalità di ricerca notizie cognitive di Bing nelle applicazioni. Oltre a trovare e restituire principalmente i video pertinenti dal Web, l'API offre diverse funzionalità per il recupero intelligente e mirato dei video sul Web.

## <a name="getting-videos"></a>Come ottenere video

Per ottenere dal Web video relativi al termine di ricerca dell'utente, inviare la seguente richiesta GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Tutte le richieste devono essere eseguite da un server.

Se è la prima volta che si chiama un'API Bing, non includere l'intestazione dell'ID client. Includere l'ID client solo se in precedenza è già stata chiamata un'API Bing e Bing ha restituito un ID client per la combinazione utente e dispositivo.

Per ottenere video da un dominio specifico, usare il [sito:](https://msdn.microsoft.com/library/ff795613.aspx) operatore query.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Il risultato contiene una risposta [Video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) che riporta un elenco di video che Bing ha ritenuto pertinenti alla query. Ogni oggetto [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) nell'elenco include l'URL, la durata, le dimensioni e il formato di codifica del video, tra gli altri attributi. L'oggetto video include anche l'URL di un'anteprima del video e le dimensioni dell'anteprima.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Anteprime video

È possibile visualizzare tutte le anteprime video restituite dall'API Ricerca video Bing oppure solo un sottoinsieme. Se l'utente visualizza un subset, avrà la possibilità di vedere i video rimanenti. Come parte dei [requisiti per l'uso e la visualizzazione](../UseAndDisplayRequirements.md) delle API Bing, è necessario visualizzare i video nell'ordine indicato nella risposta. Per informazioni sul ridimensionamento dell'anteprima, vedere [Resizing and Cropping Thumbnails](../resize-and-crop-thumbnails.md) (Ridimensionamento e ritaglio di anteprime). 

L'utente, passando con il mouse sull'anteprima, potrà usare [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) per riprodurre una versione di anteprima del video. Assicurarsi di assegnare un attributo all'anteprima animata quando la si visualizza.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Quando si fa clic su un'anteprima, sono disponibili tre opzioni per la visualizzazione del video:

- Usare [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) per visualizzare il video sul sito Web host (ad esempio YouTube)
- Usare [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) per visualizzare il video nel browser video di Bing
- Usare [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) per incorporare il video nell'esperienza dell'utente 

Assicurarsi di usare l'editore e l'autore per attribuire il video durante la riproduzione.

Per informazioni dettagliate sull'uso di [videoId](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid), consultare la sezione [Informazioni dettagliate sui video](../video-insights.md).

## <a name="filtering-videos"></a>Come filtrare i video

Per impostazione predefinita, l'API Ricerca video riporta tutti i video pertinenti alla query. Se si ricercano solo video gratuiti o video di lunghezza inferiore a cinque minuti, è possibile utilizzare i seguenti parametri di filtro per la query:

- [prezzi](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing)&mdash;Filtra i video in base al prezzo (ad esempio, video gratuiti o video a pagamento)
- [risoluzione](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution)&mdash;Filtra i video in base alla risoluzione (ad esempio, video con risoluzione pari a 720p o superiore)
- [lunghezzavideo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength)&mdash;Filtra i video in base alla lunghezza (ad esempio, video che durano meno di 5 minuti)
- [aggiornamento](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness)&mdash;Filtra i video in base alla data della ricerca (ad esempio, video individuati da Bing la scorsa settimana)

Per ottenere risultati di ricerca da un dominio specifico, includere il [sito:](https://msdn.microsoft.com/library/ff795613.aspx) dell'operatore query, nella stringa di query.

> [!NOTE]
> A seconda della query, se si usa `site:`l'operatore query è possibile che i risultati contengano contenuti per adulti, indipendentemente dall'impostazione [Ricerca sicura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch). È consigliabile usare `site:` solo se si conosce il contenuto del sito e lo scenario prevede la possibilità di contenuto per adulti.

Nel seguente esempio viene illustrato come ottenere video gratuiti da ContosoSailing.com, con una risoluzione pari a 720p o superiore e che sono stati individuati da Bing nell'ultimo mese.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Espansione della query

Se Bing può espandere la query per circoscrivere la ricerca originale, significa che l'oggetto [Video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contiene il campo `queryExpansions`. Ad esempio, se la query fosse *Pulizia dei margini*, le query espanse potrebbero essere: **Strumenti** di pulizia dei margini, pulizia dei margini **dal suolo**, **computer** di pulizia dei margini e pulizia dei margini **facile**.

Il seguente esempio mostra le query espanse per *Pulizia dei margini*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

Il campo `queryExpansions` contiene un elenco di oggetto [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj). Il campo `text` contiene la query espansa e il campo `displayText` contiene il termine di espansione. È possibile usare i campi testo e anteprima per mostrare all'utente le stringhe di query espanse, qualora la stringa di query espansa sia effettivamente ciò che si sta cercando. Rendere selezionabile l'anteprima e il testo con l'URL `webSearchUrl` o con l'URL `searchLink`. Usare `webSearchUrl` per inviare l'utente ai risultati della ricerca di Bing o `searchLink` se si fornisce la propria pagina di risultati.

## <a name="pivoting-the-query"></a>Trasformazione tramite Pivot della query

Se Bing può segmentare la query di ricerca originale, significa che l'oggetto [Video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contiene il campo `pivotSuggestions`. Se ad esempio la query originale fosse *Pulizia dei margini*, Bing potrebbe segmentare la query in *Pulizia* e *Margini*.

Il seguente esempio mostra i suggerimenti di pivot per *Pulizia dei margini*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Per ogni pivot, la risposta contiene un elenco di oggetti [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) contenenti le query suggerite. Il campo `text` contiene la query suggerita e il campo `displayText` contiene il termine che sostituisce il pivot nella query originale, Ad esempio, pulizia delle finestre.

È possibile usare i campi `text` e `thumbnail` per mostrare all'utente le stringhe di query espanse qualora la stringa di query espansa sia effettivamente ciò che sta cercando. Rendere selezionabile l'anteprima e il testo con l'URL `webSearchUrl` o con l'URL `searchLink`. Usare `webSearchUrl` per inviare l'utente ai risultati della ricerca di Bing o `searchLink` se si fornisce la propria pagina di risultati.

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
