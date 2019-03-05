---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753475"
---
## <a name="open-the-sample-project-in-unity"></a>Aprire il progetto di esempio in Unity

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Per eseguire la configurazione per un dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Per eseguire la configurazione per un dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Nel riquadro **Progetto** passare a `Assets/AzureSpatialAnchorsPlugin/Examples` e aprire il file della scena `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Nel riquadro **Inspector** (Controllo) immettere il valore di `Sharing Anchors Service url` (relativo alla distribuzione di Azure dell'app Web ASP.NET) come valore per `Base Sharing Url`, sostituendo `index.html` con `api/anchors`. L'aspetto dovrebbe essere simile al seguente: `https://<app_name>.azurewebsites.net/api/anchors`.

Salvare la scena selezionando **File** -> **Salva**.

## <a name="to-deploy-to-an-android-device"></a>Per eseguire la distribuzione in un dispositivo Android

Accendere il dispositivo Android, eseguire l'accesso e connetterlo al PC con un cavo USB.

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** -> **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) inserire un segno di spunta accanto alla scena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e deselezionare i segni di spunta per tutte le altre scene.

Verificare che non sia presente un segno di spunta accanto alla casella **Export Project** (Esporta progetto). Fare clic su **Build And Run** (Compila ed esegui). Verrà chiesto di salvare il file `.apk`, per il quale è possibile scegliere qualsiasi nome.

Seguire le istruzioni nell'app. È possibile scegliere tra **Create & Share Anchor** (Crea e condividi ancoraggio) o **Locate Shared Anchor** (Individua ancoraggio condiviso). La prima opzione consente di creare un ancoraggio che successivamente potrà essere individuato nello stesso dispositivo o in un dispositivo diverso. La seconda opzione, se in precedenza è stata eseguita l'app, nello stesso dispositivo o in uno diverso, consente di individuare gli ancoraggi condivisi in precedenza.

## <a name="to-deploy-to-an-ios-device"></a>Per eseguire la distribuzione in un dispositivo iOS

Aprire **Build Settings** (Impostazioni compilazione) selezionando **File** -> **Build Settings** (File -> Impostazioni compilazione).

In **Scenes In Build** (Scene nella compilazione) inserire un segno di spunta accanto alla scena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e deselezionare i segni di spunta per tutte le altre scene.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Seguire le istruzioni nell'app. È possibile scegliere tra **Create & Share Anchor** (Crea e condividi ancoraggio) o **Locate Shared Anchor** (Individua ancoraggio condiviso). La prima opzione consente di creare un ancoraggio che successivamente potrà essere individuato nello stesso dispositivo o in un dispositivo diverso. La seconda opzione, se in precedenza è stata eseguita l'app, nello stesso dispositivo o in uno diverso, consente di individuare gli ancoraggi condivisi in precedenza.

In Xcode arrestare l'app premendo **Stop**.
