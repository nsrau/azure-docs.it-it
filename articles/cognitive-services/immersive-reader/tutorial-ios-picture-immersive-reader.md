---
title: "Esercitazione: Creare un'app iOS per l'avvio di una foto nello strumento di lettura immersiva (Swift)"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si creerà da zero un'app iOS cui si aggiungerà la funzionalità di avvio di immagini nello strumento di lettura immersiva.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: bdaee97c8c5d7e19076847c5f1f7c07c528c1747
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899383"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Esercitazione: Creare un'app iOS per l'avvio dello strumento di lettura immersiva con il contenuto di una foto (Swift)

Lo [strumento di lettura immersiva](https://www.onenote.com/learningtools) è uno strumento incluso che implementa tecniche comprovate per migliorare la comprensione della lettura.

L'[API di lettura del servizio Visione artificiale in Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) rileva il contenuto di testo in un'immagine tramite i modelli di riconoscimento Microsoft più recenti e converte il testo identificato in flusso di caratteri leggibili dal computer.

In questa esercitazione si creerà da zero un'app iOS in cui si integrerà l'API di lettura e lo strumento di lettura immersiva tramite Immersive Reader SDK. Un esempio pratico completo di questa esercitazione è disponibile [qui](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Una risorsa Strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory (Azure AD). Seguire [queste istruzioni](./azure-active-directory-authentication.md) per configurare l'ambiente. Per la configurazione delle proprietà del progetto di esempio, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* L'uso di questo esempio richiede una sottoscrizione di Azure per il servizio cognitivo Visione artificiale. [Creare una risorsa del servizio cognitivo Visione artificiale nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Creare un progetto Xcode

Creare un nuovo progetto in Xcode.

![Nuovo progetto](./media/ios/xcode-create-project.png)

Scegliere **App visualizzazione singola**.

![Nuova app a visualizzazione singola](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Ottenere il pod CocoaPods per l'SDK
Il modo più semplice per usare Immersive Reader SDK è tramite CocoaPods. Per eseguire l'installazione tramite CocoaPods:
1. [Installare CocoaPods](http://guides.cocoapods.org/using/getting-started.html): seguire la guida introduttiva per installare CocoaPods.
2. Creare un podfile eseguendo `pod init` nella directory radice del progetto Xcode.
3.  Aggiungere il pod CocoaPods al podfile aggiungendo `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Il podfile avrà un aspetto simile al seguente, con il nome della destinazione al posto di picture-to-immersive-reader-swift:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. Nel terminale eseguire il comando `pod install` all'interno della directory del progetto Xcode per installare il pod Immersive Reader SDK.
5. Aggiungere `import immersive_reader_sdk` a tutti i file che devono fare riferimento all'SDK.
6. Assicurarsi di aprire il progetto tramite il file `.xcworkspace` e non il file `.xcodeproj`.

## <a name="acquire-an-azure-ad-authentication-token"></a>Acquisire un token di autenticazione di Azure AD

Per questa parte sono necessari alcuni valori del passaggio relativo ai prerequisiti di configurazione dell'autenticazione di Azure AD. Fare riferimento al file di testo salvato della sessione.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Nella cartella principale del progetto, che contiene il file ViewController.swift, creare un file di classe Swift denominato Constants.swift. Sostituire la classe con il codice seguente, aggiungendo i valori, se applicabile. Mantenere questo file come file locale presente solo nel proprio computer e assicurarsi di non eseguirne il commit nel controllo del codice sorgente, in quanto contiene segreti che non devono essere resi pubblici. È consigliabile evitare di mantenere segreti nell'app. È invece consigliabile usare un servizio back-end per ottenere il token, in cui i segreti possono essere mantenuti esternamente all'app e al dispositivo. L'endpoint API back-end deve essere protetto tramite una qualche forma di autenticazione, ad esempio [OAuth](https://oauth.net/2/), per impedire a utenti non autorizzati di ottenere token da usare per il servizio e la fatturazione dello strumento di lettura immersiva. Tuttavia, questa parte esula dall'ambito di questa esercitazione.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Configurare l'app per l'esecuzione senza uno storyboard

Aprire AppDelegate.swift e sostituire il file con il codice seguente.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/AppDelegate.swift)]

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Aggiungere la funzionalità per l'acquisizione e il caricamento di foto

Rinominare ViewController.swift in PictureLaunchViewController.swift e sostituire il file con il codice seguente.

[!code-swift[PictureLaunchViewController](~/ImmersiveReaderSdk/iOS/samples/picture-to-immersive-reader-swift/picture-to-immersive-reader-swift/PictureLaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Impostare lo schema di archiviazione in Xcode selezionando come destinazione un simulatore o un dispositivo.
![Schema di archiviazione](./media/ios/xcode-archive-scheme.png)<br/>
![Selezionare la destinazione](./media/ios/xcode-select-target.png)

In Xcode premere CTRL+R oppure fare clic sul pulsante di riproduzione per eseguire il progetto e avviare l'app sul simulatore o sul dispositivo specificato.

Nell'app verrà visualizzato il contenuto seguente:

![App di esempio](./media/ios/picture-to-immersive-reader-ipad-app.png)

All'interno dell'app scattare o caricare una foto di testo premendo il pulsante "Scatta foto" o "Scegli foto dalla raccolta" perché lo strumento di lettura immersiva avvii la visualizzazione del testo dalla foto.

![Strumento di lettura immersiva](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader iOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) e le [informazioni di riferimento su Immersive Reader iOS SDK](./ios-reference.md)
