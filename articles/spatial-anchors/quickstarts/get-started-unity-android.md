---
title: 'Guida introduttiva: Creare una nuova app Android'
description: In questa guida introduttiva si apprenderà a compilare un'app per Android con Unity usando il servizio Ancoraggi nello spazio.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c84ebfe9752b8594e97c7c72d706ee25c4852f6e
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538137"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app Android in Unity con gli ancoraggi nello spazio di Azure

Questo argomento di avvio rapido illustra come creare un'app Android in Unity usando [Ancoraggi nello spazio di Azure](../overview.md). Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine si avrà un'app ARCore per Android compilata con Unity in grado di salvare e richiamare un ancoraggio spaziale.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Preparare le impostazioni di compilazione di Unity
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Esportare il progetto Android Studio
> * Distribuire ed eseguire l'app in un dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

- Un computer Windows o macOS con <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a>, inclusi i moduli **Android Build Support** con **Android SDK & NDK Tools** e **OpenJDK**.
  - Se in esecuzione in Windows, sarà necessario anche <a href="https://git-scm.com/download/win" target="_blank">Git per Windows</a> e <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Se in esecuzione su macOS, installare Git tramite HomeBrew. Immettere il comando seguente in una singola riga del terminale: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Eseguire quindi `brew install git` e `brew install git-lfs`.
- Un dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">abilitato per lo sviluppo</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">idoneo per ARCore</a>.
  - Potrebbero essere necessari driver di dispositivo aggiuntivi per la comunicazione del computer con il dispositivo Android. Per altre informazioni e istruzioni, vedere [qui](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Scaricare e aprire il progetto di esempio di Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Esportare il progetto Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selezionare il dispositivo in **Esegui dispositivo** e quindi selezionare **Compila ed esegui**. Verrà chiesto di salvare un file `.apk` per cui è possibile scegliere qualsiasi nome.

Nell'app selezionare **BasicDemo** usando i tasti di direzione, quindi fare clic sul pulsante **Vai** per eseguire la demo. Seguire le istruzioni posizionare e richiamare un ancoraggio.

![Screenshot 1](./media/get-started-unity-android/screenshot-1.jpg)
![Screenshot 2](./media/get-started-unity-android/screenshot-2.jpg)
![Screenshot 3](./media/get-started-unity-android/screenshot-3.jpg)

Seguire le istruzioni nell'app per posizionare e richiamare un ancoraggio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="rendering-issues"></a>Problemi di rendering

Se durante l'esecuzione dell'app non si vede la videocamera sullo sfondo, ma si vede invece, ad esempio, uno sfondo vuoto, blu o un'altra trama, probabilmente è necessario reimportare gli asset in Unity. Arrestare l'app. Nel menu in alto in Unity scegliere **Assets -> Reimport all** (Asset -> Reimporta tutto). Quindi eseguire di nuovo l'app.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Procedura: Configurare Ancoraggi nello spazio di Azure in un progetto Unity](../how-tos/setup-unity-project.md)
