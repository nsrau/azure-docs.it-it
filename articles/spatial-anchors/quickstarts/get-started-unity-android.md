---
title: Guida introduttiva - Creare un'app Unity per Android con Ancoraggi nello spazio di Azure | Microsoft Docs
description: In questa guida introduttiva si apprenderà a compilare un'app per Android con Unity usando il servizio Ancoraggi nello spazio.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e92c812ffc8b72fe79248c602e48ff01ef9fefcb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961015"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app Unity per Android con Ancoraggi nello spazio di Azure

Questa Guida introduttiva illustra come creare un'app Unity per Android usando [Ancoraggi nello spazio di Azure](../overview.md). Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine si avrà un'app ARCore per Android compilata con Unity in grado di salvare e richiamare un ancoraggio spaziale.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Preparare le impostazioni di compilazione di Unity
> * Scaricare e importare ARCore SDK per Unity
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Esportare il progetto Android Studio
> * Distribuire ed eseguire l'app in un dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

- Un computer Windows o macOS con <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> e <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a> installati.
- Un dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">abilitato per lo sviluppo</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">idoneo per ARCore</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Aprire il progetto di esempio in Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Nel riquadro **Progetto** passare a `Assets/AzureSpatialAnchorsPlugin/Examples` e aprire il file della scena `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salvare la scena selezionando **File** -> **Salva**.

## <a name="export-the-android-studio-project"></a>Esportare il progetto Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selezionare **Esporta** per aprire una finestra di dialogo. Selezionare quindi una cartella in cui esportare il progetto Android Studio.

Al termine dell'esportazione verrà visualizzata una cartella contenente il progetto Android Studio esportato, con una sottocartella denominata **HelloAR U3D**.

## <a name="deploy-the-android-application"></a>Distribuire l'applicazione Android

Aprire Android Studio e selezionare **Open an existing Android Studio project** (Apri un progetto di Android Studio esistente). Selezionare quindi la sottocartella **HelloAR U3D** dal progetto Android Studio esportato e fare clic su **OK**.

All'apertura viene chiesto di usare il wrapper Gradle. Scegliere **OK** per usare il wrapper Gradle e aprire il progetto.

Accendere il dispositivo Android, eseguire l'accesso e connetterlo al PC con un cavo USB.

Selezionare **Run** (Esegui) sulla barra degli strumenti di Android Studio.

![Distribuzione ed esecuzione in Android Studio](./media/get-started-unity-android/android-studio-deploy-run.png)

Selezionare il dispositivo Android nella finestra di dialogo **Select Deployment Target** (Seleziona destinazione distribuzione) e scegliere **OK** per eseguire l'app nel dispositivo Android.

Seguire le istruzioni nell'app per posizionare e richiamare un ancoraggio.

> [!NOTE]
> Se durante l'esecuzione dell'app non si vede la videocamera sullo sfondo, ma si vede invece, ad esempio, uno sfondo vuoto, blu o un'altra trama, è probabilmente necessario reimportare gli asset in Unity. Arrestare l'app. Nel menu in alto in Unity scegliere **Assets -> Reimport all** (Asset -> Reimporta tutto). Quindi eseguire di nuovo l'app.

Arrestare l'app selezionando **Stop** (Arresta) sulla barra degli strumenti di Android Studio.

![Arresto dell'app in Android Studio](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)
