---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903967"
---
## <a name="set-up-your-device-in-unity"></a>Configurare un dispositivo in Unity

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

Dopo l'avvio dell'app, nella finestra di dialogo **Choose A Demo** (Scegli demo) usare le frecce sinistra o destra per selezionare l'opzione **LocalShare** e quindi toccare **Go!** (Vai). Seguire le istruzioni nell'app. È possibile selezionare **Create & Share Anchor** (Crea e condividi ancoraggio) oppure **Locate Shared Anchor** (Individua ancoraggio condiviso).

Il primo scenario consente di creare un ancoraggio che successivamente potrà essere individuato nello stesso dispositivo o in un dispositivo diverso.
Il secondo scenario, se è già stata eseguita l'app, nello stesso dispositivo o in uno diverso, consente di individuare gli ancoraggi condivisi in precedenza. Dopo la scelta dello scenario, l'app guiderà l'utente con ulteriori istruzioni sulle operazioni da eseguire. Ad esempio, verrà chiesto di muovere il dispositivo per raccogliere informazioni sull'ambiente. In un secondo momento si inserirà un ancoraggio, si attenderà il caricamento e così via.

### <a name="deploy-to-an-ios-device"></a>Distribuzione in un dispositivo iOS

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** > **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) assicurarsi che tutte le scene abbiano un segno di spunta.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Dopo l'avvio dell'app, nella finestra di dialogo **Choose A Demo** (Scegli demo) usare le frecce sinistra o destra per selezionare l'opzione **LocalShare** e quindi toccare **Go!** (Vai). Seguire le istruzioni nell'app. È possibile selezionare **Create & Share Anchor** (Crea e condividi ancoraggio) oppure **Locate Shared Anchor** (Individua ancoraggio condiviso).

Il primo scenario consente di creare un ancoraggio che successivamente potrà essere individuato nello stesso dispositivo o in un dispositivo diverso.
Il secondo scenario, se è già stata eseguita l'app, nello stesso dispositivo o in uno diverso, consente di individuare gli ancoraggi condivisi in precedenza. Dopo la scelta dello scenario, l'app guiderà l'utente con ulteriori istruzioni sulle operazioni da eseguire. Ad esempio, verrà chiesto di muovere il dispositivo per raccogliere informazioni sull'ambiente. In un secondo momento si inserirà un ancoraggio, si attenderà il caricamento e così via.

In Xcode arrestare l'app selezionando **Stop** (Arresta).
