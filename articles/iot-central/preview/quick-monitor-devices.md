---
title: 'Avvio rapido: Monitorare i dispositivi in Azure IoT Central'
description: Questa guida di avvio rapido mostra come gli operatori possono usare l'applicazione Azure IoT Central per monitorare i dispositivi.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a6bc4b76dc00330e39526aec8bc5651b9abb590b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706734"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Guida introduttiva: Usare Azure IoT Central per monitorare i dispositivi (funzionalità di anteprima)

*Questo articolo è rivolto a operatori, autori e amministratori.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo avvio rapido illustra come un operatore può usare l'applicazione Microsoft Azure IoT Central per monitorare i dispositivi e modificare le impostazioni.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è opportuno completare i tre avvii rapidi precedenti [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md), [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-pnp-device.md) e [Configurare le regole e le azioni per il dispositivo](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Ricevere una notifica

Azure IoT Central invia le notifiche sui dispositivi come messaggi di posta elettronica. Il creatore ha aggiunto una regola che prevede l'invio di una notifica quando la temperatura in un dispositivo con sensore ambientale connesso supera una determinata soglia. Controllare i messaggi di posta elettronica inviati all'account scelto dal generatore per la ricezione delle notifiche.

Aprire il messaggio di posta elettronica ricevuto al termine dell'avvio rapido [Configurare le regole e le azioni per il dispositivo](quick-configure-rules.md). Nel messaggio di posta elettronica selezionare il collegamento al dispositivo:

![Messaggio di posta elettronica di notifica avvisi](media/quick-monitor-devices/email.png)

Nel browser viene aperta la visualizzazione **Dashboard** per il dispositivo simulato con sensore ambientale creato negli avvii rapidi precedenti:

![Dispositivo che ha attivato il messaggio di posta elettronica di notifica](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Ricercare la causa di un problema

Un operatore può visualizzare le informazioni sul dispositivo nelle pagine **Informazioni generali**, **Proprietà sensore ambientale** e **Comandi**. Il creatore ha personalizzato le pagine **Dashboard** e **Proprietà sensore ambientale** in modo da visualizzare informazioni importanti relative a un dispositivo di tipo sensore ambientale connesso.

Scegliere la visualizzazione **Informazioni generali** per vedere le informazioni sul dispositivo.

Il grafico del dashboard mostra il tracciato della temperatura del dispositivo. Si decide che la temperatura del dispositivo è troppo alta.

## <a name="remediate-an-issue"></a>Correggere un problema

Per apportare una modifica al dispositivo, usare la pagina **Proprietà sensore ambientale**.

Scegliere **Proprietà sensore ambientale**. Modificare **Brightness Level** (Livello luminosità) impostandolo su 10. Scegliere **Salva** per aggiornare il dispositivo. Quando il dispositivo conferma la modifica delle impostazioni, lo stato della proprietà cambia in **sincronizzato**:

![Aggiornamento delle impostazioni](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

* Ricevere una notifica
* Ricercare la causa di un problema
* Correggere un problema

Ora che si sa come monitorare il dispositivo, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Creare e gestire un modello di dispositivo](howto-set-up-template.md).
