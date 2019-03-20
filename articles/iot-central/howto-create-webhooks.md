---
title: Creare webhook per le regole in Azure IoT Central | Microsoft Docs
description: Creare webhook in Azure IoT Central per inviare automaticamente notifiche ad altre applicazioni in caso di attivazione di regole.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 22167de6676837c45c48a0bafd19b1ba69578827
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003677"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Creare webhook per le regole in Azure IoT Central

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

I webhook consentono di connettere l'app IoT Central ad altre applicazioni e servizi per il monitoraggio remoto e le notifiche. I webhook inviano automaticamente notifiche ad altre applicazioni e servizi a cui ci si connette ogni volta che viene attivata una regola nell'app IoT Central. L'app IoT Central invia una richiesta POST all'endpoint HTTP dell'applicazione ogni volta che viene attivata una regola. Il payload contiene i dettagli del dispositivo e i dettagli trigger della regola.

## <a name="set-up-the-webhook"></a>Configurare il webhook

In questo esempio, si connette a RequestBin per ricevere notifiche quando le regole vengono generati usando i webhook.

1. Aprire [RequestBin](https://requestbin.net/).

1. Creare un nuovo RequestBin e copiare l'**URL del contenitore**.

1. Creare una [regola di telemetria](howto-create-telemetry-rules.md) o una [regola eventi](howto-create-event-rules.md). Salvare la regola e aggiungere una nuova azione.

    ![Schermata di creazione di Webhook](media/howto-create-webhooks/webhookcreate.png)

1. Scegliere l'azione del webhook e specificare un nome visualizzato e incollare l'URL del contenitore come URL di callback.

1. Salvare la regola.

Ora quando viene attivata la regola, viene visualizzata una nuova richiesta di RequestBin.

## <a name="payload"></a>Payload

Quando viene attivata una regola, viene effettuata una richiesta HTTP POST all'URL di callback contenente un payload JSON con i dettagli su misure, dispositivi, regole e applicazioni. Per una regola di dati di telemetria, il payload è simile a quanto segue:

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

Ora che si è appreso come configurare e usare i webhook, il passaggio successivo consigliato è esplorare [compilazione di flussi di lavoro in Microsoft Flow](howto-add-microsoft-flow.md).
