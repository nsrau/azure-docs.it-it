---
title: Monitorare i dispositivi in Azure IoT Central | Microsoft Docs
description: Un operatore può usare l'applicazione Azure IoT Central per monitorare i dispositivi.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878782"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Esercitazione: Usare Azure IoT Central per monitorare i dispositivi (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come un operatore può usare l'applicazione Microsoft Azure IoT Central per monitorare i dispositivi e modificare le impostazioni.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Ricevere una notifica
> * Ricercare la causa di un problema
> * Correggere un problema

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, il creatore deve completare le esercitazioni per creare l'applicazione Azure IoT Central:

* [Definire un nuovo tipo di dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Aggiungere un dispositivo](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Configurare le regole e le azioni per i dispositivi](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Ricevere una notifica

Azure IoT Central invia le notifiche sui dispositivi come messaggi di posta elettronica. Il creatore ha aggiunto una regola che prevede l'invio di una notifica quando la temperatura in un dispositivo con sensore ambientale connesso supera una determinata soglia. Controllare i messaggi di posta elettronica inviati all'account scelto dal creatore per la ricezione delle notifiche.

Aprire il messaggio di posta elettronica ricevuto al termine dell'esercitazione [Configurare le regole e le azioni per il dispositivo](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Nel messaggio di posta elettronica selezionare il collegamento al dispositivo:

![Messaggio di posta elettronica di notifica avvisi](media/tutorial-monitor-devices-pnp/email.png)

Nel browser viene aperta la visualizzazione **Dashboard** per il dispositivo simulato con sensore ambientale creato nelle esercitazioni precedenti:

![Dispositivo che ha attivato il messaggio di posta elettronica di notifica](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Ricercare la causa di un problema

Un operatore può visualizzare le informazioni sul dispositivo nelle pagine **Dashboard**, **Proprietà sensore ambientale** e **Comandi**. Il creatore ha personalizzato le pagine **Dashboard** e **Proprietà sensore ambientale** in modo da visualizzare informazioni importanti relative a un dispositivo di tipo sensore ambientale connesso.

Scegliere la visualizzazione **Dashboard** per vedere le informazioni sul dispositivo.

Il grafico del dashboard mostra il tracciato della temperatura del dispositivo. È inoltre possibile visualizzare la temperatura target corrente impostata per il dispositivo nel riquadro **Proprietà dispositivo**. Si decide che la temperatura target è troppo alta.

## <a name="remediate-an-issue"></a>Correggere un problema

Per apportare una modifica al dispositivo, usare la pagina **Proprietà sensore ambientale**:

1. Scegliere **Proprietà sensore ambientale**. Modificare **Brightness Level** (Livello luminosità) impostandolo su 10. Scegliere **Salva** per aggiornare il dispositivo. Quando il dispositivo conferma la modifica delle impostazioni, lo stato della proprietà cambia in **sincronizzato**:

    ![Aggiornamento delle impostazioni](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Scegliere **Dashboard** e verificare il nuovo valore della proprietà:

    ![Dashboard del dispositivo aggiornato](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Ricevere una notifica
* Ricercare la causa di un problema
* Correggere un problema

Ora che si sa come monitorare il dispositivo, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Configurare le regole e le azioni per il dispositivo](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).