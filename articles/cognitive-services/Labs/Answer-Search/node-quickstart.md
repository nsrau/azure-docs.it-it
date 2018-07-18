---
title: Guida introduttiva a Servizi cognitivi Microsoft per Node, Project Answer Search | Microsoft Docs
description: Introduzione all'uso di Project Answer Search, Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376193"
---
# <a name="project-answer-search-node-quickstart"></a>Guida introduttiva a Project Answer Search per Node

L'esempio seguente di Node crea una query per ottenere informazioni su Yosemite National Park.

## <a name="prerequisites"></a>prerequisiti

Ottenere una chiave di accesso per la versione di valutazione gratuita di [Lab di Servizi cognitivi](https://aka.ms/answersearchsubscription)

Questo esempio usa Node v8.9.4

## <a name="code-scenario"></a>Scenario di codice 

Il codice seguente ottiene le risposte.
Viene implementato nei passaggi seguenti:
1. Dichiarare le variabili per specificare l'endpoint dall'host e il percorso.
2. Specificare l'URL della query da visualizzare in anteprima e aggiungere il parametro di query.  
3. Creare una funzione del gestore per la risposta.
4. Definire la funzione di ricerca che crea la richiesta e aggiunge l'intestazione *Ocp-Apim-Subscription-Key*.
5. Eseguire la funzione di ricerca. 

Il codice completo per questa demo è il seguente:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

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

````

## <a name="next-steps"></a>Passaggi successivi
- [Codice C# di esempio](c-sharp-quickstart.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)