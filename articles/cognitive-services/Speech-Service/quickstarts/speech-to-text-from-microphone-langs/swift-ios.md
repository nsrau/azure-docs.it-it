---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, Swift - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Swift in iOS con Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 661dd94caa2a1998b26ec1270688c0729011c15a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505700"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale in Swift in iOS con Speech SDK

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

Questo articolo illustra come creare un'applicazione iOS in Swift con Speech SDK di Servizi cognitivi di Azure per trascrivere in testo la voce registrata da un microfono.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario soddisfare questi requisiti:

* Una [chiave di sottoscrizione](~/articles/cognitive-services/Speech-Service/get-started.md) per il servizio Voce.
* Un computer macOS con [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) o versione successiva e [CocoaPods](https://cocoapods.org/) installato.

## <a name="get-the-speech-sdk-for-ios"></a>Ottenere Speech SDK per iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Questa esercitazione non funzionerà con versioni dell'SDK precedenti alla 1.6.0.

Speech SDK di Servizi cognitivi per iOS viene distribuito come bundle framework. Può essere usato in progetti Xcode come [CocoaPod](https://cocoapods.org/) oppure scaricato da https://aka.ms/csspeech/macosbinary e collegato manualmente. Questo articolo usa CocoaPod.

## <a name="create-an-xcode-project"></a>Creare un progetto Xcode

Avviare Xcode e avviare un nuovo progetto selezionando **File** > **Nuovo** > **Progetto**.
Nella finestra di dialogo di selezione del modello scegliere **iOS Single View App**.

Nelle finestre di dialogo che seguono, effettuare le selezioni seguenti.

1. Nella finestra di dialogo **Opzioni progetto**:
    1. Immettere un nome per l'app di avvio rapido, ad esempio *helloworld*.
    1. Immettere un nome di organizzazione appropriato e l'identificatore dell'organizzazione, se si ha già un account per sviluppatore Apple. Ai fini del test, usare un nome come *testorg*. Per firmare l'app, è necessario un profilo di provisioning corretto. Per altre informazioni, vedere il [sito per sviluppatori Apple](https://developer.apple.com/).
    1. Assicurarsi che **Swift** venga scelto come linguaggio per il progetto.
    1. Disabilitare le caselle di controllo per usare storyboard e per creare un'applicazione basata su documento. L'interfaccia utente semplice per l'app di esempio verrà creata a livello di codice.
    1. Deselezionare tutte le caselle di controllo per i test e il framework Core Data.
1. Selezionare la directory di progetto:
    1. Scegliere una directory in cui inserire il progetto. Con questo passaggio viene creata una directory helloworld nella directory scelta che contiene tutti i file del progetto Xcode.
    1. Disabilitare la creazione di un repository Git per il progetto di esempio.
1. L'app deve anche dichiarare l'uso del microfono nel file `Info.plist`. Selezionare il file nella panoramica e aggiungere la chiave **Privacy - Descrizione utilizzo Microfono**  con un valore analogo a *Microfono necessario per il riconoscimento vocale*.

    ![Impostazioni in Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Chiudere il progetto Xcode. Ne verrà usata un'istanza diversa in seguito dopo l'impostazione di CocoaPods.

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Inserire un nuovo file di intestazione con il nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` nella directory helloworld all'interno del progetto helloworld. Copiare nel file il codice seguente:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Aggiungere il percorso relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` all'intestazione provvisoria nel campo **Objective-C Bridging Header** (Intestazione provvisoria Objective-C) delle impostazioni del progetto Swift relative alla destinazione helloworld.

   ![Proprietà dell'intestazione](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Sostituire il contenuto del file `AppDelegate.swift` generato automaticamente con il codice seguente:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Sostituire il contenuto del file `ViewController.swift` generato automaticamente con il codice seguente:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. In `ViewController.swift` sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.
1. Sostituire la stringa `YourServiceRegion` con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installare l'SDK come CocoaPod

1. Installare il gestore di dipendenze di CocoaPod come descritto in queste [istruzioni per l'installazione](https://guides.cocoapods.org/using/getting-started.html).
1. Passare alla directory dell'app di esempio, helloworld. Inserire nella directory un file di testo denominato *Podfile* e il contenuto seguente:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Passare alla directory helloworld in un terminale ed eseguire il comando `pod install`. Verrà generata un'area di lavoro Xcode `helloworld.xcworkspace` contenente l'app di esempio e Speech SDK come dipendenza. Quest'area di lavoro verrà usata nei passaggi seguenti.

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Aprire l'area di lavoro `helloworld.xcworkspace` in Xcode.
1. Rendere visibile l'output di debug selezionando **View** > **Debug area** > **Activate Console** (Visualizza > Area debug > Attiva console).
1. Scegliere il simulatore iOS o un dispositivo iOS connesso al computer di sviluppo come destinazione per l'app dall'elenco nel menu **Prodotto** > **Destinazione**.
1. Compilare ed eseguire l'esempio di codice nel simulatore iOS scegliendo **Prodotto** > **Esegui** dal menu. È anche possibile selezionare il pulsante **Riproduci**.
1. Dopo avere selezionato il pulsante **Recognize** (Riconosci) nell'app e aver pronunciato alcune parole, verrà visualizzata la trascrizione di testo del parlato nella parte inferiore dello schermo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)
