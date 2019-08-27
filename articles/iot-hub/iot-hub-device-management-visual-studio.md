---
title: Gestione dei dispositivi IoT di Azure con Cloud Explorer per Visual Studio | Microsoft Docs
description: Usare Cloud Explorer per Visual Studio per la gestione dei dispositivi dell'hub IoT di Azure, con opzioni di gestione come i metodi diretti e le proprietà desiderate in dispositivi gemelli.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: e05ba421a4535e6e424e65a1f2271d19f9d9abf4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70048724"
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

I dispositivi gemelli sono documenti JSON che archiviano le informazioni sullo stato del dispositivo, inclusi metadati, configurazioni e condizioni. L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette. Per altre informazioni sui dispositivi gemelli, vedere [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Questo articolo illustra come usare la Cloud Explorer per Visual Studio con varie opzioni di gestione nel computer di sviluppo.

## <a name="what-you-do"></a>Operazioni da fare

In questo articolo, eseguire Cloud Explorer per Visual Studio con diverse opzioni di gestione.

## <a name="what-you-need"></a>Elementi necessari

Sono necessari i prerequisiti seguenti:

- Una sottoscrizione di Azure attiva.

- Un hub IoT di Azure nella sottoscrizione.

- Microsoft Visual Studio 2017 aggiornamento 9 o versione successiva. Questo articolo usa [Visual studio 2017 o Visual studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer componente da Programma di installazione di Visual Studio, che è selezionato per impostazione predefinita con il carico di lavoro di Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aggiornare Cloud Explorer alla versione più recente

Il componente Cloud Explorer da Programma di installazione di Visual Studio per Visual Studio 2017 supporta solo il monitoraggio dei messaggi da dispositivo a cloud e da cloud a dispositivo. Per usare Visual Studio 2017, scaricare e installare la [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)più recente.

## <a name="sign-in-to-access-your-hub"></a>Accedi per accedere all'hub

1. In Visual Studio selezionare **Visualizza** > **Cloud Explorer** per aprire Cloud Explorer.

1. Selezionare l'icona di gestione account per visualizzare le sottoscrizioni.

    ![Icona di gestione account](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se è stato eseguito l'accesso ad Azure, vengono visualizzati gli account. Per accedere ad Azure per la prima volta, scegliere **Aggiungi un account**.

1. Selezionare le sottoscrizioni di Azure che si vuole usare e scegliere **applica**.

1. Espandere la sottoscrizione, quindi espandere **Hub**Internet.  In ogni hub è possibile visualizzare i dispositivi per l'hub. Fare clic con il pulsante destro del mouse su un dispositivo per accedere alle opzioni di gestione.

    ![Opzioni di gestione](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Metodi diretti

Per usare i metodi diretti, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Invoke Device Direct Method** (Richiama metodo diretto dispositivo).

1. Immettere il nome del metodo e il payload nel **Metodo Invoke Direct**, quindi selezionare **OK**.

    I risultati vengono visualizzati nell' **output**.

## <a name="update-device-twin"></a>Aggiornamento dispositivo gemello

Per modificare un dispositivo gemello, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul dispositivo e scegliere **Edit Device Twin** (Modifica dispositivo gemello).

   Viene aperto un file **Azure-Internet per dispositivi gemelli. JSON** con il contenuto del dispositivo gemello.

1. Apportare alcune modifiche ai **tag** o alle **Proprietà.** i campi desiderati nel file **Azure-Internet.**

1. Premere **CTRL+S** per aggiornare il dispositivo gemello.

   I risultati vengono visualizzati nell' **output**.

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Fare clic con il pulsante destro sul dispositivo e selezionare **Invia messaggio da cloud a dispositivo**.

1. Immettere il messaggio in **Invia messaggio C2D** e selezionare **OK**.

   I risultati vengono visualizzati nell' **output**.

## <a name="next-steps"></a>Passaggi successivi

È stato descritto come usare Cloud Explorer per Visual Studio con varie opzioni di gestione.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
