---
title: Gestire la messaggistica dei dispositivi cloud hub IoT di Azure con Cloud Explorer per Visual Studio | Microsoft Docs
description: Informazioni su come usare Cloud Explorer per Visual Studio per monitorare dispositivi per i messaggi al cloud e inviare messaggi al dispositivo nell'hub IoT di Azure cloud.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 01/07/2018
ms.date: 05/06/2019
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440632"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usare Cloud Explorer per Visual Studio per inviare e ricevere messaggi tra il dispositivo e l'hub IoT

![Diagramma end-to-end](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) è un'utile estensione di Visual Studio che consente di visualizzare le risorse di Azure, controllare le relative proprietà ed eseguire azioni fondamentali per gli sviluppatori dall'interno di Visual Studio. Questo articolo approfondisce l'uso di Cloud Explorer per inviare e ricevere messaggi tra il dispositivo e l'hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Concetti legati all'esercitazione

Verrà descritto come usare Cloud Explorer per Visual Studio per monitorare i messaggi da dispositivo a cloud e inviare i messaggi da cloud a dispositivo. I messaggi da dispositivo a cloud possono includere dati di sensori raccolti dal dispositivo e inviati all'hub IoT. I messaggi da cloud a dispositivo possono includere comandi inviati dall'hub IoT al dispositivo, ad esempio per far lampeggiare un LED connesso a quest'ultimo.

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Usare Cloud Explorer per Visual Studio per monitorare i messaggi da dispositivo a cloud.
- Usare Cloud Explorer per Visual Studio per inviare i messaggi da cloud a dispositivo.

## <a name="what-you-need"></a>Elementi necessari

- Una sottoscrizione di Azure attiva.
- Un hub IoT di Azure nella sottoscrizione.
- Microsoft Visual Studio 2017 Update 8 o versioni successive
- Componente Cloud Explorer del programma di installazione di Visual Studio (selezionato per impostazione predefinita con Carico di lavoro di Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Aggiornare Cloud Explorer alla versione più recente

Il componente di Cloud Explorer del programma di installazione di Visual Studio supporta solo il monitoraggio dei messaggi da dispositivo a cloud e da cloud a dispositivo. Per inviare messaggi al dispositivo o al cloud, scaricare e installare la versione più recente di [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

1. Nella finestra **Cloud Explorer** di Visual Studio fare clic sull'icona Gestione account. È possibile aprire la finestra Cloud Explorer dal menu **Visualizza** > **Cloud Explorer**.

    ![Fare clic su Gestione account](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Fare clic su **Gestisci account** in Cloud Explorer.
1. Fare clic su **Aggiungi un account** nella nuova finestra per accedere ad Azure per la prima volta.
1. Dopo l'accesso, verrà visualizzato l'elenco delle sottoscrizioni di Azure. Selezionare le sottoscrizioni di Azure da visualizzare e fare clic su **Applica**.
1. Espandere **Sottoscrizione in uso** > **Hub IoT**  > **Hub IoT in uso**. Verrà visualizzato l'elenco dei dispositivi sotto il nodo dell'hub IoT.

    ![Elenco dei dispositivi](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorare i messaggi da dispositivo a cloud

Per monitorare i messaggi inviati dal dispositivo all'hub IoT, seguire questi passaggi:

1. Fare clic con il pulsante destro sull'hub IoT o sul dispositivo e selezionare **Start Monitoring D2C Message** (Avvia monitoraggio messaggi da dispositivo a cloud).

    ![Avvio del monitoraggio dei messaggi da dispositivo a cloud](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

1. I messaggi monitorati verranno visualizzati nel riquadro di output **Hub IoT**.

    ![Risultato del monitoraggio dei messaggi da dispositivo a cloud](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

1. Per interrompere il monitoraggio, fare clic su qualsiasi dispositivo o hub IoT e selezionare **Stop Monitoring D2C Message** (Interrompi monitoraggio messaggi da dispositivo a cloud).

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**.

    ![Invio di un messaggio da cloud a dispositivo](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

1. Immettere il messaggio nella casella di input.
1. I risultati verranno visualizzati nel riquadro di output **Hub IoT**.

    ![Risultato dell'invio di un messaggio da cloud a dispositivo](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Passaggi successivi

È stato appreso come monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo tra il dispositivo IoT e l'hub IoT di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]