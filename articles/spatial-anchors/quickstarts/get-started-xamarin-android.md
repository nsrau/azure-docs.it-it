---
title: Avvio rapido - Creare un'app Android in Xamarin con Ancoraggi nello spazio di Azure | Microsoft Docs
description: Questo argomento di avvio rapido illustra come creare un'app Android con Xamarin usando Ancoraggi nello spazio.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0958961f1e66be130dd2be816f6002dd34465dc6
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931422"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app Android in Xamarin con Ancoraggi nello spazio di Azure

Questo argomento di avvio rapido illustra come creare un'app Android con Xamarin usando [Ancoraggi nello spazio di Azure](../overview.md). Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine, si avrà un'app Android in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Distribuire ed eseguire l'app in un dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:
- Un computer Windows o macOS:
  - Se si usa Windows:
    - Una versione aggiornata di <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2+</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git per Windows</a>
  - Se si usa macOS:
    - Una versione aggiornata di <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio per Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git per macOS</a>
- La versione più recente di Xamarin.Android installata e in esecuzione nella piattaforma preferita. Per una guida all'installazione di Xamarin.Android, vedere le guide di [installazione di Xamarin.Android](https://docs.microsoft.com/xamarin/android/get-started/installation/index).
- Un dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">abilitato per lo sviluppo</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">idoneo per ARCore</a>.
  - Potrebbero essere necessari driver di dispositivo aggiuntivi per la comunicazione del computer con il dispositivo Android. Per altre informazioni, vedere [qui](https://developer.android.com/studio/run/device.html).
- L'app deve avere come destinazione ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Aprire `Xamarin/SampleXamarin.sln` in Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Il passaggio successivo consiste nel configurare l'app in modo da usare l'identificatore e la chiave dell'account. Questi dati sono stati copiati in un editor di testo durante la [configurazione della risorsa Ancoraggi nello spazio](#create-a-spatial-anchors-resource).

Aprire `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Individuare il campo `SpatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `SpatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

## <a name="deploy-the-app-to-your-android-device"></a>Distribuire l'app nel dispositivo Android

Accendere il dispositivo Android, eseguire l'accesso e connetterlo al computer con un cavo USB.

Impostare il progetto di avvio su **SampleXamarin.Android**, modificare il valore di **Configurazione soluzione** in **Rilascio** e selezionare il dispositivo di destinazione della distribuzione nell'elenco a discesa di selezione del dispositivo.

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Configurazione di Visual Studio](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Selezionare **Debug** > **Avvia debug** per distribuire e avviare l'app.

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Configurazione di Visual Studio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Selezionare **Esegui** > **Avvia senza eseguire debug** per distribuire e avviare l'app.

---

Nell'app selezionare **Di base** per eseguire la demo e seguire le istruzioni per inserire e richiamare un ancoraggio.

> ![Screenshot 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![Screenshot 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![Screenshot 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)
