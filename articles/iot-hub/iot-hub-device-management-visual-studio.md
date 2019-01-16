---
title: Gestione dei dispositivi IoT di Azure con Cloud Explorer per Visual Studio | Microsoft Docs
description: Usare Cloud Explorer per Visual Studio per la gestione dei dispositivi dell'hub IoT di Azure, con opzioni di gestione come i metodi diretti e le proprietà desiderate in dispositivi gemelli.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: f0cb740f04970aeed4342511a046862f05aaa736
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109226"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Usare Cloud Explorer per Visual Studio per la gestione dei dispositivi dell'hub IoT di Azure

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) è un'utile estensione di Visual Studio che consente di visualizzare le risorse di Azure, controllare le relative proprietà ed eseguire azioni fondamentali per gli sviluppatori dall'interno di Visual Studio. Include opzioni di gestione che consentono di eseguire varie attività.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opzione di gestione          | Attività                    |
|----------------------------|--------------------------------|
| Metodi diretti             | Far eseguire un'attività al dispositivo, quale l'avvio o l'arresto dell'invio di messaggi o il riavvio del dispositivo.                                        |
| Lettura dispositivo gemello           | Ottenere lo stato restituito da un dispositivo. Ad esempio, il dispositivo segnala che il LED sta lampeggiando.                                    |
| Aggiornamento dispositivo gemello         | Impostare determinati stati di un dispositivo, ad esempio impostare la luce verde per un LED o impostare l'intervallo di invio dei dati di telemetria su 30 minuti.         |
| Messaggi da cloud a dispositivo   | Inviare notifiche a un dispositivo. Ad esempio "Oggi è molto probabile che piova. Meglio non dimenticare l'ombrello".              |

Per altre informazioni sulle differenze e sull'uso di queste opzioni, vedere [Device-to-cloud communication guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud) e [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo).

I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette. Per altre informazioni sui dispositivi gemelli, vedere [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprende l'uso di Cloud Explorer per Visual Studio con varie opzioni di gestione nel proprio computer di sviluppo.

## <a name="what-you-do"></a>Operazioni da fare

Eseguire Cloud Explorer per Visual Studio con varie opzioni di gestione.

## <a name="what-you-need"></a>Elementi necessari

- Una sottoscrizione di Azure attiva.
- Un hub IoT di Azure nella sottoscrizione.
- Microsoft Visual Studio 2017 Update 8 o versioni successive
- Componente Cloud Explorer del programma di installazione di Visual Studio (selezionato per impostazione predefinita con Carico di lavoro di Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Aggiornare Cloud Explorer alla versione più recente

Il componente di Cloud Explorer del programma di installazione di Visual Studio supporta solo il monitoraggio dei messaggi da dispositivo a cloud e da cloud a dispositivo. È necessario scaricare e installare la versione più recente di [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) per accedere alle opzioni di gestione.

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

1. Nella finestra **Cloud Explorer** di Visual Studio fare clic sull'icona Gestione account. È possibile aprire la finestra Cloud Explorer dal menu **Visualizza** > **Cloud Explorer**.

    ![Fare clic su Gestione account](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Fare clic su **Gestisci account** in Cloud Explorer.
1. Fare clic su **Aggiungi un account** nella nuova finestra per accedere ad Azure per la prima volta.
1. Dopo l'accesso, verrà visualizzato l'elenco delle sottoscrizioni di Azure. Selezionare le sottoscrizioni di Azure da visualizzare e fare clic su **Applica**.
1. Espandere **Sottoscrizione in uso** > **Hub IoT**  > **Hub IoT in uso**. Verrà visualizzato l'elenco dei dispositivi sotto il nodo dell'hub IoT. Fare clic con il pulsante destro del mouse su un dispositivo per accedere alle opzioni di gestione.

    ![Opzioni di gestione](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Metodi diretti

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Invoke Device Direct Method** (Richiama metodo diretto dispositivo).
1. Immettere il nome e il payload del metodo nella casella di input.
1. I risultati verranno visualizzati nel riquadro di output **Hub IoT**.

## <a name="read-device-twin"></a>Lettura dispositivo gemello

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Edit Device Twin** (Modifica dispositivo gemello).
1. Verrà aperto un file **azure-iot-device-twin.json** con il contenuto del dispositivo gemello.

## <a name="update-device-twin"></a>Aggiornamento dispositivo gemello

1. Apportare alcune modifiche al campo **tags** o **properties.desired** nel file **azure-iot-device-twin.json**.
1. Premere **CTRL+S** per aggiornare il dispositivo gemello.
1. I risultati verranno visualizzati nel riquadro di output **Hub IoT**.

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**.
1. Immettere il messaggio nella casella di input.
1. I risultati verranno visualizzati nel riquadro di output **Hub IoT**.

## <a name="next-steps"></a>Passaggi successivi

È stato descritto come usare Cloud Explorer per Visual Studio con varie opzioni di gestione.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]