---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, Objective-C - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Objective-C in macOS con Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: be5f62a75d9a3998d843e9865768b46658b8386c
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815022"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale in Objective-C in macOS con Speech SDK

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

Questo articolo illustra come creare un'app macOS in Objective-C usando Speech SDK di Servizi cognitivi di Azure per trascrivere in testo la voce registrata da un microfono.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario soddisfare questi requisiti:

* Una [chiave di sottoscrizione](~/articles/cognitive-services/Speech-Service/get-started.md) per il servizio Voce.
* Un computer macOS con [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) o versione successiva e macOS 10.13 o versione successiva.

## <a name="get-the-speech-sdk-for-macos"></a>Ottenere Speech SDK per macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Speech SDK di Servizi cognitivi per Mac viene distribuito come bundle framework. Può essere usato in progetti Xcode come [CocoaPod](https://cocoapods.org/) oppure scaricato da https://aka.ms/csspeech/macosbinary e collegato manualmente. Questo articolo usa CocoaPod.

## <a name="create-an-xcode-project"></a>Creare un progetto Xcode

Avviare Xcode e avviare un nuovo progetto selezionando **File** > **Nuovo** > **Progetto**. Nella finestra di dialogo di selezione del modello selezionare **App Cocoa**.

Nelle finestre di dialogo che seguono, effettuare le selezioni seguenti.

1. Nella finestra di dialogo **Opzioni progetto**:
    1. Immettere un nome per l'app di avvio rapido, ad esempio *helloworld*.
    1. Immettere un nome di organizzazione appropriato e l'identificatore dell'organizzazione, se si ha già un account per sviluppatore Apple. Ai fini del test, usare un nome come *testorg*. Per firmare l'app, è necessario un profilo di provisioning corretto. Per altre informazioni, vedere il [sito per sviluppatori Apple](https://developer.apple.com/).
    1. Assicurarsi che per il progetto sia selezionato il linguaggio **Objective-C**.
    1. Deselezionare le caselle di controllo per usare storyboard e per creare un'applicazione basata su documento. L'interfaccia utente semplice per l'app di esempio verrà creata a livello di codice.
    1. Deselezionare tutte le caselle di controllo per i test e il framework Core Data.

    ![Impostazioni del progetto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Selezionare la directory di progetto:
    1. Scegliere una directory in cui inserire il progetto. Con questo passaggio viene creata una directory helloworld nella directory home che contiene tutti i file del progetto Xcode.
    1. Disabilitare la creazione di un repository Git per il progetto di esempio.
1. Impostare gli entitlement per l'accesso alla rete e al microfono. Selezionare il nome dell'app nella prima riga della panoramica a sinistra per passare alla configurazione dell'app. Quindi selezionare la scheda **Funzionalità**.
    1. Abilitare l'impostazione **Sandbox app** per l'app.
    1. Selezionare le caselle di controllo per l'accesso a **Connessioni in uscita** e **Microfono**.

    ![Impostazioni della sandbox](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. L'app deve anche dichiarare l'uso del microfono nel file `Info.plist`. Selezionare il file nella panoramica e aggiungere la chiave **Privacy - Descrizione utilizzo Microfono**  con un valore analogo a *Microfono necessario per il riconoscimento vocale*.

    ![Impostazioni in Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Chiudere il progetto Xcode. Ne verrà usata un'istanza diversa in seguito dopo l'impostazione di CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installare l'SDK come CocoaPod

1. Installare il gestore di dipendenze di CocoaPod come descritto in queste [istruzioni per l'installazione](https://guides.cocoapods.org/using/getting-started.html).
1. Passare alla directory dell'app di esempio, helloworld. Inserire nella directory un file di testo denominato *Podfile* e il contenuto seguente:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Passare alla directory helloworld in un terminale ed eseguire il comando `pod install`. Verrà generata un'area di lavoro Xcode `helloworld.xcworkspace` contenente l'app di esempio e Speech SDK come dipendenza. Quest'area di lavoro verrà usata nei passaggi seguenti.

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Aprire l'area di lavoro `helloworld.xcworkspace` in Xcode.
1. Sostituire il contenuto del file `AppDelegate.m` generato automaticamente con il codice seguente:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.
1. Sostituire la stringa `YourServiceRegion` con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Rendere visibile l'output di debug selezionando **View** > **Debug area** > **Activate Console** (Visualizza > Area debug > Attiva console).
1. Compilare ed eseguire il codice di esempio scegliendo **Prodotto** > **Esegui** dal menu. È anche possibile selezionare **Riproduci**.
1. Dopo aver selezionato il pulsante e aver pronunciato alcune parole, verrà visualizzata la trascrizione di testo del parlato nella parte inferiore dello schermo. La prima volta che si esegue l'app, verrà chiesto di fornire all'app l'accesso al microfono del computer.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di Objective-C su GitHub](https://aka.ms/csspeech/samples)
