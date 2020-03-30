---
title: Eseguire più azioni da una regola di Azure IoT Central Documenti Microsoft
description: Eseguire più azioni da una singola regola di IoT Central e creare gruppi riutilizzabili di azioni che è possibile eseguire da più regole.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157688"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Raggruppare più azioni da eseguire da una o più regole

*Questo articolo si applica ai costruttori e agli amministratori.*

In Azure IoT Central è possibile creare regole per eseguire azioni quando viene soddisfatta una condizione. Le regole sono basate su eventi o dati di telemetria del dispositivo. Ad esempio, è possibile notificare a un operatore quando la temperatura di un dispositivo supera una soglia. Questo articolo descrive come usare i gruppi di azioni di Monitoraggio di Azure per collegare più azioni a una regola di Centro IoT.This article describes how to use [Azure Monitor](../../azure-monitor/overview.md) action *groups* to attach multiple actions to an IoT Central rule. È possibile associare un gruppo di azioni a più regole. Un gruppo di azioni è una raccolta di preferenze di notifica definite dal proprietario di una sottoscrizione di Azure.An [action group](../../azure-monitor/platform/action-groups.md) is a collection of notification preferences defined by the owner of an Azure subscription.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione creata utilizzando un piano tariffario standard
- Un account azure e una sottoscrizione per creare e gestire i gruppi di azioni di Monitoraggio di AzureAn Azure account and subscription to create and manage Azure Monitor action groups

## <a name="create-action-groups"></a>Creare gruppi di azioni

È possibile creare e gestire gruppi di azioni nel portale di Azure o con un modello di Azure Resource Manager.You can create and [manage action groups in the Azure portal](../../azure-monitor/platform/action-groups.md) or with an Azure Resource Manager [template](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Un gruppo di azioni può:

- Inviare notifiche come un messaggio di posta elettronica, un SMS o effettuare una chiamata vocale.
- Eseguire un'azione, ad esempio la chiamata a un webhook.

La schermata seguente mostra un gruppo di azioni che invia notifiche tramite posta elettronica e SMS e chiama un webhook:The following screenshot shows an action group that sends email and SMS notifications and calls a webhook:

![Gruppo di azioni](media/howto-use-action-groups/actiongroup.png)

Per usare un gruppo di azioni in una regola di IoT Central, il gruppo di azioni deve trovarsi nella stessa sottoscrizione di Azure dell'applicazione IoT Central.To use an action group in an IoT Central rule, the action group must be in the same Azure subscription as the IoT Central application.

## <a name="use-an-action-group"></a>Utilizzare un gruppo di azioni

Per usare un gruppo di azioni nell'applicazione IoT Central, creare innanzitutto una regola. Quando si aggiunge un'azione alla regola, selezionare Gruppi di azioni di Monitoraggio di Azure:When you add an action to the rule, select **Azure Monitor Action Groups:**

![Scegliere un'azione](media/howto-use-action-groups/chooseaction.png)

Scegliere un gruppo di azioni dalla sottoscrizione di Azure:Choose an action group from your Azure subscription:

![Scegli gruppo di azioni](media/howto-use-action-groups/chooseactiongroup.png)

Selezionare **Salva**. Il gruppo di azioni viene ora visualizzato nell'elenco delle azioni da eseguire quando viene attivata la regola:

![Gruppo di azioni salvate](media/howto-use-action-groups/savedactiongroup.png)

Nella tabella seguente sono riepilogate le informazioni inviate ai tipi di azione supportati:

| Tipo di azione | Formato di output |
| ----------- | -------------- |
| Email       | Modello di messaggio di posta elettronica standard di IoT Central |
| sms         | Avviso di Azure IoT Central: " , nomeapplicazione - ""nomeregola" attivato su ""nomedispositivo" "" in "''triggerDate''trigger''''''''''''''''''applicationName'" '''ruleName'' 'attivato''''''''''''''application''' ''''''''''''''''''''''''''''''''''nomeregola'' attivato su |
| Chiamata vocale       | Avviso di Azure I.O.T Central: regola "'nomeRegola'" attivata sul dispositivo "'nomedispositivo'" in corrispondenza della data di attivazione''''''''''''''''''''''''''''''''''''nomeRegola'" attivata sul dispositivo'' in corrispondenza dell'opzione "''''''''''''''''''''''''''''''''''''''regola'''''''''''''''' |
| webhook     | " schemaId: "AzureIoTCentralRuleWebhook", "data": -[payload webhook regolare](howto-create-webhooks.md#payload) |

Il testo seguente è un messaggio SMS di esempio da un gruppo di azioni:The following text is an example SMS message from an action group:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come usare i gruppi di azioni con le regole, il passaggio successivo consigliato consiste nell'imparare a [gestire i dispositivi.](howto-manage-devices.md)
