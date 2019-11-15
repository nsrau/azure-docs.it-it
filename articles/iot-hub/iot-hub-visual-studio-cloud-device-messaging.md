---
title: Usare Visual Studio Cloud Explorer per gestire la messaggistica dei dispositivi dell'hub Azure
description: Informazioni su come usare Cloud Explorer per Visual Studio per monitorare dispositivi per i messaggi al cloud e inviare messaggi al dispositivo nell'hub IoT di Azure cloud.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74079483"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usare Cloud Explorer per Visual Studio per inviare e ricevere messaggi tra il dispositivo e l'hub IoT

![Diagramma end-to-end](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) è un'utile estensione di Visual Studio che consente di visualizzare le risorse di Azure, controllare le relative proprietà ed eseguire azioni fondamentali per gli sviluppatori dall'interno di Visual Studio. Questo articolo è incentrato su come usare Cloud Explorer per inviare e ricevere messaggi tra il dispositivo e l'hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Questo articolo illustra come usare Cloud Explorer per Visual Studio per monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo. I messaggi da dispositivo a cloud possono includere dati di sensori raccolti dal dispositivo e inviati all'hub IoT. I messaggi da cloud a dispositivo possono includere comandi inviati dall'hub IoT al dispositivo, ad esempio per far lampeggiare un LED connesso a quest'ultimo.

## <a name="what-you-do"></a>Operazioni da fare

In questo articolo vengono eseguite le attività seguenti:

- Usare Cloud Explorer per Visual Studio per monitorare i messaggi da dispositivo a cloud.

- Usare Cloud Explorer per Visual Studio per inviare i messaggi da cloud a dispositivo.

## <a name="what-you-need"></a>Elementi necessari

È necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure attiva.

- Un hub IoT di Azure nella sottoscrizione.

- Microsoft Visual Studio 2017 aggiornamento 9 o versione successiva. Questo articolo usa [Visual Studio 2019](https://www.visualstudio.com/vs/).

- Il componente Cloud Explorer da Programma di installazione di Visual Studio, che è selezionato per impostazione predefinita con il carico di lavoro di Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aggiornare Cloud Explorer alla versione più recente

Il componente Cloud Explorer da Programma di installazione di Visual Studio per Visual Studio 2017 supporta solo il monitoraggio dei messaggi da dispositivo a cloud e da cloud a dispositivo. Per usare Visual Studio 2017, scaricare e installare la [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)più recente.

## <a name="sign-in-to-access-your-hub"></a>Accedi per accedere all'hub

Per accedere all'hub, seguire questa procedura:

1. In Visual Studio selezionare **visualizza** > **Cloud Explorer** per aprire Cloud Explorer.

1. Selezionare l'icona di gestione account per visualizzare le sottoscrizioni.

    ![Icona di gestione account](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se è stato eseguito l'accesso ad Azure, vengono visualizzati gli account. Per accedere ad Azure per la prima volta, scegliere **Aggiungi un account**.

1. Selezionare le sottoscrizioni di Azure che si vuole usare e scegliere **applica**.

1. Espandere la sottoscrizione, quindi espandere **Hub**Internet.  In ogni hub è possibile visualizzare i dispositivi per l'hub.

    ![Elenco dei dispositivi](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorare i messaggi da dispositivo a cloud

Per monitorare i messaggi inviati dal dispositivo all'hub IoT, seguire questi passaggi:

1. Fare clic con il pulsante destro sull'hub IoT o sul dispositivo e selezionare **Start Monitoring D2C Message** (Avvia monitoraggio messaggi da dispositivo a cloud).

    ![Avvio del monitoraggio dei messaggi da dispositivo a cloud](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. I messaggi monitorati vengono visualizzati sotto **output**.

    ![Risultato del monitoraggio dei messaggi da dispositivo a cloud](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Per interrompere il monitoraggio, fare clic su qualsiasi dispositivo o hub IoT e selezionare **Stop Monitoring D2C Message** (Interrompi monitoraggio messaggi da dispositivo a cloud).

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**.

1. Immettere il messaggio nella casella di input.

    ![Invio di un messaggio da cloud a dispositivo](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    I risultati vengono visualizzati in **output**.

    ![Risultato dell'invio di un messaggio da cloud a dispositivo](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Passaggi successivi

È stato appreso come monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo tra il dispositivo IoT e l'hub IoT di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]