---
title: Creare webhook per le regole in Azure IoT Central | Microsoft Docs
description: Creare webhook in Azure IoT Central per inviare automaticamente notifiche ad altre applicazioni in caso di attivazione di regole.
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 62599419d5634bea7cd25c93fbada8b472b95c4d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772237"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Creare webhook per le regole in Azure IoT Central

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

I webhook consentono di connettere l'app IoT Central ad altre applicazioni e servizi per il monitoraggio remoto e le notifiche. I webhook inviano automaticamente notifiche ad altre applicazioni e servizi a cui ci si connette ogni volta che viene attivata una regola nell'app IoT Central. Ogni volta che viene attivata una regola, l'app IoT Central invia una richiesta POST all'endpoint HTTP dell'altra applicazione. Il payload conterrà i dettagli del dispositivo e del trigger di regola. 

## <a name="how-to-set-up-the-webhook"></a>Come configurare il webhook
In questo esempio ci si connetterà a RequestBin per ricevere notifiche all'attivazione di regole usando i webhook. 

1. Aprire [RequestBin](http://requestbin.net/). 
1. Creare un nuovo RequestBin e copiare l'**URL del contenitore**. 
1. Creare una [regola di telemetria](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) o una [regola eventi](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Salvare la regola e aggiungere una nuova azione.
    ![Schermata di creazione del webhook](media/howto-create-webhooks-experimental/webhookcreate.png)
1. Scegliere l'azione del webhook e specificare un nome visualizzato e incollare l'URL del contenitore come URL di callback. 
1. Salvare la regola.

A questo punto, quando viene attivata la regola, viene visualizzata una nuova richiesta in RequestBin.

## <a name="payload"></a>Payload
Quando viene attivata una regola, viene effettuata una richiesta HTTP POST all'URL di callback contenente un payload JSON con i dettagli su misure, dispositivi, regole e applicazioni. Per una regola di telemetria, il payload è simile al seguente:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Limitazioni note
Attualmente non è possibile sottoscrivere/annullare la sottoscrizione a livello di codice da questi webhook tramite un'API.

Inviare eventuali suggerimenti su come migliorare questa funzionalità al [forum UserVoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato illustrato come configurare e usare i webhook, il passaggio successivo consigliato consiste nell'esplorare la [compilazione di flussi di lavoro in Microsoft Flow](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
