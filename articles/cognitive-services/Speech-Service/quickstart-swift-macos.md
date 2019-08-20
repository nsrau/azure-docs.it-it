---
title: 'Guida introduttiva: Riconoscimento vocale, Swift - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Swift in macOS con Speech SDK
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: c1407e8a133013054ef33d4fe55fb8ecb46de654
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035696"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale in Swift in macOS con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un'applicazione macOS in Swift usando Speech SDK di Servizi cognitivi per trascrivere la voce registrata da un microfono in testo.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, ecco un elenco di prerequisiti:

* Una [chiave di sottoscrizione](get-started.md) per il servizio Voce.
* Un computer macOS con [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) o versione successiva e [CocoaPods](https://cocoapods.org/) installato.

## <a name="get-the-speech-sdk-for-macos"></a>Ottenere Speech SDK per macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `1.6.0`. Questa esercitazione non funzionerà per versioni precedenti dell'SDK se non apportando le opportune modifiche.

Speech SDK di Servizi cognitivi per macOS viene distribuito come bundle framework.
Può essere usato in progetti Xcode come [CocoaPod](https://cocoapods.org/) oppure scaricato da https://aka.ms/csspeech/macosbinary e collegato manualmente. Questa guida usa CocoaPod.

## <a name="create-an-xcode-project"></a>Creare un progetto Xcode

Avviare Xcode e avviare un nuovo progetto facendo clic su **File** > **Nuovo** > **Progetto**.
Nella finestra di dialogo di selezione del modello scegliere il modello "App Cocoa".

Nelle finestre di dialogo che seguono, effettuare le selezioni seguenti:

1. Finestra di dialogo Opzioni di progetto
    1. Immettere un nome per l'app di Guida introduttiva, ad esempio `helloworld`.
    1. Immettere un nome di organizzazione appropriato e l'identificatore dell'organizzazione, se si ha già un account per sviluppatore Apple. A scopo di test è possibile scegliere qualsiasi nome, ad esempio `testorg`. Per firmare l'app, è necessario un profilo di provisioning corretto. Per dettagli, visitare il [sito Apple Developer](https://developer.apple.com/).
    1. Assicurarsi che Swift venga scelto come lingua per il progetto.
    1. Disabilitare le caselle di controllo per usare storyboard e per creare un'applicazione basata su documento. L'interfaccia utente semplice per l'app di esempio verrà creata a livello di codice.
    1. Disabilitare tutte le caselle di spunta per i test e i dati principali.
1. Selezionare la directory di progetto
    1. Scegliere una directory in cui inserire il progetto. Verrà creata una directory `helloworld` nella directory scelta, contenente tutti i file del progetto Xcode.
    1. Disabilitare la creazione di un repository Git per il progetto di esempio.
1. Impostare gli entitlement per l'accesso alla rete e al microfono. Fare clic sul nome dell'app nella prima riga della panoramica a sinistra per passare alla configurazione dell'app e quindi scegliere la scheda "Funzionalità".
    1. Abilitare l'impostazione "Sandbox app" per l'app.
    1. Abilitare le caselle di controllo per l'accesso a "Connessioni in uscita" e "Microfono".
    ![Impostazioni della sandbox](media/sdk/qs-swift-macos-sandbox.png)
1. L'app deve anche dichiarare l'uso del microfono nel file `Info.plist`. Fare clic sul file nella panoramica e aggiungere la chiave "Privacy - Descrizione utilizzo Microfono " con un valore analogo a "Microfono necessario per il riconoscimento vocale".
    ![Impostazioni in Info.plist](media/sdk/qs-swift-macos-info-plist.png)
1. Chiudere il progetto Xcode. Ne verrà usata un'istanza diversa in seguito dopo l'impostazione di CocoaPods.

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Inserire un nuovo file di intestazione con il nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` nella directory `helloworld` all'interno del progetto helloworld e incollare il codice seguente: [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Aggiungere il percorso relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` all'intestazione provvisoria nel campo *Objective-C Bridging Header* (Intestazione provvisoria Objective-C) delle impostazioni del progetto Swift relative alla destinazione helloworld ![Proprietà dell'intestazione](media/sdk/qs-swift-macos-bridging-header.png)
1. Sostituire il contenuto del file `AppDelegate.swift` generato automaticamente con: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/AppDelegate.swift#code)]
1. In `AppDelegate.swift` sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.
1. Sostituire la stringa `YourServiceRegion` con l'area associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione della versione di valutazione gratuita.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installare l'SDK come CocoaPod

1. Installare il gestore di dipendenze di CocoaPod come descritto in queste [istruzioni per l'installazione](https://guides.cocoapods.org/using/getting-started.html).
1. Passare alla directory dell'app di esempio (`helloworld`). Inserire nella directory un file di testo denominato `Podfile` e il contenuto seguente: [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/Podfile)]
1. Passare alla directory `helloworld` in un terminale ed eseguire il comando `pod install`. Verrà generata un'area di lavoro Xcode `helloworld.xcworkspace` contenente l'app di esempio e Speech SDK come dipendenza. Quest'area di lavoro verrà usata di seguito.

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Aprire l'area di lavoro `helloworld.xcworkspace` in Xcode.
1. Rendere visibile l'output di debug (**Visualizza** > **Area di debug** > **Attivare Console**).
1. Compilare ed eseguire il codice di esempio scegliendo **Prodotto** > **Esegui** dal menu o facendo clic sul pulsante **Riproduci**.
1. Dopo avere fatto clic sul pulsante "Riconosci" nell'app e aver pronunciato alcune parole, verrà visualizzato il testo pronunciato nella parte inferiore della finestra dell'app.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)

