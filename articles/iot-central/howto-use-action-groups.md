---
title: Eseguire più azioni da una regola di Azure IoT Central | Microsoft Docs
description: Eseguire più azioni da una singola regola IoT Central e creare gruppi riutilizzabili di azioni che è possibile eseguire da più regole.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523012"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Più azioni da eseguire da una o più regole di gruppo

*Questo articolo si applica ai generatori e agli amministratori.*

In Azure IoT Central, vengono create regole per eseguire azioni quando viene soddisfatta una condizione. Le regole sono basate su eventi o di telemetria del dispositivo. Ad esempio, è possibile avvisare un operatore quando la temperatura in un dispositivo supera una soglia. Questo articolo descrive come usare [monitoraggio di Azure](../azure-monitor/overview.md) *gruppi di azioni* per associare più azioni per una regola di IoT Central. È possibile collegare un gruppo di azioni a più regole. Un' [gruppo di azioni](../azure-monitor/platform/action-groups.md) è una raccolta delle preferenze di notifica definiti per il proprietario di una sottoscrizione di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione con pagamento in base al consumo
- Un account Azure e una sottoscrizione per creare e gestire gruppi di azioni di monitoraggio di Azure

## <a name="create-action-groups"></a>Creare gruppi di azioni

È possibile [creare e gestire gruppi di azione nel portale di Azure](../azure-monitor/platform/action-groups.md) o con un [modello di Azure Resource Manager](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

È possibile eseguire un gruppo di azione:

- Invio di notifiche, ad esempio un messaggio di posta elettronica, SMS, oppure effettuare una chiamata vocale.
- Eseguire un'azione, ad esempio chiamare un webhook.

Lo screenshot seguente illustra un gruppo di azione che invia messaggio di posta elettronica e le notifiche SMS e chiama un webhook:

![Gruppo di azioni](media/howto-use-action-groups/actiongroup.png)

Per usare un gruppo di azioni in una regola di IoT Central, il gruppo di azione deve essere nella stessa sottoscrizione di Azure dell'applicazione IoT Central.

## <a name="use-an-action-group"></a>Usare un gruppo di azioni

Per usare un gruppo di azione nell'applicazione IoT Central, prima di tutto creare una regola di evento o di telemetria. Quando si aggiunge un'azione per la regola, selezionare **gruppi di azioni di monitoraggio di Azure**:

![Scegliere un'azione](media/howto-use-action-groups/chooseaction.png)

Scegliere un gruppo di azione dalla sottoscrizione di Azure:

![Scegliere il gruppo di azione](media/howto-use-action-groups/chooseactiongroup.png)

Selezionare **Salva**. Il gruppo di azione ora viene visualizzata nell'elenco di azioni da eseguire quando viene attivata la regola:

![Il gruppo di azione salvato](media/howto-use-action-groups/savedactiongroup.png)

La tabella seguente riepiloga le informazioni inviate per i tipi di azione supportata:

| Tipo di azione | Formato di output |
| ----------- | -------------- |
| Email       | Modello di messaggio di posta elettronica standard IoT Central |
| sms         | Avviso di Azure IoT Central: ${applicationName} - "${ruleName}" attivato "${deviceName}" in ${triggerDate} ${triggerTime} |
| Chiamata vocale       | Avviso di Azure I.O.T Central: regola "${ruleName}" ha attivato sul dispositivo "${deviceName}" in ${triggerDate} ${triggerTime}, nell'applicazione ${applicationName} |
| webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "data": {[payload del webhook regolari](#payload)}} |

Il testo seguente è un messaggio SMS di esempio da un gruppo di azioni:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> Il codice JSON seguente illustra un payload di azione webhook di esempio:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i gruppi di azioni con le regole, il passaggio successivo consigliato sia per informazioni su come [gestire i dispositivi](howto-manage-devices.md).
