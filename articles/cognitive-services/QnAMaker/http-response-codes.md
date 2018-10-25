---
title: Codici di risposta HTTP dell'API - QnA Maker
titleSuffix: Azure Cognitive Services
description: Comprendere quali codici di risposta HTTP vengono restituiti dalle API di QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 96782d32817e4989c02e0ed098d7772c80aa26c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079668"
---
# <a name="qna-maker-api-http-response-codes"></a>Codici di risposta HTTP delle API di QnA Maker
Le API di [gestione](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) e stima restituiscono codici di risposta HTTP. Mentre i messaggi di risposta includono informazioni specifiche per una richiesta, il codice di stato di risposta HTTP è generale. 

### <a name="management"></a>Gestione

|Codice|Spiegazione|
|:--|--|
|2xx|Success|
|400|I parametri della richiesta non sono corretti, vale a dire che i parametri obbligatori mancano, non solo validi o sono troppo grandi|
|400|Il corpo della richiesta non è corretto, vale a dire che la stringa JSON manca, non è valida o è troppo grande|
|401|Chiave non valida|
|403|Operazione non consentita: non si dispone delle autorizzazioni corrette|
|404|La knowledge base non esiste|