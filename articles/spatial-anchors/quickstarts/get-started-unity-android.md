---
title: "Avvio rapido: Creare un'app Android in Unity con gli ancoraggi nello spazio di Azure | Microsoft Docs"
description: In questa guida introduttiva si apprenderà a compilare un'app per Android con Unity usando il servizio Ancoraggi nello spazio.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 642abfb99b40d67802b7194ad225ebcd2872a72b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135134"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app Android in Unity con gli ancoraggi nello spazio di Azure

Questo argomento di avvio rapido illustra come creare un'app Android in Unity usando [Ancoraggi nello spazio di Azure](../overview.md). Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine si avrà un'app ARCore per Android compilata con Unity in grado di salvare e richiamare un ancoraggio spaziale.

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

- Un computer Windows o macOS con <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> e <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Se in esecuzione su Windows, sarà necessario anche <a href="https://git-scm.com/download/win" target="_blank">Git per Windows</a>.
  - Se in esecuzione su macOS, installare Git tramite HomeBrew. Immettere il comando seguente in una singola riga del terminale: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Quindi eseguire `brew install git`.
- Un dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">abilitato per lo sviluppo</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">idoneo per ARCore</a>.
- L'app deve usare la versione **1.7** di ARCore SDK per Unity.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Scaricare e aprire il progetto di esempio di Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Nel riquadro **Progetto** passare a `Assets/AzureSpatialAnchorsPlugin/Examples` e aprire il file della scena `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salvare la scena selezionando **File** -> **Salva**.

## <a name="export-the-android-studio-project"></a>Esportare il progetto Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Verificare che non sia presente un segno di spunta accanto alla casella **Export Project** (Esporta progetto). Fare clic su **Build And Run** (Compila ed esegui). Verrà chiesto di salvare il file `.apk`, per il quale è possibile scegliere qualsiasi nome.

Seguire le istruzioni nell'app per posizionare e richiamare un ancoraggio.

> [!NOTE]
> Se durante l'esecuzione dell'app non si vede la videocamera sullo sfondo, ma si vede invece, ad esempio, uno sfondo vuoto, blu o un'altra trama, è probabilmente necessario reimportare gli asset in Unity. Arrestare l'app. Nel menu in alto in Unity scegliere **Assets -> Reimport all** (Asset -> Reimporta tutto). Quindi eseguire di nuovo l'app.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)
