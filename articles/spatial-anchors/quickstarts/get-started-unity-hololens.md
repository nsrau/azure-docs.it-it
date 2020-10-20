---
title: "Guida introduttiva: Creare un'app HoloLens con Unity"
description: In questa guida introduttiva viene illustrato come compilare un'app HoloLens con Unity usando il servizio Ancoraggi nello spazio.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c7aa4b90d52ac52a9f879d9e5cc16ec93818b70d
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939733"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app HoloLens in Unity che usa gli ancoraggi nello spazio di Azure

Questo argomento di avvio rapido illustra come creare un'app HoloLens in Unity che usa [Ancoraggi nello spazio di Azure](../overview.md). Ancoraggi nello spazio è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista con oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine si avrà un'app HoloLens compilata con Unity in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

- Creare un account di Ancoraggi nello spazio.
- Preparare le impostazioni di compilazione di Unity.
- Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio.
- Esportare il progetto Visual Studio HoloLens.
- Distribuire l'app ed eseguirla in un dispositivo HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

- È necessario un computer Windows in cui sono installati <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a> e <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> o versione successiva. L'installazione di Visual Studio deve includere il carico di lavoro **Sviluppo di app per la piattaforma UWP (Universal Windows Platform)** e il componente **Windows 10 SDK (10.0.18362.0 o versione successiva)** . È inoltre necessario installare <a href="https://git-scm.com/download/win" target="_blank">Git per Windows</a> e <a href="https://git-lfs.github.com/">Git LFS</a>.
- È necessario un dispositivo HoloLens con la [modalità sviluppatore](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) abilitata. Nel dispositivo deve essere installato l'[aggiornamento di Windows 10 (maggio 2020)](https://docs.microsoft.com/windows/mixed-reality/whats-new/release-notes-may-2020). Per aggiornare alla versione più recente per HoloLens, aprire l'app **Impostazioni**, scegliere **Aggiornamento e sicurezza**, quindi selezionare **Verifica disponibilità aggiornamenti**.
- Nell'app è necessario abilitare la funzionalità **SpatialPerception**. Questa impostazione si trova in **Build Settings (Impostazioni compilazione)**  > **Player Settings (Impostazioni lettore)**  > **Publishing Settings (Impostazioni pubblicazione)**  > **Capabilities (Funzionalità)** .
- Nell'app è necessario abilitare **Virtual Reality Supported** (Realtà virtuale supportata) con **Windows Mixed Reality SDK** (SDK realtà mista Windows). Questa impostazione si trova in **Build Settings (Impostazioni compilazione)**  > **Player Settings (Impostazioni lettore)**  > **XR Settings (Impostazioni realtà mista)** .

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Scaricare e aprire il progetto di esempio di Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

Nella sezione **Platform** (Piattaforma) selezionare **Universal Windows Platform** (Piattaforma UWP (Universal Windows Platform)). Impostare quindi **HoloLens** come **Target Device** (Dispositivo di destinazione).

Selezionare **Switch Platform** (Cambia piattaforma) per impostare **Universal Windows Platform** (Piattaforma UWP (Universal Windows Platform)) come piattaforma. Unity potrebbe chiedere di installare i componenti di supporto della piattaforma UWP se sono mancanti.

![Finestra di impostazioni di compilazione di Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Chiudere la finestra **Build Settings** (Impostazioni compilazione).

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>Esportare il progetto Visual Studio HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selezionare **Compila**. Nella finestra di dialogo selezionare una cartella in cui esportare il progetto HoloLens Visual Studio.

Al termine dell'esportazione, verrà visualizzata una cartella contenente il progetto HoloLens esportato.

## <a name="deploy-the-hololens-application"></a>Distribuire l'applicazione HoloLens

Nella cartella fare doppio clic su **HelloAR U3D.sln** per aprire il progetto in Visual Studio.

Impostare **Configurazione soluzione** su **Versione**, impostare **Piattaforme soluzione** su **x86** e selezionare **Dispositivo** nelle opzioni di destinazione della distribuzione.

Se si usa HoloLens 2, usare **ARM64** come **piattaforma soluzione**, invece di **x86**.

   ![Configurazione di Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Accendere il dispositivo HoloLens, eseguire l'accesso e connetterlo al PC con un cavo USB.

Selezionare **Debug** > **Avvia debug** per distribuire l'app e avviare il debug.

Nell'app selezionare **BasicDemo** usando i tasti di direzione, quindi fare clic sul pulsante **Vai** per eseguire la demo. Seguire le istruzioni posizionare e richiamare un ancoraggio.

![Screenshot 1](./media/get-started-unity-hololens/screenshot-1.jpg)
![Screenshot 2](./media/get-started-unity-hololens/screenshot-2.jpg)
![Screenshot 3](./media/get-started-unity-hololens/screenshot-3.jpg)
![Screenshot 4](./media/get-started-unity-hololens/screenshot-4.jpg)

In Visual Studio arrestare l'app selezionando **Arresta debug** o premendo MAIUSC+F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Procedura: Configurare Ancoraggi nello spazio di Azure in un progetto Unity](../how-tos/setup-unity-project.md)
