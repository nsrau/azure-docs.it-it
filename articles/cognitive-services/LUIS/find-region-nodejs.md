---
title: Trovare l’area LUIS con Node.js nei limiti del servizio LUIS (Language Understanding) | Microsoft Docs
description: Ricerca programmatica dell'area di pubblicazione con codice di sottoscrizione e ID applicazione per LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 18ee324c10f074601c0c04573ca1a5266481f21c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2018
ms.locfileid: "35378734"
---
# <a name="region-can-be-determined-from-api-call"></a>L’area può essere determinata dalla chiamata API 
Se si dispone dell'ID dell'applicazione LUIS e dell'ID della sottoscrizione LUIS, è possibile trovare la regione da utilizzare per le query sugli endpoint.

> [!NOTE] 
> La soluzione Node.js completa è disponibile nel [**repository Github degli esempi LUIS**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>Strategia di query dell’endpoint LUIS
Ogni query dell’endpoint LUIS richiede:

* Una chiave di sottoscrizione
* Un ID applicazione
* Un’area

Se la query dell’endpoint LUIS utilizza la chiave di sottoscrizione e l'ID app corretti, ma l’area è sbagliata, il codice di risposta è 401. La richiesta 401 non viene considerata ai fini della quota della sottoscrizione. Trasforma questa richiesta in una strategia per eseguire il polling di tutte le aree per trovare l’area corretta. L'area corretta è l'unica richiesta che restituisce un codice di stato 2xx. 

|Codice della risposta|Parametri|
|--|--|
|2xx|chiave di sottoscrizione corretta<br>ID app corretto<br>area host corretta|
|401|chiave di sottoscrizione corretta<br>ID app corretto<br>area host _non corretta_|

## <a name="nodejs-code-to-find-region"></a>Codice Node. js per trovare l'area
L'applicazione della console acquisisce l'ID dell'applicazione LUIS e la chiave di sottoscrizione e restituisce tutte le aree ad essa associate. Attualmente, viene creata una chiave di sottoscrizione per regione, per cui solo una regione dovrebbe restituirla.

Includere le dipendenze NPM:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Aggiungere costanti. Sostituire i valori variabili per `subscriptionKey` e `appId` con valori personalizzati.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Aggiungere la funzione `searchRegions` per trovare l'area. Tutte le aree non corrette restituiscono 401, che viene catturato e ignorato.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Chiamare la funzione `searchRegions` e tornare alla singola area:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Quando l'applicazione viene eseguita, il terminale mostra l'area per l'ID dell'applicazione e la chiave di sottoscrizione.

![Screenshot dell'app console che mostra l’area LUIS](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Passaggi successivi

Maggiori informazioni sulle [aree](luis-reference-regions.md) LUIS.