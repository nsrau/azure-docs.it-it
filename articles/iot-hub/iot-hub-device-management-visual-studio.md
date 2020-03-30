---
title: Azure IoT device management w/ Visual Studio Cloud Explorer
description: Usare Cloud Explorer per Visual Studio per la gestione dei dispositivi dell'hub IoT di Azure, con opzioni di gestione come i metodi diretti e le proprietà desiderate in dispositivi gemelli.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953192"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Usare Cloud Explorer per Visual Studio per la gestione dei dispositivi dell'hub IoT di Azure

![Diagramma end-to-end](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) è un'utile estensione di Visual Studio che consente di visualizzare le risorse di Azure, controllare le relative proprietà ed eseguire azioni fondamentali per gli sviluppatori dall'interno di Visual Studio. Include opzioni di gestione che consentono di eseguire varie attività.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opzione di gestione          | Attività                    |
|----------------------------|--------------------------------|
| Metodi diretti             | Far eseguire un'attività al dispositivo, quale l'avvio o l'arresto dell'invio di messaggi o il riavvio del dispositivo.                                        |
| Lettura dispositivo gemello           | Ottenere lo stato restituito da un dispositivo. Ad esempio, il dispositivo segnala che il LED sta lampeggiando.                                    |
| Aggiornamento dispositivo gemello         | Impostare determinati stati di un dispositivo, ad esempio impostare la luce verde per un LED o impostare l'intervallo di invio dei dati di telemetria su 30 minuti.         |
| Messaggi da cloud a dispositivo   | Inviare notifiche a un dispositivo. Ad esempio "Oggi è molto probabile che piova. Meglio non dimenticare l'ombrello".              |

Per altre informazioni sulle differenze e sull'uso di queste opzioni, vedere [Device-to-cloud communication guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud) e [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo).

i dispositivi gemelli sono documenti JSON che archiviano informazioni sullo stato dei dispositivi, tra cui metadati, configurazioni e condizioni. L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette. Per altre informazioni sui dispositivi gemelli, vedere [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

In questo articolo imparerai a usare Cloud Explorer per Visual Studio con varie opzioni di gestione nel computer di sviluppo.

## <a name="what-you-do"></a>Operazioni da fare

In questo articolo, eseguire Cloud Explorer per Visual Studio con varie opzioni di gestione.

## <a name="what-you-need"></a>Elementi necessari

È necessario soddisfare i prerequisiti seguenti:

- Una sottoscrizione di Azure attiva.

- Un hub IoT di Azure nella sottoscrizione.

- Microsoft Visual Studio 2017 Update 9 o versione successiva. In questo articolo viene utilizzato [Visual Studio 2017 o Visual Studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer component from Visual Studio Installer, which selected by default with Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Aggiornare Cloud Explorer alla versione più recente

Il componente Cloud Explorer del programma di installazione di Visual Studio per Visual Studio 2017 supporta solo il monitoraggio dei messaggi da dispositivo a cloud e da cloud a dispositivo. Per utilizzare Visual Studio 2017, scaricare e installare la versione più recente di [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Accedere per accedere all'hub

1. In Visual Studio selezionare Visualizza Cloud Explorer per aprire Cloud Explorer.In Visual Studio, select **View** > **Cloud Explorer** to open Cloud Explorer.

1. Selezionare l'icona Gestione account per visualizzare le sottoscrizioni.

    ![Icona Gestione account](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se è stato eseguito l'accesso ad Azure, vengono visualizzati gli account. Per accedere ad Azure per la prima volta, scegliere **Aggiungi un account.**

1. Selezionare le sottoscrizioni di Azure da usare e scegliere **Applica**.

1. Espandere l'abbonamento, quindi espandere **Hub IoT**.  In ogni hub è possibile visualizzare i dispositivi per tale hub. Fare clic con il pulsante destro del mouse su un dispositivo per accedere alle opzioni di gestione.

    ![Opzioni di gestione](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Metodi diretti

Per utilizzare i metodi diretti, procedere come segue:

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Invoke Device Direct Method** (Richiama metodo diretto dispositivo).

1. Immettere il nome del metodo e il payload in **Richiama metodo diretto**, quindi scegliere **OK**.

    I risultati vengono visualizzati in **Output**.

## <a name="update-device-twin"></a>Aggiornamento dispositivo gemello

Per modificare un dispositivo gemello, procedere come segue:

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Edit Device Twin** (Modifica dispositivo gemello).

   Viene aperto un file **azure-iot-device-twin.json** con il contenuto del dispositivo gemello.

1. Apportare alcune modifiche di **tag** o **campi properties.desired** al file **azure-iot-device-twin.json.**

1. Premere **CTRL+S** per aggiornare il dispositivo gemello.

   I risultati vengono visualizzati in **Output**.

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**.

1. Immettere il messaggio in **Invia messaggio C2D** e selezionare **OK**.

   I risultati vengono visualizzati in **Output**.

## <a name="next-steps"></a>Passaggi successivi

È stato descritto come usare Cloud Explorer per Visual Studio con varie opzioni di gestione.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
