---
title: "Guida introduttiva: Creare un'app iOS con Unity"
description: In questa guida introduttiva verrà illustrato come creare un'app iOS con Unity usando Ancoraggi nello spazio.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b278ac6c824b1583e90cfc9152264f61357dd228
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95971409"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Guida introduttiva: Creare un'app iOS in Unity con gli ancoraggi nello spazio di Azure

Questo argomento di avvio rapido illustra come creare un'app iOS in Unity usando [Ancoraggi nello spazio di Azure](../overview.md). Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine si avrà un'app ARKit per iOS compilata con Unity in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Preparare le impostazioni di compilazione di Unity
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Esportare il progetto Xcode
> * Distribuire ed eseguire l'app in un dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

- Un computer macOS con <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a>, l'ultima versione di <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> installata.
- Git installato tramite HomeBrew. Immettere il comando seguente in una singola riga del terminale: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Eseguire quindi `brew install git` e `brew install git-lfs`.
- Un dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatibile con ARKit</a> abilitato per lo sviluppo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Scaricare e aprire il progetto di esempio di Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-xcode-project"></a>Esportare il progetto Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Nell'app selezionare **BasicDemo** usando i tasti di direzione, quindi fare clic sul pulsante **Vai** per eseguire la demo. Seguire le istruzioni posizionare e richiamare un ancoraggio.

![Screenshot 1](./media/get-started-unity-ios/screenshot-1.jpg)
![Screenshot 2](./media/get-started-unity-ios/screenshot-2.jpg)
![Screenshot 3](./media/get-started-unity-ios/screenshot-3.jpg)

Al termine, arrestare l'app premendo **Stop** (Arresta) in Xcode.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="rendering-issues"></a>Problemi di rendering

Se durante l'esecuzione dell'app non si vede la videocamera sullo sfondo, ma si vede invece, ad esempio, uno sfondo vuoto, blu o un'altra trama, è probabilmente necessario reimportare gli asset in Unity. Arrestare l'app. Nel menu in alto in Unity scegliere **Assets -> Reimport all** (Asset -> Reimporta tutto). Quindi eseguire di nuovo l'app.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Procedura: Configurare Ancoraggi nello spazio di Azure in un progetto Unity](../how-tos/setup-unity-project.md)
