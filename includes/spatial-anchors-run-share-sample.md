---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631168"
---
## <a name="set-up-your-device"></a>Configurare il dispositivo

In Unity aprire il progetto nella cartella `Unity`.

![Fine Unità](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>Configurare un dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurare un dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Nel riquadro **Progetto** passare a `Assets/AzureSpatialAnchorsPlugin/Examples` e aprire il file della scena `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Nel riquadro **Inspector** (Controllo) immettere il valore di `Sharing Anchors Service url` (relativo alla distribuzione di Azure dell'app Web ASP.NET) come valore per `Base Sharing Url`, sostituendo `index.html` con `api/anchors`. L'aspetto dovrebbe risultare simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

Salvare la scena selezionando **File** > **Salva**.

## <a name="deploy-to-your-device"></a>Eseguire la distribuzione nel dispositivo

### <a name="deploy-to-android-device"></a>Distribuzione in un dispositivo Android

Accedere al dispositivo Android e connetterlo al computer con un cavo USB.

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) inserire un segno di spunta accanto alla scena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e deselezionare i segni di spunta per tutte le altre scene.

Assicurarsi che l'opzione **Export Project** (Esporta progetto) non sia selezionata. Selezionare **Build And Run** (Compila ed esegui). Verrà richiesto di salvare il file `.apk`. È possibile scegliere qualsiasi nome.

Seguire le istruzioni nell'app. È possibile selezionare **Create & Share Anchor** (Crea e condividi ancoraggio) oppure **Locate Shared Anchor** (Individua ancoraggio condiviso). Il primo scenario consente di creare un ancoraggio che successivamente potrà essere individuato nello stesso dispositivo o in un dispositivo diverso. Il secondo scenario, se è già stata eseguita l'app, nello stesso dispositivo o in uno diverso, consente di individuare gli ancoraggi condivisi in precedenza. Dopo aver scelto lo scenario, l'app guiderà l'utente con ulteriori istruzioni sulle operazioni da eseguire. Ad esempio, verrà chiesto di spostarsi all'interno del dispositivo per raccogliere informazioni sull'ambiente. In un secondo momento si inserirà un ancoraggio, si attenderà il caricamento e così via.

### <a name="deploy-to-an-ios-device"></a>Distribuzione in un dispositivo iOS

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) inserire un segno di spunta accanto alla scena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e deselezionare i segni di spunta per tutte le altre scene.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Seguire le istruzioni nell'app. È possibile selezionare **Create & Share Anchor** (Crea e condividi ancoraggio) oppure **Locate Shared Anchor** (Individua ancoraggio condiviso). Il primo scenario consente di creare un ancoraggio che successivamente potrà essere individuato nello stesso dispositivo o in un dispositivo diverso. Il secondo scenario, se è già stata eseguita l'app, nello stesso dispositivo o in uno diverso, consente di individuare gli ancoraggi condivisi in precedenza. Dopo aver scelto lo scenario, l'app guiderà l'utente con ulteriori istruzioni sulle operazioni da eseguire. Ad esempio, verrà chiesto di spostarsi all'interno del dispositivo per raccogliere informazioni sull'ambiente. In un secondo momento si inserirà un ancoraggio, si attenderà il caricamento e così via.

In Xcode arrestare l'app selezionando **Stop** (Arresta).
