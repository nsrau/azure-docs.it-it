---
title: "Guida introduttiva: Creare un'app iOS"
description: Informazioni su come creare un'app iOS usando Ancoraggi nello spazio di Azure a livello di codice in Swift o Objective-C.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 4434118313989eb8bc70f1d44e977b2df3633050
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "96009076"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Guida introduttiva: Creare un'app iOS con Ancoraggi nello spazio di Azure, in Swift o Objective-C

Questa guida introduttiva illustra come creare un'app iOS usando [Ancoraggi nello spazio di Azure](../overview.md) in Swift o Objective-C. Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine, si avrà un'app iOS ARKit in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Distribuire ed eseguire l'app in un dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

- Un computer macOS abilitato per lo sviluppo in cui è installata la versione più recente di <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> e <a href="https://cocoapods.org" target="_blank">CocoaPods</a>.
- Git installato tramite HomeBrew:
  1. Immettere il comando seguente in una singola riga del terminale: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. 
  1. Eseguire `brew install git` e `brew install git-lfs`.
  1. Aggiornare la configurazione di Git con `git lfs install` (per l'utente corrente) o con `git lfs install --system` (per l'intero sistema).
- Un dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatibile con ARKit</a> abilitato per lo sviluppo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

Usare il terminale per le azioni seguenti.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Installare i pod necessari usando CocoaPods:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Accedere a `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Accedere a `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Eseguire `pod install --repo-update` per installare CocoaPods per il progetto.

Aprire quindi `.xcworkspace` in Xcode.

> [!NOTE]
> Vedere i passaggi relativi alla risoluzione dei problemi [qui](#cocoapods-issues-on-macos-catalina-1015) se si verificano problemi di CocoaPod dopo l'aggiornamento a macOS Catalina (10.15).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Il passaggio successivo consiste nel configurare l'app in modo da usare l'identificatore e la chiave dell'account. Questi dati sono stati copiati in un editor di testo durante la [configurazione della risorsa Ancoraggi nello spazio](#create-a-spatial-anchors-resource).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Aprire `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Individuare il campo `spatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `spatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

Individuare il campo `spatialAnchorsAccountDomain` e sostituire `Set me` con il dominio dell'account.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Aprire `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Individuare il campo `SpatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `SpatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

Individuare il campo `SpatialAnchorsAccountDomain` e sostituire `Set me` con il dominio dell'account.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuire l'app nel dispositivo iOS

Connettere il dispositivo iOS al Mac e impostare lo **schema attivo** sul dispositivo iOS.

![Selezionare il dispositivo](./media/get-started-ios/select-device.png)

Selezionare **Build and then run the current scheme** (Compila e quindi esegui lo schema corrente).

![Distribuire ed eseguire](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Se viene visualizzato un errore `library not found for -lPods-SampleObjC`, è probabile che sia stato aperto il file `.xcodeproj` invece di `.xcworkspace`. Aprire `.xcworkspace` e riprovare.

In Xcode arrestare l'app premendo **Stop** (Arresta).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemi di CocoaPods in macOS Catalina (10.15)

Se di recente è stato eseguito l'aggiornamento a macOS Catalina (10.15) e CocoaPods è stato installato in precedenza, CocoaPods potrebbe interrompersi e non essere in grado di configurare correttamente i pod e i file di progetto `.xcworkspace`. Per risolvere questo problema, è necessario reinstallare CocoaPods eseguendo i comandi seguenti:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Arresti anomali dell'app durante la distribuzione in iOS 10.3.1 da un profilo di provisioning personale/account per sviluppatore 

Se si distribuisce l'app iOS in iOS 10.3.1 da un profilo di provisioning personale/account per sviluppatore, è possibile che venga visualizzato questo errore: `Library not loaded: @rpath/ADAL...`. 

Per risolvere il problema:

- Usare un profilo di provisioning che non sia un profilo del team personale (account per sviluppatore a pagamento).
- Distribuire l'app in un dispositivo iOS che esegue iOS 13.3 o versione precedente oppure in un dispositivo che esegue iOS 13.4 versione beta o finale.
- Per altre informazioni su questo problema, vedere [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)
