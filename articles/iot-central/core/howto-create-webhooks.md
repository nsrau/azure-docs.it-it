---
title: Creare webhook per le regole in Azure IoT Central | Microsoft Docs
description: Creare webhook in Azure IoT Central per inviare automaticamente notifiche ad altre applicazioni in caso di attivazione di regole.
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337229"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Creare webhook per le regole in Azure IoT Central

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

I webhook consentono di connettere l'app IoT Central ad altre applicazioni e servizi per il monitoraggio remoto e le notifiche. I webhook inviano automaticamente notifiche ad altre applicazioni e servizi a cui ci si connette ogni volta che viene attivata una regola nell'app IoT Central. L'app IoT Central invia una richiesta POST all'endpoint HTTP dell'altra applicazione ogni volta che viene attivata una regola. Il payload contiene i dettagli del dispositivo e i dettagli del trigger di regola.

## <a name="set-up-the-webhook"></a>Configurare il webhook

In questo esempio si esegue la connessione a RequestBin per ricevere una notifica quando vengono attivate le regole usando i webhook.

1. Aprire [RequestBin](https://requestbin.net/).

1. Creare un nuovo RequestBin e copiare l'**URL del contenitore**.

1. Creare una [regola di telemetria](tutorial-create-telemetry-rules.md). Salvare la regola e aggiungere una nuova azione.

    ![Schermata di creazione del webhook](media/howto-create-webhooks/webhookcreate.png)

1. Scegliere l'azione del webhook e specificare un nome visualizzato e incollare l'URL del contenitore come URL di callback.

1. Salvare la regola.

A questo punto, quando viene attivata la regola, viene visualizzata una nuova richiesta in RequestBin.

## <a name="payload"></a>Payload

Quando viene attivata una regola, viene eseguita una richiesta HTTP POST all'URL di callback contenente un payload JSON con i dettagli relativi alla telemetria, al dispositivo, alla regola e all'applicazione. Il payload potrebbe essere simile al seguente:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
Se la regola monitora i dati di telemetria aggregati in un periodo di tempo, il payload conterrà una sezione di telemetria diversa.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>Avviso di modifica del formato dati

Se sono stati creati e salvati uno o più webhook prima del **3 aprile 2020**, sarà necessario eliminare il webhook e creare un nuovo webhook. Questo perché i webhook meno recenti usano un formato di payload precedente che verrà deprecato in futuro.

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>Payload del webhook (formato deprecato a partire dal 3 aprile 2020)

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Limitazioni note

Attualmente non è possibile sottoscrivere/annullare la sottoscrizione a livello di codice da questi webhook tramite un'API.

Se si hanno idee su come migliorare questa funzionalità, pubblicare i suggerimenti nel forum di Microsoft [User Voice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare e usare i webhook, il passaggio successivo suggerito consiste nell'esaminare la [configurazione dei gruppi di azioni di monitoraggio di Azure](howto-use-action-groups.md).
