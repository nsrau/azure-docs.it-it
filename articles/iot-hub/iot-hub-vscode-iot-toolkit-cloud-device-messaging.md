---
title: Gestire la messaggistica dei dispositivi cloud hub IoT di Azure con Azure IoT Tools per Visual Studio Code | Microsoft Docs
description: Informazioni su come usare Azure IoT Tools per Visual Studio Code per monitorare dispositivi per i messaggi al cloud e inviare messaggi al dispositivo nell'hub IoT di Azure cloud.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: dd56db628dd8a25ab2664f8f1c16b8ac45996549
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443564"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usare Azure IoT Tools per Visual Studio Code per inviare e ricevere messaggi tra il dispositivo e l'hub IoT

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) è un'utile estensione di Visual Studio Code che semplifica la gestione dell'hub IoT e lo sviluppo di applicazioni IoT. Questo articolo descrive come usare Azure IoT Tools per Visual Studio Code e inviare e ricevere messaggi tra il dispositivo e l'hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-will-learn"></a>Concetti legati all'esercitazione

Informazioni su come usare Azure IoT Tools per Visual Studio Code per monitorare i messaggi dal dispositivo al cloud e inviare messaggi dal cloud al dispositivo. I messaggi da dispositivo a cloud possono includere dati di sensori raccolti dal dispositivo e inviati all'hub IoT. I messaggi da cloud a dispositivo possono includere comandi inviati dall'hub IoT al dispositivo per far lampeggiare un LED connesso a quest'ultimo.

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Usare Azure IoT Tools per Visual Studio Code per monitorare i messaggi dal dispositivo al cloud.
- Usare Azure IoT Tools per Visual Studio Code per inviare i messaggi dal cloud al dispositivo.

## <a name="what-you-need"></a>Elementi necessari

- Una sottoscrizione di Azure attiva.
- Un hub IoT di Azure nella sottoscrizione.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Gli strumenti di Azure IoT per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) oppure [aprire questo collegamento in Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

1. Nella visualizzazione **Explorer** del codice di Visual Studio, espandere la sezione **dispositivi Azure IoT Hub** nell'angolo in basso a sinistra.
1. Fare clic su **Seleziona hub IoT** nel menu di scelta rapida.
1. Verrà visualizzata una finestra popup nell'angolo in basso a destra che consentirà di accedere ad Azure per la prima volta.
1. Dopo l'accesso, verrà visualizzato l'elenco di sottoscrizioni di Azure; selezionare quindi la sottoscrizione di Azure e l'hub IoT.
1. Verrà visualizzato in pochi secondi l'elenco dei dispositivi nella scheda **Dispositivi di Azure IoT hub di Azure**.

   > [!Note]
   > È anche possibile completare la configurazione scegliendo **Set IoT Hub Connection String** (Configura la stringa di connessione dell'hub IoT). Immettere la stringa di connessione per l'hub IoT a cui si connette il dispositivo IoT nella finestra popup.
   
## <a name="monitor-device-to-cloud-messages"></a>Monitorare i messaggi da dispositivo a cloud

Per monitorare i messaggi inviati dal dispositivo all'hub IoT, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Inizia monitoraggio messaggi da dispositivo a cloud**.
1. I messaggi monitorati verranno visualizzati nella visualizzazione **OUTPUT** > **Toolkit dell'hub IoT di Azure**.
1. Per arrestare il monitoraggio, fare clic con il pulsante destro sulla visualizzazione **OUTPUT** e selezionare **Arrestare monitoraggio dei messaggi dal dispositivo al cloud**.

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**. 
1. Immettere il messaggio nella casella di input.
1. I risultati verranno visualizzati nella visualizzazione **OUTPUT** > **Toolkit dell'hub IoT di Azure**.

## <a name="next-steps"></a>Passaggi successivi

È stato appreso come monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo tra il dispositivo IoT e l'hub IoT di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
