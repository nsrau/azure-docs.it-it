---
title: Codici di risposta HTTP dell'API-LUIS
titleSuffix: Azure Cognitive Services
description: Comprendere quali codici di risposta HTTP vengono restituiti dalle API di creazione LUIS e dalle API per endpoint
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 43fbca732bbd281ad980ae86e4a89f446fdb69bc
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945855"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Codici di risposta comuni API e relativo significato

Le API di [creazione](https://go.microsoft.com/fwlink/?linkid=2092087) e per [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) restituiscono codici di risposta HTTP. Mentre i messaggi di risposta includono informazioni specifiche per una richiesta, il codice di stato di risposta HTTP è generale. 

## <a name="common-status-codes"></a>Codici di stato comuni
La tabella seguente elenca alcuni dei codici di stato di risposta HTTP più comuni per le API di [creazione ](https://go.microsoft.com/fwlink/?linkid=2092087) e per [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356):

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

## <a name="next-steps"></a>Passaggi successivi

* API REST documentazione per [creazione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) ed [endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
