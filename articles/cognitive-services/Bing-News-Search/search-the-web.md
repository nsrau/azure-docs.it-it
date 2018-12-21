---
title: Informazioni sull'API Ricerca notizie Bing
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare l'API Ricerca notizie Bing per la ricerca sul Web di notizie in diverse categorie, tra cui titoli e gli argomenti di tendenza.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: overview
ms.date: 06/21/2016
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 17383d38b1401149003ad9d1794b3e69284f9033
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253093"
---
# <a name="what-is-the-bing-news-search-api"></a>Informazioni sull'API Ricerca notizie Bing

L'API Ricerca notizie Bing offre un'esperienza simile (ma non uguale) a [Bing Notizie](https://www.bing.com/news). L'API Ricerca notizie Bing consente di inviare una query di ricerca a Bing e di visualizzare un elenco dei notizie pertinenti.

Se si sta compilando una pagina risultati di ricerca, comprendente solo notizie, per trovare notizie pertinenti alla query di ricerca dell'utente, chiamare quest'API, invece della più generica [API Ricerca Web Bing](../bing-web-search/search-the-web.md). Se oltre alle notizie si desiderano altri contenuti, come pagine Web, immagini e video, chiamare l'API Ricerca Web Bing.

## <a name="suggesting--using-search-terms"></a>Suggerimenti e uso dei termini di ricerca

Se si fornisce una casella di ricerca in cui l'utente immette il termine di ricerca, usare l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per migliorare l'esperienza. Mentre l'utente digita, l'API restituisce le stringhe di query suggerite, in base ai termini di ricerca parziali.

Dopo che l'utente ha immesso il termine di ricerca, l'URL lo codifica prima di impostare il parametro di query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query). Se ad esempio l'utente immette *sailing dinghies*, impostare `q` su `sailing+dinghies` o `sailing%20dinghies`.

## <a name="getting-general-news"></a>Ottenere notizie generali

Per ottenere dal Web articoli di notizie generali relativi al termine di ricerca dell'utente, inviare la richiesta GET seguente:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Se è la prima volta che si chiama un'API Bing, non includere l'intestazione dell'ID client. Includere l'ID client solo se in precedenza è già stata chiamata un'API Bing e Bing ha restituito un ID client per la combinazione utente e dispositivo.

Per ottenere notizie da un dominio specifico, usare l'operatore di query del [sito:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Di seguito è riportata la risposta alla query precedente. È necessario visualizzare ogni notizia nell'ordine indicato nella risposta. Se l'articolo ha articoli cluster, è necessario indicare che esistono articoli correlati e visualizzarli se l'utente li richiede.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

La risposta [notizie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#news) contiene un elenco di articoli di notizie considerati da Bing pertinenti alla query. Il campo `totalEstimatedMatches` contiene una stima del numero di articoli disponibili da visualizzare. Per informazioni sul paging attraverso gli articoli, consultare [Sfogliare le notizie](./paging-news.md).

Ogni [articolo di notizie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#newsarticle) nell'elenco include nome, descrizione e URL dell'articolo nel sito Web dell'host. Se l'articolo contiene un'immagine, l'oggetto include un'anteprima dell'immagine. Usare `name` e `url` per creare un collegamento ipertestuale che indirizzi l'utente all'articolo di notizie nel sito dell'host. Se l'articolo include un'immagine, rendere l'immagine selezionabile tramite `url`. Assicurarsi di usare `provider` per assegnare un attributo all'articolo.

Se Bing può determinare la categoria dell'articolo di notizie, l'articolo include il campo `category`.

## <a name="getting-todays-top-news"></a>Ottenere le notizie principali del giorno

Per ottenere i principali articoli di notizie del giorno, si effettua la stessa richiesta usata per ottenere le notizie generali ma lasciando `q` non impostato.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La risposta per ottenere notizie principali è quasi uguale a quella per le notizie generali. Tuttavia, il numero di risultati è già impostato, quindi la risposta `news` non include il campo `totalEstimatedMatches`. Il numero di articoli di notizie principali può variare a seconda del ciclo di notizie. Assicurarsi di usare `provider` per assegnare un attributo all'articolo.

## <a name="getting-news-by-category"></a>Ottenere le notizie per categoria

Per ottenere le notizie in base alla categoria, come i principali articoli su sport e intrattenimento, inviare la richiesta GET seguente a Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Usare la [categoria](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) parametro di query per specificare la categoria degli articoli. Per un elenco delle categorie di notizie specificabili, consultare [Categorie notizie in base al mercato](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-categories-by-market).

La risposta per ottenere notizie principali è quasi uguale a quella per le notizie generali. Tuttavia, gli articoli appartengono tutti alla categoria specificata.

## <a name="getting-headline-news"></a>Ottenere le notizie in prima pagina

Per richiedere gli articoli di notizie in prima pagina e ottenere gli articoli di tutte le categorie, inviare la richiesta GET seguente a Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Non includere la [categoria](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) parametro di query.

La risposta per ottenere le notizie in prima pagina è quasi uguale a quella per le notizie principali del giorno. Se si tratta di un articolo in prima pagina, il suo campo `headline` è impostato su **true**.

Per impostazione predefinita, la risposta include fino a 12 articoli in prima pagina. Per modificare il numero di articoli di intestazione da restituire, specificare il parametro di query [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headlinecount). La risposta include anche un massimo di quattro articoli non in prima pagina per ogni categoria di notizie.

La risposta conta i cluster come un singolo articolo. In un cluster possono esserci diversi articoli, perciò la risposta può includere più di 12 articoli in prima pagina e più di quattro articoli non in prima pagina per ogni categoria.

## <a name="getting-trending-news"></a>Ottenere le notizie di tendenza

Per ottenere notizie su argomenti che sono di tendenza nei social network, inviare la richiesta GET seguente a Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Trending Topics è disponibile solo nei mercati en-US e zh-CN.

Il seguente JSON indica la risposta alla richiesta precedente. Ogni articolo di notizie di tendenza include un'immagine correlata, flag delle ultime notizie e un URL ai risultati della ricerca Bing per l'articolo. Usare l'URL nel campo `webSearchUrl` per indirizzare l'utente alla pagina dei risultati della ricerca Bing. In alternativa, usare il testo della query per chiamare l'[API ricerca Web](../bing-web-search/search-the-web.md) e visualizzare i risultati autonomamente.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Ottenere notizie correlate

Se sono presenti altri articoli correlati a un articolo di notizie, la notizia potrebbe includere il campo [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle-clusteredarticles). L'esempio seguente illustra un articolo con articoli di cluster.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente con la prima richiesta, vedere [Making your first entities request](./quickstart.md) (Come effettuare la prima richiesta di entità).

Acquisire familiarità con la documentazione di riferimento sull'[API Ricerca notizie Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference). La documentazione di riferimento contiene l'elenco di endpoint, intestazioni e parametri di query da usare per richiedere i risultati della ricerca. Include anche le definizioni degli oggetti della risposta.

Per migliorare l'esperienza utente con la casella di ricerca, vedere [API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md). Quando l'utente immette i termini della query, è possibile chiamare questa API per ottenere i termini della query pertinenti usati da altri utenti.

Per non violare alcuna regola relativa all'uso dei risultati della ricerca, vedere [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Requisiti per l'uso e la visualizzazione di Bing).