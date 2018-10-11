---
title: Codici di risposta HTTP dell'API Language Understanding (LUIS) - Azure | Microsoft Docs
titleSuffix: Azure
description: Comprendere quali codici di risposta HTTP vengono restituiti dalle API di creazione LUIS e dalle API per endpoint
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/16/2018
ms.author: diberry
ms.openlocfilehash: f005c7f13cd05ce3ca6ce494c4a2670106cb3637
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900982"
---
# <a name="luis-api-http-response-codes"></a>Codici di risposta HTTP delle API LUIS
Le API di [creazione](https://aka.ms/luis-authoring-apis) e per [endpoint](https://aka.ms/luis-endpoint-apis) restituiscono codici di risposta HTTP. Mentre i messaggi di risposta includono informazioni specifiche per una richiesta, il codice di stato di risposta HTTP è generale. 

## <a name="common-status-codes"></a>Codici di stato comuni
La tabella seguente elenca alcuni dei codici di stato di risposta HTTP più comuni per le API di [creazione ](https://aka.ms/luis-authoring-apis) e per [endpoint](https://aka.ms/luis-endpoint-apis):

|Codice|API|Spiegazione|
|:--|--|--|
|400|Creazione, Endpoint|i parametri della richiesta non sono corretti, vale a dire che i parametri obbligatori sono mancanti, non validi o troppo grandi|
|400|Creazione, Endpoint|il corpo della richiesta non è corretto, vale a dire che la stringa JSON è mancante, non valida o troppo grande|
|401|Creazione|è stata usata chiave di sottoscrizione dell'endpoint, anziché la chiave di creazione|
|401|Creazione, Endpoint|chiave non valida, in formato non corretto o vuota|
|401|Creazione, Endpoint| la chiave non corrisponde all'area|
|401|Creazione|l'utente non è proprietario o collaboratore|
|401|Creazione|ordine non valido di chiamate API|
|403|Creazione, Endpoint|è stato superato il limite totale mensile della quota di chiavi|
|409|Endpoint|il caricamento dell'applicazione è ancora in corso|
|410|Endpoint|è necessario ripetere il training e pubblicare nuovamente l'applicazione|
|414|Endpoint|la query supera il limite massimo di caratteri|
|429|Creazione, Endpoint|È stato superato il limite di velocità (richieste/sec)|