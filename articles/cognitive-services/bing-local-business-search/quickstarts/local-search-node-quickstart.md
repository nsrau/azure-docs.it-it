---
title: "Avvio rapido: Inviare una query all'API con Node.js - Ricerca attività commerciali locali Bing"
titleSuffix: Azure Cognitive Services
description: Seguire questa guida introduttiva per inviare richieste all'API Bing Local Business Search, un servizio cognitivo di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3bb31c36e8c614a72b86f95cb7e7d1c588692f97
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873096"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Guida introduttiva: Inviare una query all'API di ricerca di attività locali Bing mediante Node.js

Usare questo argomento di avvio rapido per informazioni su come inviare richieste all'API Ricerca attività commerciali locali Bing, un servizio cognitivo di Azure. Anche se questa semplice applicazione è scritta in Node.js, l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione in grado di eseguire richieste HTTP e analizzare codice JSON.

Questa applicazione di esempio recupera i dati di risposta locali dall'API per una query di ricerca.

## <a name="prerequisites"></a>Prerequisiti

* La versione più recente di [Node.js](https://nodejs.org/en/download/).
* [Libreria di richieste JavaScript](https://github.com/request/request).
* Un [account dell'API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con API di Ricerca Bing. Per questo argomento di avvio rapido è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Salvare la chiave API fornita quando si attiva la versione di valutazione gratuita. Per altre informazioni, vedere [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="code-scenario"></a>Scenario di codice

Il codice seguente definisce e invia la richiesta, che viene implementata nei passaggi seguenti:

1. Dichiarare le variabili per specificare l'endpoint dall'host e il percorso.
2. Specificare la query e aggiungere il parametro di query.
3. Creare una funzione del gestore per la risposta.
4. Definire la funzione di ricerca che crea la richiesta e aggiunge l'intestazione `Ocp-Apim-Subscription-Key`.
5. Eseguire la funzione di ricerca.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Passaggi successivi

* [Guida di avvio rapido a Ricerca attività commerciali locali Bing in C#](local-quickstart.md)
* [Guida introduttiva a Local Business Search in Java](local-search-java-quickstart.md)
* [Guida introduttiva a Local Business Search in Python](local-search-python-quickstart.md)
