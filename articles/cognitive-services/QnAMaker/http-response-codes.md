---
title: Codici di risposta HTTP dell'API - QnA Maker
titleSuffix: Azure Cognitive Services
description: Informazioni sui codici di risposta HTTP restituiti dalle API di QnA Maker che consentiranno di risolvere eventuali errori.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0416b282a869536b0cea591ba39d7a3804697bac
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446531"
---
# <a name="qna-maker-api-http-response-codes"></a>Codici di risposta HTTP delle API di QnA Maker
Le API di [gestione](https://go.microsoft.com/fwlink/?linkid=2092179) e stima restituiscono codici di risposta HTTP. Mentre i messaggi di risposta includono informazioni specifiche per una richiesta, il codice di stato di risposta HTTP è generale. 

### <a name="management"></a>Gestione

|Codice|Spiegazione|
|:--|--|
|2xx|Riuscito|
|400|I parametri della richiesta non sono corretti, vale a dire che i parametri obbligatori mancano, non solo validi o sono troppo grandi|
|400|Il corpo della richiesta non è corretto, vale a dire che la stringa JSON manca, non è valida o è troppo grande|
|401|Chiave non valida|
|403|Operazione non consentita: non si dispone delle autorizzazioni corrette|
|404|La knowledge base non esiste|
|410|L'API è deprecata e non è più disponibile|
