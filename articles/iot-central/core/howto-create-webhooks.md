---
title: Creare webhook per le regole in Azure IoT Central | Microsoft Docs
description: Creare webhook in Azure IoT Central per inviare automaticamente notifiche ad altre applicazioni in caso di attivazione di regole.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158096"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Creare webhook per le regole in Azure IoT Central

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

I webhook consentono di connettere l'app IoT Central ad altre applicazioni e servizi per il monitoraggio remoto e le notifiche. I webhook inviano automaticamente notifiche ad altre applicazioni e servizi a cui ci si connette ogni volta che viene attivata una regola nell'app IoT Central. L'app IoT Central invia una richiesta POST all'endpoint HTTP dell'altra applicazione ogni volta che viene attivata una regola. Il payload contiene i dettagli del dispositivo e i dettagli del trigger della regola.

## <a name="set-up-the-webhook"></a>Configurare il webhook

In questo esempio, ci si connette a RequestBin per ricevere una notifica quando le regole vengono attivate tramite webhook.

1. Aprire [RequestBin](https://requestbin.net/).

1. Creare un nuovo RequestBin e copiare l'**URL del contenitore**.

1. Creare una regola di [telemetria](tutorial-create-telemetry-rules.md). Salvare la regola e aggiungere una nuova azione.

    ![Schermata di creazione del webhook](media/howto-create-webhooks/webhookcreate.png)

1. Scegliere l'azione del webhook e specificare un nome visualizzato e incollare l'URL del contenitore come URL di callback.

1. Salvare la regola.

Ora, quando la regola viene attivata, viene visualizzata una nuova richiesta in RequestBin.

## <a name="payload"></a>Payload

Quando viene attivata una regola, viene effettuata una richiesta HTTP POST all'URL di callback contenente un payload json con i dettagli di telemetria, dispositivo, regola e applicazione. Il payload potrebbe essere simile al seguente:

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
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Limitazioni note

Attualmente non è possibile sottoscrivere/annullare la sottoscrizione a livello di codice da questi webhook tramite un'API.

Se hai idee su come migliorare questa funzione, pubblica i tuoi suggerimenti sul nostro [forum vocale dell'utente.](https://feedback.azure.com/forums/911455-azure-iot-central)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come configurare e usare i webhook, il passaggio successivo consigliato consiste nell'esplorare la configurazione dei gruppi di azioni di Monitoraggio di [Azure.](howto-use-action-groups.md)
