---
title: Area di endpoint, Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: Con Node.js, trovare l'area di pubblicazione con chiave endpoint e ID dell'applicazione LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 6bcea43fb21cae6f000f3522b498dfc6c0f6d9a7
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166218"
---
# <a name="find-endpoint-region-with-nodejs"></a>Trovare l'area di endpoint con Node.js
Se si dispone dell'ID dell'applicazione LUIS e dell'ID della sottoscrizione LUIS, è possibile trovare la regione da utilizzare per le query sugli endpoint.

> [!NOTE] 
> La soluzione Node.js completa è disponibile nel repository GitHub [**LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>Strategia di query dell’endpoint LUIS
Ogni query dell’endpoint LUIS richiede:

* Una chiave endpoint
* Un ID applicazione
* Un’area

Se la query dell'endpoint LUIS usa la chiave endpoint e l'ID app corretti, ma l'area è sbagliata, il codice di risposta è 401. La richiesta 401 non viene considerata ai fini della quota della sottoscrizione. Trasforma questa richiesta in una strategia per eseguire il polling di tutte le aree per trovare l’area corretta. L'area corretta è l'unica richiesta che restituisce un codice di stato 2xx. 

|Codice della risposta|Parametri|
|--|--|
|2xx|correggere la chiave endpoint<br>ID app corretto<br>area host corretta|
|401|correggere la chiave endpoint<br>ID app corretto<br>area host _non corretta_|

## <a name="nodejs-code-to-find-region"></a>Codice Node. js per trovare l'area
L'applicazione della console acquisisce l'ID dell'applicazione LUIS e la chiave endpoint e restituisce tutte le aree ad essa associate. Attualmente, viene creata una chiave endpoint per regione, per cui solo una regione dovrebbe restituirla.

Includere le dipendenze NPM:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Aggiungere costanti. Sostituire i valori variabili per `subscriptionKey` e `appId` con valori personalizzati.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Aggiungere la funzione `searchRegions` per trovare l'area. Tutte le aree non corrette restituiscono 401, che viene catturato e ignorato.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Chiamare la funzione `searchRegions` e tornare alla singola area:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Quando l'applicazione viene eseguita, il terminale mostra l'area per l'ID dell'applicazione.

![Screenshot dell'app console che mostra l’area LUIS](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Passaggi successivi

Maggiori informazioni sulle [aree](luis-reference-regions.md) LUIS.
