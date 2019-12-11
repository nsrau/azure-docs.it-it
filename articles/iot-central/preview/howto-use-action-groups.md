---
title: Eseguire più azioni da una regola di IoT Central di Azure | Microsoft Docs
description: Eseguire più azioni da una singola regola di IoT Central e creare gruppi riutilizzabili di azioni che è possibile eseguire da più regole.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: a93e96dd14411302bf5368b4825044e060b54a43
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974360"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules-preview-features"></a>Raggruppare più azioni da eseguire da una o più regole (funzionalità di anteprima)

*Questo articolo si applica ai generatori e agli amministratori.*

In IoT Central di Azure è possibile creare regole per eseguire azioni quando viene soddisfatta una condizione. Le regole sono basate sui dati di telemetria del dispositivo o sugli eventi. Ad esempio, è possibile inviare una notifica a un operatore quando la temperatura di un dispositivo supera una soglia. Questo articolo descrive come usare i [monitoraggio di Azure](../../azure-monitor/overview.md) *gruppi di azioni* per alleghi più azioni a una regola di IOT Central. È possibile aggiungere un gruppo di azioni a più regole. Un [gruppo di azioni](../../azure-monitor/platform/action-groups.md) è una raccolta di preferenze di notifica definite dal proprietario di una sottoscrizione di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione con pagamento in base al consumo
- Un account e una sottoscrizione di Azure per creare e gestire gruppi di azioni di monitoraggio di Azure

## <a name="create-action-groups"></a>Creare gruppi di azioni

È possibile [creare e gestire gruppi di azioni nella portale di Azure](../../azure-monitor/platform/action-groups.md) o con un [modello di Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Un gruppo di azioni può:

- Inviare notifiche quali un messaggio di posta elettronica, un SMS o effettuare una chiamata vocale.
- Eseguire un'azione, ad esempio la chiamata a un webhook.

La schermata seguente mostra un gruppo di azione che invia notifiche tramite posta elettronica e SMS e chiama un webhook:

![Gruppo di azioni](media/howto-use-action-groups/actiongroup.png)

Per usare un gruppo di azioni in una regola di IoT Central, il gruppo di azioni deve trovarsi nella stessa sottoscrizione di Azure dell'applicazione IoT Central.

## <a name="use-an-action-group"></a>Usare un gruppo di azioni

Per usare un gruppo di azioni nell'applicazione IoT Central, creare prima di tutto una regola. Quando si aggiunge un'azione alla regola, selezionare **gruppi di azioni di monitoraggio di Azure**:

![Scegliere un'azione](media/howto-use-action-groups/chooseaction.png)

Scegliere un gruppo di azione dalla sottoscrizione di Azure:

![Scegliere il gruppo di azioni](media/howto-use-action-groups/chooseactiongroup.png)

Selezionare **Salva**. Il gruppo di azioni viene ora visualizzato nell'elenco di azioni da eseguire quando viene attivata la regola:

![Gruppo di azione salvato](media/howto-use-action-groups/savedactiongroup.png)

Nella tabella seguente sono riepilogate le informazioni inviate ai tipi di azione supportati:

| Tipo di azione | Formato di output |
| ----------- | -------------- |
| Indirizzo di posta elettronica       | Modello di posta elettronica standard IoT Central |
| SMS         | Avviso IoT Central di Azure: $ {ApplicationName}-"$ {RuleName}" attivata in "$ {DeviceName}" in $ {triggerDate} $ {triggerTime} |
| Voce       | Avviso centrale Azure I. O. T: regola "$ {RuleName}" attivata sul dispositivo "$ {DeviceName}" in $ {triggerDate} $ {triggerTime}, nell'applicazione $ {applicationName} |
| webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "data": {[payload del webhook normale](howto-create-webhooks.md#payload)}} |

Il testo seguente è un esempio di messaggio SMS da un gruppo di azione:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i gruppi di azioni con le regole, il passaggio successivo suggerito consiste nell'apprendere come [gestire i dispositivi](howto-manage-devices.md).
