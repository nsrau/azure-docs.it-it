---
title: Trovare l’area LUIS con C# nei limiti di Language Understanding (LUIS) | Microsoft Docs
description: Ricerca programmatica dell'area di pubblicazione con codice di sottoscrizione e ID applicazione per LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: c8d2024567255083aec470adfebff0d1706fd472
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2018
ms.locfileid: "35378731"
---
# <a name="region-can-be-determined-from-api-call"></a>L’area può essere determinata dalla chiamata API 
Se si dispone dell'ID dell'applicazione LUIS e dell'ID della sottoscrizione LUIS, è possibile trovare la regione da utilizzare per le query sugli endpoint.

> [!NOTE] 
> La soluzione C# completa è disponibile nel [**repository Github degli esempi LUIS**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>Codice della classe C# per trovare l'area
L'applicazione della console acquisisce l'ID dell'applicazione LUIS e la chiave di sottoscrizione e restituisce tutte le aree ad essa associate. Attualmente, viene creata una chiave di sottoscrizione per regione, per cui solo una regione dovrebbe restituirla.

Includere le dipendenze della libreria .Net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Aggiungere questa classe Language Understanding personalizzata predefinita per trovare l’area. Sostituire i valori variabili per `luisAppId` e `luisSubscriptionKey` con valori personalizzati. Tutte le aree che restituiscono 401 verranno scritte nella console di debug. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Questo è un esempio di come si chiama la classe LUIS personalizzata nel metodo principale dell'applicazione della console:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Quando l'applicazione viene eseguita, la console mostra l'area per l'ID dell'applicazione e la chiave di sottoscrizione.

![Screenshot dell'app console che mostra l’area LUIS](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Passaggi successivi

Maggiori informazioni sulle [aree](luis-reference-regions.md) LUIS.
