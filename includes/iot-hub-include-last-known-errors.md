---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 54f4835a904b897370cf9f075ae3c005b1114992
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563542"
---
[Ottenere l'integrità dell'endpoint](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) nell'API REST indica lo stato di integrità degli endpoint e l'ultimo errore noto, per identificare il motivo per cui un endpoint non è integro. Nella tabella seguente sono elencati gli errori più comuni.

|Ultimo errore noto|Descrizione/quando si verifica|Possibile mitigazione|
|-----|-----|-----|
|Temporaneo|Si è verificato un errore temporaneo e l'hub Internet tenterà di nuovo l'operazione.|Osservare i [log delle risorse di route](../articles/iot-hub/monitor-iot-hub-reference.md#routes).|
|InternalError|Si è verificato un errore durante il recapito di un messaggio a un endpoint.|Si tratta di un'eccezione interna, ma anche di osservare i [log delle risorse di route](../articles/iot-hub/monitor-iot-hub-reference.md#routes).|
|Non autorizzata|L'hub Internet delle cose non è autorizzato a inviare messaggi all'endpoint specificato.|Verificare che la stringa di connessione sia aggiornata per l'endpoint. Se è stato modificato, prendere in considerazione un aggiornamento nell'hub Internet delle cose. Se l'endpoint usa l'identità gestita, verificare che l'entità dell'hub degli utenti disponga delle autorizzazioni necessarie per la destinazione.|
|Sospensione causata dal servizio Microsoft FullText|L'hub Internet delle cose viene limitato durante la scrittura dei messaggi nell'endpoint.|Esaminare i limiti di limitazione per l'endpoint interessato. Modificare le configurazioni per l'endpoint per la scalabilità verticale, se necessario.|
|Timeout|Timeout dell'operazione.|Ripetere l'operazione.|
|Non trovato|La risorsa di destinazione non esiste.|Verificare che la risorsa di destinazione esista.|
|Contenitore non trovato|Il contenitore di archiviazione non esiste.|Verificare che il contenitore di archiviazione esista.|
|Contenitore disabilitato|Il contenitore di archiviazione è disabilitato.|Verificare che il contenitore di archiviazione sia abilitato.|
|MaxMessageSizeExceeded|Il routing dei messaggi ha un limite di dimensioni del messaggio di 256 KB. la dimensione del messaggio instradata supera questo limite.|Controllare se le dimensioni del messaggio possono essere ridotte utilizzando un minor numero di proprietà dell'applicazione o un numero minore di arricchimenti dei messaggi.|
|PartitioningAndDuplicateDetectionNotSupported|Per il bus di servizio non è possibile abilitare il rilevamento dei duplicati.|Disabilitare il rilevamento dei duplicati dal bus di servizio o provare a usare un'entità senza rilevamento di duplicati.|
|SessionfulEntityNotSupported|Per il bus di servizio non è possibile abilitare le sessioni.|Disabilitare la sessione dal bus di servizio o prendere in considerazione l'uso di un'entità senza sessioni.|
|NoMatchingSubscriptionsForMessage|Non esiste alcuna sottoscrizione per scrivere un messaggio nell'argomento del bus di servizio.|Creare una sottoscrizione per l'indirizzamento dei messaggi dell'hub Internet.|
|EndpointExternallyDisabled|L'endpoint non si trova in uno stato attivo, in modo che l'hub Internet possa inviare messaggi.|Abilitare l'endpoint per ripristinare lo stato attivo.|
|DeviceMaximumQueueDepthExceeded|È stato raggiunto il limite delle dimensioni del bus di servizio.|Provare a rimuovere i messaggi dagli hub eventi di destinazione per consentire l'inserimento di nuovi messaggi nell'hub eventi.|