---
title: "Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando Node.js | Microsoft Docs"
titleSuffix: Azure Cognitive Services
description: Seguire questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca all'istanza di Ricerca personalizzata Bing con Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b0fe87b199be81d5f79a8e9530c927e391e4ddf7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101766"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando Node.js

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Seguire questo argomento di avvio rapido per informazioni su come richiedere risultati della ricerca all'istanza di Ricerca personalizzata Bing. Anche se l'applicazione è scritta in JavaScript, l'API Ricerca personalizzata Bing è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza di Ricerca personalizzata Bing. Per altre informazioni, vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).

- [Il runtime JavaScript di Node.js](https://www.nodejs.org/).

- La [libreria di richieste JavaScript](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

- Creare un nuovo file JavaScript nell'IDE o nell'editor preferito e aggiungere un'istruzione `require()` per la libreria di richieste. Creare variabili per la chiave di sottoscrizione, l'ID configurazione personalizzato e un termine di ricerca.

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Inviare e ricevere una richiesta di ricerca 

1. Creare una variabile per archiviare le informazioni inviate nella richiesta. Creare l'URL della richiesta aggiungendo il termine di ricerca al parametro di query `q=` e l'ID configurazione personalizzato dell'istanza di ricerca al parametro `customconfig=`. Separare i parametri con una e commerciale (`&`). È possibile usare l'endpoint globale nel codice seguente o l'endpoint di [sottodominio personalizzato](../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Usare la libreria di richieste JavaScript per inviare una richiesta di ricerca all'istanza di Ricerca personalizzata Bing e stampare le informazioni sui risultati, inclusi il nome, l'URL e la data dell'ultima ricerca per indicizzazione nella pagina Web.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](./tutorials/custom-search-web-page.md)
