---
title: 'Avvio rapido: Riconoscere i contenuti vocali, Objective-C - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Objective-C in macOS con Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: 49e39a40093f2efdf0fabbbf9f383576b2cebe22
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485001"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale in Objective-C in macOS con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un'applicazione macOS in Objective-C usando Speech SDK di Servizi cognitivi per trascrivere la voce registrata da un microfono in testo.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, ecco un elenco di prerequisiti:

* Una [chiave di sottoscrizione](get-started.md) per il servizio di riconoscimento vocale
* Un computer macOS con [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) o versione successiva e macOS 10.13 o versione successiva

## <a name="get-the-speech-sdk-for-macos"></a>Ottenere Speech SDK per macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `1.6.0`.

Speech SDK di Servizi cognitivi per Mac viene distribuito come bundle framework.
Può essere usato in progetti Xcode come [CocoaPod](https://cocoapods.org/) oppure scaricato da https://aka.ms/csspeech/macosbinary e collegato manualmente. Questa guida usa CocoaPod.

## <a name="create-an-xcode-project"></a>Creare un progetto Xcode

Avviare Xcode e avviare un nuovo progetto facendo clic su **File** > **Nuovo** > **Progetto**.
Nella finestra di dialogo di selezione del modello scegliere il modello "App Cocoa".

Nelle finestre di dialogo che seguono, effettuare le selezioni seguenti:

1. Finestra di dialogo Opzioni di progetto
    1. Immettere un nome per l'app di Guida introduttiva, ad esempio `helloworld`.
    1. Immettere un nome di organizzazione appropriato e l'identificatore dell'organizzazione, se si ha già un account per sviluppatore Apple. A scopo di test è possibile scegliere qualsiasi nome, ad esempio `testorg`. Per firmare l'app, è necessario un profilo di provisioning corretto. Per dettagli, visitare il [sito Apple Developer](https://developer.apple.com/).
    1. Assicurarsi che Objective-C venga scelto come lingua per il progetto.
    1. Disabilitare le caselle di controllo per usare storyboard e per creare un'applicazione basata su documento. L'interfaccia utente semplice per l'app di esempio verrà creata a livello di codice.
    1. Disabilitare tutte le caselle di spunta per i test e i dati principali.
    ![Impostazioni progetto](media/sdk/qs-objectivec-macos-project-settings.png)
1. Selezionare la directory di progetto
    1. Scegliere una directory in cui inserire il progetto. Verrà creata una directory `helloworld` nella home directory che contiene tutti i file del progetto Xcode.
    1. Disabilitare la creazione di un repository Git per il progetto di esempio.
1. Impostare gli entitlement per l'accesso alla rete e al microfono. Fare clic sul nome dell'app nella prima riga della panoramica a sinistra per passare alla configurazione dell'app e quindi scegliere la scheda "Funzionalità".
    1. Abilitare l'impostazione "Sandbox app" per l'app.
    1. Abilitare le caselle di controllo per l'accesso a "Connessioni in uscita" e "Microfono".
    ![Impostazioni della sandbox](media/sdk/qs-objectivec-macos-sandbox.png)
1. L'app deve anche dichiarare l'uso del microfono nel file `Info.plist`. Fare clic sul file nella panoramica e aggiungere la chiave "Privacy - Descrizione utilizzo Microfono " con un valore analogo a "Microfono necessario per il riconoscimento vocale".
    ![Impostazioni in Info.plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Chiudere il progetto Xcode. Ne verrà usata un'istanza diversa in seguito dopo l'impostazione di CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installare l'SDK come CocoaPod

1. Installare il gestore di dipendenze di CocoaPod come descritto in queste [istruzioni per l'installazione](https://guides.cocoapods.org/using/getting-started.html).
1. Passare alla directory dell'app di esempio (`helloworld`). Inserire nella directory un file di testo denominato `Podfile` e il contenuto seguente: [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Passare alla directory `helloworld` in un terminale ed eseguire il comando `pod install`. Verrà generata un'area di lavoro Xcode `helloworld.xcworkspace` contenente l'app di esempio e Speech SDK come dipendenza. Quest'area di lavoro verrà usata di seguito.

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Aprire l'area di lavoro `helloworld.xcworkspace` in Xcode.
1. Sostituire il contenuto del file generato automaticamente `AppDelegate.m`:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.
1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Rendere visibile l'output di debug (**Visualizza** > **Area di debug** > **Attivare Console**).
1. Compilare ed eseguire il codice di esempio scegliendo **Prodotto** > **Esegui** dal menu o facendo clic sul pulsante **Riproduci**.
1. Dopo avere fatto clic sul pulsante e aver pronunciato alcune parole, verrà visualizzato il testo pronunciato nella parte inferiore dello schermo. La prima volta che si esegue l'app, verrà chiesto di fornire all'app l'accesso al microfono del computer.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di Objective-C su GitHub](https://aka.ms/csspeech/samples)

