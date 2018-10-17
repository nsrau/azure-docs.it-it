---
title: 'Guida introduttiva: API Suggerimenti automatici Bing'
titlesuffix: Azure Cognitive Services
description: Illustra come iniziare a usare l'API Suggerimenti automatici Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: quickstart
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 446d271854a4e45bcea8c261a0dc078e549f8229
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830754"
---
# <a name="quickstart-making-your-first-autosuggest-query"></a>Guida introduttiva: Creazione della prima query di Suggerimenti automatici

Prima di poter effettuare la prima chiamata, è necessario ottenere una chiave di sottoscrizione di Servizi cognitivi. Per ottenere una chiave, vedere [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

Per ottenere risultati della ricerca Web, è consigliabile inviare una richiesta GET all'endpoint seguente:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> Endpoint di anteprima V7:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

La richiesta deve usare il protocollo HTTPS.

È consigliabile che tutte le richieste abbiano origine da un server. La distribuzione della chiave come parte di un'applicazione client consente a terze parti dannose di accedervi più facilmente. L'esecuzione delle chiamate da un server fornisce anche un singolo punto di aggiornamento per le future versioni dell'API.

La richiesta deve specificare il parametro di query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), contenente il termine di ricerca parziale dell'utente. Nonostante sia facoltativo, la richiesta dovrebbe anche specificare il parametro di query [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), che identifica il mercato da cui devono provenire i risultati. Per un elenco di parametri di query facoltativi, vedere [Query Parameters](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters) (Parametri di query). Tutti i valori dei parametri di query devono essere codificati in URL.

La richiesta deve specificare l'intestazione [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Nonostante sia facoltativo, è consigliabile specificare anche le intestazioni seguenti:

- [User-agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Le intestazioni relative a IP client e posizione sono importanti per la restituzione di contenuto con riconoscimento della posizione.

Per un elenco di tutte le intestazioni di richiesta e risposta, vedere [Headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers) (Intestazioni).

## <a name="the-request"></a>Richiesta

La richiesta deve includere tutte le intestazioni e i parametri di query suggeriti. Si chiamerà questa API ogni volta che l'utente digita un nuovo carattere nella casella di ricerca. La completezza della stringa di query influisce sulla pertinenza dei termini di query suggeriti che vengono restituiti dall'API. Più completa è la stringa di query, più rilevante è l'elenco dei termini di query suggeriti. Ad esempio, i suggerimenti che l'API può restituire per *s* sono probabilmente meno pertinenti di quelli restituiti dalla query per *sailing dinghies*. 

L'esempio seguente illustra una richiesta che restituisce le stringhe di query suggerite per *sail*.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Richiesta di anteprima V7:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Se è la prima volta che si chiama un'API Bing, non includere l'intestazione dell'ID client. Includere l'intestazione dell'ID client solo se in precedenza è già stata chiamata un'API Bing e Bing ha restituito un ID client per la combinazione utente e dispositivo.

Di seguito è indicata la risposta alla richiesta precedente. La risposta include un gruppo di suggerimenti Web che contiene un elenco di suggerimenti di query di ricerca. Ogni suggerimento include un campo `displayText`, `query` e `url`.

Il campo `displayText` contiene la query suggerita che si userebbe per popolare l'elenco a discesa della casella di ricerca. È necessario visualizzare tutti i suggerimenti inclusi nella risposta, nell'ordine indicato.  

Se l'utente seleziona una query dall'elenco a discesa, è possibile usare la stringa di query nel campo `query` per chiamare l'[API di ricerca Bing](../bing-web-search/search-the-web.md) e visualizzare i risultati oppure è possibile usare l'URL nel campo `url` per inviare l'utente alla pagina dei risultati della ricerca di Bing.

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Passaggi successivi

Provare l'API. Andare alla [console di test dell'API Suggerimenti automatici](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

Per informazioni dettagliate sull'utilizzo degli oggetti della risposta, vedere [Getting Suggested Search Terms (Recupero dei termini di ricerca suggeriti)](./get-suggested-search-terms.md).
