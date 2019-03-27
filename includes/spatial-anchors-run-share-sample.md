---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305083"
---
## <a name="set-up-your-device"></a>Configurare il dispositivo

### <a name="set-up-an-android-device"></a>Configurare un dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurare un dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Nel riquadro **Progetto** passare a `Assets/AzureSpatialAnchorsPlugin/Examples` e aprire il file della scena `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Nel riquadro **Inspector** (Controllo) immettere il valore di `Sharing Anchors Service url` (relativo alla distribuzione di Azure dell'app Web ASP.NET) come valore per `Base Sharing Url`, sostituendo `index.html` con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

Salvare la scena selezionando **File** > **Salva**.

## <a name="to-deploy-the-app-to-an-android-device"></a>Per distribuire l'app in un dispositivo Android

Accedere al dispositivo Android e connetterlo al computer con un cavo USB.

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) inserire un segno di spunta accanto alla scena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e deselezionare i segni di spunta per tutte le altre scene.

Assicurarsi che l'opzione **Export Project** (Esporta progetto) non sia selezionata. Selezionare **Build And Run** (Compila ed esegui). Verrà richiesto di salvare il file `.apk`. È possibile scegliere qualsiasi nome.

Seguire le istruzioni nell'app. È possibile selezionare **Create & Share Anchor** (Crea e condividi ancoraggio) oppure **Locate Shared Anchor** (Individua ancoraggio condiviso). La prima opzione consente di creare un ancoraggio che successivamente potrà essere individuato nello stesso dispositivo o in un dispositivo diverso. La seconda opzione, se è già stata eseguita l'app, nello stesso dispositivo o in uno diverso, consente di individuare gli ancoraggi condivisi in precedenza.

## <a name="to-deploy-the-app-to-an-ios-device"></a>Per distribuire l'app in un dispositivo iOS

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) inserire un segno di spunta accanto alla scena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e deselezionare i segni di spunta per tutte le altre scene.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Seguire le istruzioni nell'app. È possibile selezionare **Create & Share Anchor** (Crea e condividi ancoraggio) oppure **Locate Shared Anchor** (Individua ancoraggio condiviso). La prima opzione consente di creare un ancoraggio che successivamente potrà essere individuato nello stesso dispositivo o in un dispositivo diverso. La seconda opzione, se è già stata eseguita l'app, nello stesso dispositivo o in uno diverso, consente di individuare gli ancoraggi condivisi in precedenza.

In Xcode arrestare l'app selezionando **Stop** (Arresta).
