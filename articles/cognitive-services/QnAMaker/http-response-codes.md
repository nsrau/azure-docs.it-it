---
title: Codici di risposta HTTP dell'API - QnA Maker
titleSuffix: Azure Cognitive Services
description: Informazioni sui codici di risposta HTTP restituiti dalle API di QnA Maker che consentiranno di risolvere eventuali errori.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8a39e8ee783d999bf3851ef1ba564e8f24de7910
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726953"
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