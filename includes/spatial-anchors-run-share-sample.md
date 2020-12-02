---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 9554e04c82299016076f09f85f604af10a94ab4a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185415"
---
## <a name="android"></a>[Android](#tab/Android)

L'esempio Java Android supporta la condivisione tra dispositivi.

In Android Studio aprire il file *SharedActivity.java* nella cartella di esempi. 

Immettere l'URL copiato nel passaggio precedente (dalla distribuzione di Azure per l'app Web ASP.NET) come valore per `SharingAnchorsServiceUrl` nel file *SharedActivity.java*. 

Sostituire `index.html` nell'URL con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

L'esempio Objective-C iOS supporta la condivisione tra dispositivi.

Aprire il file *SharedDemoViewController.m* nella cartella di esempi. 

Immettere l'URL ottenuto nel passaggio precedente (dalla distribuzione di Azure per l'app Web ASP.NET) come valore per `SharingAnchorsServiceUrl` nel file *SharedDemoViewController.m*. 

Sostituire `index.html` nell'URL con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

Distribuire l'app nel dispositivo. 

Dopo l'avvio dell'app, selezionare l'opzione **Tap to start Shared Demo** (Toccare per avviare la demo condivisa), quindi seguire le istruzioni nell'app. È possibile selezionare **Tap to locate Anchor by its anchor number** (Toccare per individuare l'ancoraggio in base al relativo numero) o **Tap to create Anchor and save it to the service** (Toccare per creare l'ancoraggio e salvarlo nel servizio).

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Gli esempi Xamarin Android e iOS supportano entrambi la condivisione tra dispositivi.

Aprire il file *AccountDetails.cs* nella cartella di esempi. 

Immettere l'URL ottenuto nel passaggio precedente (dalla distribuzione di Azure per l'app Web ASP.NET) come valore per `AnchorSharingServiceUrl` nel file *AccountDetails.cs*. 

Sostituire `index.html` nell'URL con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurare un dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurare un dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Nel riquadro **Progetto** passare a `Assets\AzureSpatialAnchors.Examples\Resources`. 

Selezionare **SpatialAnchorSamplesConfig**. Quindi, nel riquadro **Inspector** (Controllo) immettere l'URL `Sharing Anchors Service` (dalla distribuzione di Azure per l'app Web ASP.NET) come valore per `Base Sharing Url`. Sostituire `index.html` con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

Salvare la scena selezionando **File** > **Salva**.

## <a name="deploy-to-your-device"></a>Eseguire la distribuzione nel dispositivo

### <a name="deploy-to-an-android-device"></a>Eseguire la distribuzione in un dispositivo Android

Accedere al dispositivo Android e connetterlo al computer con un cavo USB.

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) assicurarsi che tutte le scene abbiano un segno di spunta.

Assicurarsi che l'opzione **Export Project** (Esporta progetto) non sia selezionata. Selezionare **Build And Run** (Compila ed esegui). Verrà chiesto di salvare il file con estensione *apk*. È possibile scegliere qualsiasi nome.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Distribuzione in un dispositivo iOS

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) assicurarsi che tutte le scene abbiano un segno di spunta.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

In Xcode arrestare l'app selezionando **Stop** (Arresta).
