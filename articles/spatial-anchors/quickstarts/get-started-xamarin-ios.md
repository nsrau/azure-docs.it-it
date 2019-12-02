---
title: "Guida introduttiva: Creare un'app Xamarin in iOS"
description: In questo argomento di avvio rapido verrà illustrato come creare un'app iOS con Xamarin usando Ancoraggi nello spazio.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: d2e1612804c78e3436b776cdffea7e19bc2925fe
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276995"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app iOS in Xamarin con Ancoraggi nello spazio di Azure

Questo argomento di avvio rapido illustra come creare un'app iOS con Xamarin usando [Ancoraggi nello spazio di Azure](../overview.md). Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine, si avrà un'app iOS in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Distribuire ed eseguire l'app in un dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:
- Un computer Mac che esegue macOS High Sierra (10.13) o versione successiva con:
  - Le versioni più recenti di Xcode e dell'SDK iOS installate dall'[App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - Una versione aggiornata di <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio per Mac 8.1+</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git per macOS</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Aprire `Xamarin/SampleXamarin.sln` in Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Il passaggio successivo consiste nel configurare l'app in modo da usare l'identificatore e la chiave dell'account. Questi dati sono stati copiati in un editor di testo durante la [configurazione della risorsa Ancoraggi nello spazio](#create-a-spatial-anchors-resource).

Aprire `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Individuare il campo `SpatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `SpatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuire l'app nel dispositivo iOS

Accendere il dispositivo iOS, eseguire l'accesso e connetterlo al computer con un cavo USB.

Impostare il progetto di avvio su **SampleXamarin.iOS**, modificare il valore di **Configurazione soluzione** in **Rilascio** e selezionare il dispositivo di destinazione della distribuzione nell'elenco a discesa di selezione del dispositivo.

![Configurazione di Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Selezionare **Esegui** > **Avvia senza eseguire debug** per distribuire e avviare l'app.

Nell'app selezionare **Di base** per eseguire la demo e seguire le istruzioni per inserire e richiamare un ancoraggio.

> ![Screenshot 1](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![Screenshot 2](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![Screenshot 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)
