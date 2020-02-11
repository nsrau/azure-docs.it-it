---
title: 'Avvio rapido: Monitorare i dispositivi in Azure IoT Central'
description: Informazioni per operatori su come usare l'applicazione Azure IoT Central per monitorare i dispositivi.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e5185d78b0ffc17e861f49d294af65ddcd200d07
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026164"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Avvio rapido: Usare Azure IoT Central per monitorare i dispositivi

*Questo articolo è rivolto a operatori, autori e amministratori.*

Questo avvio rapido illustra come un operatore può usare l'applicazione Microsoft Azure IoT Central per monitorare i dispositivi e modificare le impostazioni.

## <a name="prerequisites"></a>Prerequisites

Prima di iniziare, è opportuno completare i tre avvii rapidi precedenti [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md), [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-pnp-device.md) e [Configurare le regole e le azioni per il dispositivo](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Ricevere una notifica

Azure IoT Central invia le notifiche sui dispositivi come messaggi di posta elettronica. Lo sviluppatore ha aggiunto una regola per l'invio di una notifica quando la temperatura di un dispositivo sensore connesso supera una soglia. Controllare i messaggi di posta elettronica inviati all'account scelto dal generatore per la ricezione delle notifiche.

Aprire il messaggio di posta elettronica ricevuto al termine dell'avvio rapido [Configurare le regole e le azioni per il dispositivo](quick-configure-rules.md). Nel messaggio di posta elettronica selezionare il collegamento al dispositivo:

![Messaggio di posta elettronica di notifica avvisi](media/quick-monitor-devices/email.png)

Nel browser si apre la visualizzazione **Panoramica** per il dispositivo simulato creato negli argomenti di avvio rapido precedenti:

![Dispositivo che ha attivato il messaggio di posta elettronica di notifica](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Ricercare la causa di un problema

Un operatore può visualizzare le informazioni sul dispositivo nelle pagine **Panoramica**, **Informazioni** e **Comandi**. Lo sviluppatore ha creato una visualizzazione **Gestisci dispositivo** che consente di modificare le informazioni del dispositivo e di impostarne le proprietà.

Scegliere la visualizzazione **Informazioni generali** per vedere le informazioni sul dispositivo.

Il grafico del dashboard mostra il tracciato della temperatura del dispositivo. Si decide che la temperatura del dispositivo è troppo alta.

## <a name="remediate-an-issue"></a>Correggere un problema

Per apportare una modifica al dispositivo, usare la pagina **Gestisci dispositivo**.

Impostare **Velocità della ventola** su 500 per raffreddare il dispositivo. Scegliere **Salva** per aggiornare il dispositivo. Quando il dispositivo conferma la modifica delle impostazioni, lo stato della proprietà cambia in **sincronizzato**:

![Aggiornamento delle impostazioni](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

* Ricevere una notifica
* Ricercare la causa di un problema
* Correggere un problema

Ora che si sa come monitorare il dispositivo, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Creare e gestire un modello di dispositivo](howto-set-up-template.md).
