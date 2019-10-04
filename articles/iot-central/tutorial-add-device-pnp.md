---
title: Aggiungere un dispositivo reale a un'applicazione Azure IoT Central | Microsoft Docs
description: Un operatore può aggiungere un dispositivo reale all'applicazione Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878862"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>Esercitazione: Aggiungere un dispositivo simulato nell'applicazione Azure IoT Central (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come aggiungere e configurare un dispositivo simulato nell'applicazione Microsoft Azure IoT Central.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un nuovo dispositivo simulato
> * Usare un dispositivo simulato nell'esperienza di creazione

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, il creatore deve completare la prima esercitazione per creare l'applicazione Azure IoT Central:

* [Definire un nuovo tipo di dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (obbligatorio)

## <a name="add-a-simulated-device"></a>Aggiungere un dispositivo simulato

Per aggiungere un dispositivo reale all'applicazione, si usa il modello di dispositivo **Sensore ambientale** creato nell'esercitazione [Definire un nuovo tipo di dispositivo](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Per aggiungere un nuovo dispositivo in qualità di operatore, scegliere **Dispositivi** nel menu di spostamento a sinistra. La scheda **Dispositivi** include l'opzione **Tutti i dispositivi** e il modello di dispositivo **Sensore ambientale**.

1. Per aggiungere un dispositivo di tipo sensore ambientale simulato, selezionare **+ Nuovo**. Usare il valore di **ID dispositivo** suggerito o immettere un **ID dispositivo** personalizzato in lettere minuscole. È anche possibile immettere un nome per il nuovo dispositivo. Impostare l'interruttore **Simulato** su **Sì** e quindi selezionare **Crea**.

    ![Dispositivo simulato](./media/tutorial-add-device-pnp/simulated-device.png)

È ora possibile interagire con le visualizzazioni create per il modello di dispositivo usando dati simulati.

## <a name="use-a-simulated-device-to-improve-views"></a>Usare un dispositivo simulato per migliorare le visualizzazioni

Dopo aver creato un nuovo dispositivo simulato, il creatore può usare questo dispositivo per continuare a migliorare e arricchire le visualizzazioni per il modello di dispositivo.

1. Nella visualizzazione del dispositivo copiare il valore di **ID dispositivo** del dispositivo simulato creato.

1. Scegliere la scheda **Modelli di dispositivo** nel menu di spostamento a sinistra e selezionare il modello **Sensore ambientale**.

1. Selezionare la visualizzazione che si vuole modificare o creare una nuova visualizzazione. Fare clic su **Configura dispositivo in anteprima**. Qui è possibile scegliere di non creare un dispositivo di anteprima, di usare un dispositivo reale che è possibile configurare per i test oppure di usare un dispositivo esistente aggiunto in IoT Central.

1. Scegliere **Seleziona da un dispositivo in esecuzione** e immettere il valore di **ID dispositivo** del dispositivo simulato che è stato copiato.

1. Scegliere **Applica**. È ora possibile visualizzare lo stesso dispositivo simulato nell'esperienza di creazione di visualizzazioni dei modelli di dispositivo. Questa visualizzazione è particolarmente utile per i grafici e altri elementi visivi.

    ![Configurare il dispositivo in anteprima](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Aggiungere un nuovo dispositivo simulato
* Usare un dispositivo simulato nell'esperienza di creazione

Ora che è stato connesso un dispositivo simulato all'applicazione Azure IoT Central, ecco alcuni passaggi successivi consigliati.

Un operatore può apprendere come:

> [!div class="nextstepaction"]
> [Configurare le regole](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

Uno sviluppatore di dispositivi può apprendere come:

> [!div class="nextstepaction"]
> [Connect an MXChip IoT DevKit device to your Azure IoT Central application](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central)



