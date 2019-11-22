---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882327"
---
## <a name="androidtabandroid"></a>[Android](#tab/Android)

L'esempio Java Android supporta la condivisione tra dispositivi.
Aprire il file `SharedActivity.java` dalla cartella di esempi in Android Studio. Immettere l'URL ottenuto nel passaggio precedente (dalla distribuzione di Azure per l'app Web ASP.NET) come valore per `SharingAnchorsServiceUrl` nel file `SharedActivity.java`. Sostituire `index.html` nell'URL con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="iostabios"></a>[iOS](#tab/iOS)

L'esempio Objective-C iOS supporta la condivisione tra dispositivi.
Aprire il file `SharedDemoViewController.m` nella cartella di esempi. Immettere l'URL ottenuto nel passaggio precedente (dalla distribuzione di Azure per l'app Web ASP.NET) come valore per `SharingAnchorsServiceUrl` nel file `SharedActivity.java`. Sostituire `index.html` nell'URL con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarintabxamarin"></a>[Xamarin](#tab/Xamarin)

Gli esempi Xamarin Android e iOS supportano entrambi la condivisione tra dispositivi.
Aprire il file `AccountDetails.cs` nella cartella di esempi. Immettere l'URL ottenuto nel passaggio precedente (dalla distribuzione di Azure per l'app Web ASP.NET) come valore per `AnchorSharingServiceUrl` nel file `SharedActivity.java`. Sostituire `index.html` nell'URL con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unitytabunity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurare un dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurare un dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Nel riquadro **Progetto** passare a `Assets/AzureSpatialAnchorsPlugin/Examples` e aprire il file della scena `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Nel riquadro **Progetto** passare a `Assets\AzureSpatialAnchors.Examples\Resources`. Selezionare `SpatialAnchorSamplesConfig`. Quindi, nel riquadro **Inspector** (Controllo) immettere il valore di `Sharing Anchors Service url` (relativo alla distribuzione di Azure dell'app Web ASP.NET) come valore per `Base Sharing Url`, sostituendo `index.html` con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

Salvare la scena selezionando **File** > **Salva**.

## <a name="deploy-to-your-device"></a>Eseguire la distribuzione nel dispositivo

### <a name="deploy-to-android-device"></a>Distribuzione in un dispositivo Android

Accedere al dispositivo Android e connetterlo al computer con un cavo USB.

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) assicurarsi che tutte le scene abbiano un segno di spunta.

Assicurarsi che l'opzione **Export Project** (Esporta progetto) non sia selezionata. Selezionare **Build And Run** (Compila ed esegui). Verrà richiesto di salvare il file `.apk`. È possibile scegliere qualsiasi nome.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Distribuzione in un dispositivo iOS

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) assicurarsi che tutte le scene abbiano un segno di spunta.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

In Xcode arrestare l'app selezionando **Stop** (Arresta).
