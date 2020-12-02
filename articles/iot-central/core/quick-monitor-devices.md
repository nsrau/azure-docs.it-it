---
title: 'Avvio rapido: Monitorare i dispositivi in Azure IoT Central'
description: "Avvio rapido: Informazioni per operatori su come usare l'applicazione Azure IoT Central per monitorare i dispositivi."
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e4485e4f8e873b1b49dc3d6df72eb04a1cbe8c17
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992755"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Guida introduttiva: Usare Azure IoT Central per monitorare i dispositivi

*Questo articolo è rivolto a operatori, autori e amministratori.*

Questo avvio rapido illustra come un operatore può usare l'applicazione Microsoft Azure IoT Central per monitorare i dispositivi e modificare le impostazioni.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è opportuno completare i tre avvii rapidi precedenti [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md), [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-simulated-device.md) e [Configurare le regole e le azioni per il dispositivo](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Ricevere una notifica

Azure IoT Central invia le notifiche sui dispositivi come messaggi di posta elettronica. Lo sviluppatore ha aggiunto una regola per l'invio di una notifica a un operatore quando l'umidità di un dispositivo sensore connesso supera una soglia. L'operatore controlla la presenza di notifiche nella posta elettronica.

Aprire il messaggio di posta elettronica ricevuto al termine dell'avvio rapido [Configurare le regole e le azioni per il dispositivo](quick-configure-rules.md). Nel messaggio di posta elettronica selezionare il collegamento al dispositivo:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Screenshot che mostra il messaggio di posta elettronica di notifica":::

Nel browser si apre la visualizzazione **Panoramica** per il dispositivo simulato creato negli argomenti di avvio rapido precedenti:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Screenshot che mostra una panoramica del dispositivo che ha attivato la notifica":::

## <a name="investigate-an-issue"></a>Ricercare la causa di un problema

Un operatore può visualizzare le informazioni sul dispositivo nelle pagine **Panoramica**, **Informazioni** e **Comandi**. Lo sviluppatore ha creato una visualizzazione **Gestisci dispositivo** che consente di modificare le informazioni del dispositivo e di impostarne le proprietà.

Il grafico nel dashboard mostra un tracciato dell'umidità del dispositivo. Si decide che l'umidità del dispositivo è troppo elevata.

## <a name="remediate-an-issue"></a>Correggere un problema

Per apportare una modifica al dispositivo, usare la pagina **Gestisci dispositivo**.

Impostare la **temperatura di destinazione** su 80 per riscaldare il dispositivo e ridurre l'umidità. Scegliere **Salva** per aggiornare il dispositivo. Quando il dispositivo conferma la modifica delle impostazioni, lo stato della proprietà cambia in **sincronizzato**:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Screenshot che mostra l'impostazione aggiornata della temperatura di destinazione per il dispositivo":::

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

* Ricevere una notifica
* Ricercare la causa di un problema
* Correggere un problema

Ora che si sa come monitorare il dispositivo, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Creare e gestire un modello di dispositivo](howto-set-up-template.md).
