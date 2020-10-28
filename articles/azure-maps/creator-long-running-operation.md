---
title: API per le operazioni a esecuzione prolungata di Mappe di Azure
description: Informazioni sull'elaborazione asincrona in background a esecuzione prolungata in Mappe di Azure
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 69c5b9d15c0ba3c7c8b6b01643beac1905c404ad
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895869"
---
# <a name="creator-long-running-operation-api"></a>API per le operazioni a esecuzione prolungata di autori

Alcune API di Mappe di Azure usano un [criterio asincrono di richiesta/risposta](/azure/architecture/patterns/async-request-reply). Questo criterio consente a Mappe di Azure di fornire servizi reattivi e a disponibilità elevata. Questo articolo illustra l'implementazione dell'elaborazione asincrona in background a esecuzione prolungata specifica per Mappe di Azure.

## <a name="submitting-a-request"></a>Invio di una richiesta

Un'applicazione client avvia un'operazione a esecuzione prolungata tramite una chiamata sincrona a un'API HTTP. In genere, questa chiamata è sotto forma di richiesta HTTP POST. Quando un carico di lavoro asincrono viene creato correttamente, l'API restituirà un codice di stato HTTP `202` a indicare che la richiesta è stata accettata. Questa risposta contiene un'intestazione `Location` che punta a un endpoint per il quale il client può eseguire il polling e controllare lo stato dell'operazione a esecuzione prolungata.

### <a name="example-of-a-success-response"></a>Esempio di risposta con esito positivo

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Se la chiamata non supera la convalida, l'API restituirà invece una risposta HTTP `400` di Richiesta non valida. Il corpo della risposta fornirà al client informazioni più dettagliate sul motivo per cui la richiesta non è valida.

### <a name="monitoring-the-operation-status"></a>Monitoraggio dello stato dell'operazione

È possibile eseguire il polling dell'endpoint del percorso specificato nelle intestazioni di risposta accettate per controllare lo stato dell'operazione a esecuzione prolungata. Il corpo della risposta della richiesta di stato dell'operazione conterrà sempre le proprietà `status` e `createdDateTime`. La proprietà `status` indica lo stato corrente dell'operazione a esecuzione prolungata. Gli stati possibili sono `"NotStarted"`, `"Running"`, `"Succeeded"` e `"Failed"`. La proprietà `createdDateTime` indica l'ora in cui è stata eseguita la richiesta iniziale di avvio dell'operazione a esecuzione prolungata. Quando lo stato è `"NotStarted"` o `"Running"`, verrà fornita anche un'intestazione `Retry-After` con la risposta. L'intestazione `Retry-After`, misurata in secondi, può essere usata per determinare quando deve essere eseguita la successiva chiamata di polling all'API per lo stato delle operazioni.

### <a name="example-of-running-a-status-response"></a>Esempio di esecuzione di una risposta di stato

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Gestione del completamento delle operazioni

Al termine dell'operazione a esecuzione prolungata, lo stato della risposta sarà `"Succeeded"` o `"Failed"`. Quando è stata creata una nuova risorsa da un'operazione a esecuzione prolungata, la risposta con esito positivo restituirà un codice di stato HTTP `201 Created`. La risposta conterrà anche un'intestazione `Location` che punta ai metadati relativi alla risorsa. Quando non è stata creata alcuna nuova risorsa, la risposta con esito positivo restituirà un codice di stato HTTP `200 OK`. In caso di errore, il codice di stato della risposta sarà anche il codice `200 OK`. Tuttavia, la risposta avrà una proprietà `error` inclusa nel corpo. I dati di errore sono conformi alla specifica di errore OData.

### <a name="example-of-success-response"></a>Esempio di risposta con esito positivo

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Esempio di risposta con errori

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```