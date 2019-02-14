---
title: Informazioni sui Suggerimenti automatici Bing
titlesuffix: Azure Cognitive Services
description: Imparare a usare l'API Suggerimenti automatici Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: b5959e014b7e531b8f52fcbe6f6492576eedd61a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875672"
---
# <a name="what-is-bing-autosuggest"></a>Informazioni sui Suggerimenti automatici Bing

Se si inviano query a una delle API di ricerca Bing, è possibile usare l'API Suggerimenti automatici Bing per migliorare l'esperienza con la casella di ricerca. L'API Suggerimenti automatici Bing restituisce un elenco di query suggerite basate sulla stringa di query parziale che l'utente immette nella casella di ricerca. Visualizzare i suggerimenti riportati nell'elenco a discesa della casella di ricerca. Termini suggeriti basati su query suggerite che altri utenti hanno ricercato e su finalità dell'utente.

Tipicamente, si chiamerà questa API ogni volta che l'utente digita un nuovo carattere nella casella di ricerca. La completezza della stringa di query influisce sulla pertinenza dei termini di query suggeriti che vengono restituiti dall'API. Più completa è la stringa di query, più rilevante è l'elenco dei termini di query suggeriti. Ad esempio, i suggerimenti che l'API può restituire per *s* sono probabilmente meno pertinenti di quelli restituiti dalla query per *sailing dinghies*.

## <a name="getting-suggested-search-terms"></a>Recuperare i termini di ricerca suggeriti

L'esempio seguente illustra una richiesta che restituisce le stringhe di query suggerite per *sail*. Ricordarsi di codificare nell'URL il termine della query parziale dell'utente quando si imposta il parametro della query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query). Se ad esempio l'utente ha immesso *sailing les*, impostare `q` su `sailing+les` o `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La risposta seguente contiene un elenco di oggetti [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) contenenti i termini della query suggerita.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

Ogni suggerimento include i campi `displayText`, `query` e `url`. Il campo `displayText` contiene la query suggerita che si usa per popolare l'elenco a discesa della casella di ricerca. È necessario visualizzare tutti i suggerimenti inclusi nella risposta, nell'ordine indicato.

Di seguito viene illustrato un esempio di casella di ricerca con elenco a discesa contenente i termini di query suggeriti.

![Elenco di casella di ricerca con elenco a discesa di suggerimenti automatici](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Se l'utente seleziona una query suggerita dall'elenco a discesa, è possibile usare il termine di query nel campo `query` per chiamare l'[API Ricerca Web Bing](../bing-web-search/search-the-web.md) e visualizzare i risultati. Oppure è possibile usare l'URL nel campo `url` per inviare l'utente alla pagina dei risultati della ricerca di Bing.

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente con la prima richiesta, vedere [Effettuare la prima query](quickstarts/csharp.md).

Acquisire familiarità con la documentazione di riferimento sull'[API Suggerimenti automatici Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). La documentazione di riferimento contiene l'elenco di endpoint, intestazioni e parametri di query da usare per richiedere i termini di query suggeriti e le definizioni degli oggetti della risposta.

Informazioni su come effettuare ricerche sul Web con l'[API Ricerca Web Bing](../bing-web-search/search-the-web.md).

Per non violare nessuna delle regole relative all'uso dei risultati della ricerca, vedere [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Requisiti per l'uso e la visualizzazione di Bing).
