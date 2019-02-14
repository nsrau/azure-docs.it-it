---
title: Codici di risposta HTTP dell'API - QnA Maker
titleSuffix: Azure Cognitive Services
description: Informazioni sui codici di risposta HTTP restituiti dalle API di QnA Maker che consentiranno di risolvere eventuali errori.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d5b97f9c3b29732ba901f783ac04a3ada338ab69
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866441"
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
