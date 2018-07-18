---
title: Avvio rapido per l'API Ricerca notizie | Microsoft Docs
description: Illustra come iniziare a usare l'API Ricerca notizie Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 9CF6EAF3-42D8-4321-983C-4AC3896E8E03
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2aa1b3de07bd61eebfc1d410cda76c32fc7c958e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377385"
---
# <a name="your-first-news-search-query"></a>La prima query di ricerca notizie

Prima di poter effettuare la prima chiamata, è necessario ottenere una chiave di sottoscrizione di Servizi cognitivi. Per ottenere una chiave, vedere [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api).

Per ottenere solo le notizie come risultati della ricerca, è consigliabile inviare una richiesta GET all'endpoint seguente:

```http
https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

La richiesta deve usare il protocollo HTTPS.

È consigliabile che tutte le richieste abbiano origine da un server. La distribuzione della chiave come parte di un'applicazione client consente a terze parti dannose di accedervi più facilmente. Inoltre, l'esecuzione delle chiamate da un server fornisce un singolo punto di aggiornamento per le versioni future dell'API.

La richiesta deve specificare il parametro di query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query), contenente il termine di ricerca dell'utente. Nonostante sia facoltativo, la richiesta dovrebbe anche specificare il parametro di query [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#mkt), che identifica il mercato da cui devono provenire i risultati. Per un elenco di parametri di query facoltativi come `freshness` e `textDecorations`, vedere [Query Parameters](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters) (Parametri di query). Tutti i valori dei parametri di query devono essere codificati in URL.

La richiesta deve specificare l'intestazione [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#subscriptionkey). Nonostante sia facoltativo, è consigliabile specificare anche le intestazioni seguenti:

- [User-agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#location)

Le intestazioni relative a IP client e posizione sono importanti per la restituzione di contenuto con riconoscimento della posizione.

Per un elenco di tutte le intestazioni di richiesta e risposta, vedere [Headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headers) (Intestazioni).

## <a name="the-request"></a>Richiesta

Di seguito è illustrata una richiesta di notizie che include tutte le intestazioni e i parametri di query suggeriti. Se è la prima volta che si chiama un'API Bing, non includere l'intestazione dell'ID client. Includere l'ID client solo se in precedenza è già stata chiamata un'API Bing e Bing ha restituito un ID client per la combinazione utente e dispositivo.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="the-response"></a>Risposta

Di seguito è riportata la risposta alla richiesta precedente. L'esempio mostra anche le intestazioni di risposta specifiche di Bing.

```http
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "Sailing College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },
    {
        "name" : "Reunion at Fabrikam Lakes Sailing Club, celebrates 50 years...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.38210...",
                "width" : 650,
                "height" : 366
            }
        },
        "description" : "The reunion on April 29, at Fabrikam Lakes Sailing...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T13:08:00"
    },
    {
        "name" : "Sailing Club to host Dinghy Sailing World...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.364AB41...",
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
    },
    {
        "name" : "A 24-Carat Dinghy",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.6CC2...",
                "width" : 700,
                "height" : 466
            }
        },
        "description" : "“Hard dinghies are for purists, the kind of people who...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-03T12:14:00",
        "category" : "Politics"
    }]
}
```

## <a name="next-steps"></a>Passaggi successivi

Provare l'API. Passare a [News Search API Testing Console](https://dev.cognitive.microsoft.com/docs/services/56b43f72cf5ff8098cef380a/operations/56f02400dbe2d91900c68553) (Console di test dell'API Ricerca notizie Bing).

Per informazioni dettagliate sull'utilizzo degli oggetti della risposta, vedere [What is Bing News Search?](./search-the-web.md) (Che cos'è Ricerca notizie Bing?). Sono inoltre disponibili altre informazioni sulle azioni comuni seguenti:

- [Ottenere le notizie principali del giorno](./search-the-web.md#getting-todays-top-news)
- [Ottenere le notizie per categoria](./search-the-web.md#getting-news-by-category)
- [Ottenere le notizie di tendenza](./search-the-web.md#getting-trending-news)
