---
title: Invio di richieste all'API Suggerimenti automatici Bing
titleSuffix: Azure Cognitive Services
description: L'API Suggerimenti automatici Bing restituisce un elenco di query suggerite basate sulla stringa di query parziale immessa nella casella di ricerca. Altre informazioni sull'invio di richieste.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dd845c0fb877afa76b84eb5c2d86392f763eccf7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353392"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Invio di richieste all'API Suggerimenti automatici Bing.

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Se l'applicazione invia query a una delle API Ricerca Bing, è possibile usare l'API Suggerimenti automatici Bing per migliorare l'esperienza di ricerca degli utenti. L'API Suggerimenti automatici Bing restituisce un elenco di query suggerite basate sulla stringa di query parziale immessa nella casella di ricerca. Quando i caratteri vengono immessi in una casella di ricerca nell'applicazione, è possibile visualizzare i suggerimenti in un elenco a discesa. Questo articolo contiene informazioni approfondite sull'invio di richieste a questa API. 

## <a name="bing-autosuggest-api-endpoint"></a>Endpoint dell'API Suggerimenti automatici Bing

L'**API Suggerimenti automatici Bing** include un endpoint che restituisce un elenco di query suggerite da un termine di ricerca parziale.

Per ottenere query suggerite tramite l'API Bing, inviare una richiesta `GET` all'endpoint seguente. Usare le intestazioni e i parametri URL per definire altre specifiche.

**Endpoint:** restituisce suggerimenti per la ricerca come risultati JSON pertinenti per l'input dell'utente definito da `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Per maggiori dettagli su intestazioni, parametri, codici di mercato, oggetti risposta, errori e altro ancora, vedere il riferimento per l'[API Suggerimenti automatici Bing v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference).

Le API **Bing** supportano azioni di ricerca che restituiscono risultati in base al tipo. Tutti gli endpoint di ricerca restituiscono risultati come oggetti della risposta JSON.
Tutti gli endpoint supportano query che restituiscono una posizione e/o una lingua specifica in base a longitudine, latitudine e raggio di ricerca.

Per informazioni complete sui parametri supportati da ogni endpoint, vedere le pagine di riferimento per ogni tipo.
Per esempi di richieste di base con l'API Suggerimenti automatici, vedere [Guide introduttive a Suggerimenti automatici](/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Richieste all'API Suggerimenti automatici Bing

> [!NOTE]
> * Le richieste all'API Suggerimenti automatici Bing devono usare il protocollo HTTPS.

È consigliabile che tutte le richieste abbiano origine da un server. La distribuzione della chiave come parte di un'applicazione client consente a terze parti dannose di accedervi più facilmente. Inoltre, l'esecuzione delle chiamate da un server consente di usare un solo punto di aggiornamento per i futuri aggiornamenti.

La richiesta deve specificare il parametro di query [q](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), contenente il termine di ricerca parziale dell'utente. Nonostante sia facoltativo, la richiesta dovrebbe anche specificare il parametro di query [mkt](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), che identifica il mercato da cui devono provenire i risultati. Per un elenco di parametri di query facoltativi, vedere [parametri di query](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Tutti i valori dei parametri di query devono essere codificati in URL.

La richiesta deve specificare l'intestazione [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Nonostante sia facoltativo, è consigliabile specificare anche le intestazioni seguenti:

- [User-Agent](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-ricerca-percorso](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Le intestazioni relative a IP client e posizione sono importanti per la restituzione di contenuto con riconoscimento della posizione.

Per un elenco di tutte le intestazioni di richiesta e risposta, vedere [Headers](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers) (Intestazioni).

> [!NOTE]
> Quando si chiama l'API Suggerimenti automatici Bing da JavaScript, le funzionalità di sicurezza predefinite del browser possono impedire l'accesso ai valori di queste intestazioni.

Per risolvere il problema, è possibile inviare la richiesta all'API Suggerimenti automatici Bing tramite un proxy CORS. La risposta da un proxy di questo tipo ha un' `Access-Control-Expose-Headers` intestazione che filtra le intestazioni di risposta e le rende disponibili a JavaScript.

Si può installare facilmente un proxy CORS per consentire all'[app dell'esercitazione](../tutorials/autosuggest.md) di accedere alle intestazioni client facoltative. Per prima cosa [installare Node.js](https://nodejs.org/en/download/), se non è già disponibile. Immettere quindi il comando seguente al prompt dei comandi.

```console
npm install -g cors-proxy-server
```

Modificare quindi l'endpoint dell'API Suggerimenti automatici Bing nel file HTML come segue:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Infine avviare il proxy CORS con il comando seguente:

```console
cors-proxy-server
```

Lasciare aperta la finestra di comando mentre si usa l'app dell'esercitazione. La chiusura della finestra determina l'arresto del proxy. Nella sezione Intestazioni HTTP espandibile sotto i risultati della ricerca è ora possibile visualizzare l'intestazione `X-MSEdge-ClientID` (tra le altre) e verificare che sia la stessa per ogni richiesta.

Le richieste devono includere tutte le intestazioni e tutti i parametri di query suggeriti. 

L'esempio seguente illustra una richiesta che restituisce le stringhe di query suggerite per *sail*.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Se è la prima volta che si chiama un'API Bing, non includere l'intestazione dell'ID client. Includere l'intestazione dell'ID client solo se in precedenza è già stata chiamata un'API Bing e Bing ha restituito un ID client per la combinazione utente e dispositivo.

Il gruppo di suggerimento Web seguente è una risposta alla richiesta precedente. Il gruppo contiene un elenco di suggerimenti di query di ricerca, con ogni suggerimento che include un campo `displayText`, `query` e `url`.

Il campo `displayText` contiene la query suggerita che si userebbe per popolare l'elenco a discesa della casella di ricerca. È necessario visualizzare tutti i suggerimenti inclusi nella risposta, nell'ordine indicato.  

Se l'utente seleziona una query dall'elenco a discesa, è possibile usarla per chiamare una delle [API di ricerca Bing](../../bing-web-search/bing-api-comparison.md?bc=%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fen-us%2fazure%2fcognitive-services%2fbing-autosuggest%2ftoc.json) e visualizzare autonomamente i risultati. In alternativa, è possibile indirizzare l'utente alla pagina dei risultati di Bing tramite il campo `url` restituito.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
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

- [Informazioni su Suggerimenti automatici Bing](../get-suggested-search-terms.md)
- [Riferimento sull'API Suggerimenti automatici Bing versione 7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Ottenere termini di ricerca suggeriti dall'API Suggerimenti automatici Bing](get-suggestions.md)