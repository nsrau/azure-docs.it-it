---
title: Area degli endpoint, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Con C#, trovare l'area di pubblicazione con la chiave dell'endpoint e l'ID applicazione per LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 5d4c564ac797f40d7f6e9b169a6c864d3ef8147b
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165164"
---
# <a name="find-endpoint-region-with-c"></a>Trovare l'area di endpoint con C# 
Se si dispone dell'ID dell'applicazione LUIS e dell'ID della sottoscrizione LUIS, è possibile trovare la regione da utilizzare per le query sugli endpoint.

> [!NOTE] 
> La soluzione C# completa è disponibile nel [ repository GitHub **LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>Codice della classe C# per trovare l'area
L'applicazione della console acquisisce l'ID dell'applicazione LUIS e la chiave endpoint e restituisce tutte le aree ad essa associate. Attualmente, viene creata una chiave endpoint per regione, per cui solo una regione dovrebbe restituirla.

Includere le dipendenze della libreria .Net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Aggiungere questa classe Language Understanding personalizzata predefinita per trovare l’area. Sostituire i valori variabili per `luisAppId` e `luisSubscriptionKey` con valori personalizzati. Tutte le aree che restituiscono 401 verranno scritte nella console di debug. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Questo è un esempio di come si chiama la classe LUIS personalizzata nel metodo principale dell'applicazione della console:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Quando l'applicazione viene eseguita, la console mostra l'area per l'ID dell'applicazione.

![Screenshot dell'app console che mostra l’area LUIS](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Passaggi successivi

Maggiori informazioni sulle [aree](luis-reference-regions.md) LUIS.
