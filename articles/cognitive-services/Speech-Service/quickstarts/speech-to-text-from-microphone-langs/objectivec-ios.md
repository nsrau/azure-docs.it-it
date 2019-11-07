---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, Objective-C - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Objective-C in iOS con Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0d353b115acbf48bfffa6c2033b507721de501c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505692"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale in Objective-C in iOS con Speech SDK

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

Questo articolo illustra come creare un'app iOS in Objective-C usando Speech SDK di Servizi cognitivi di Azure per trascrivere il testo convertito dalla voce di un microfono o da un file con audio registrato.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario soddisfare questi requisiti:

* Una [chiave di sottoscrizione](~/articles/cognitive-services/Speech-Service/get-started.md) per il servizio Voce.
* Un computer macOS con [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) o versione successiva.
* La destinazione impostata su iOS versione 9.3 o successiva.

## <a name="get-the-speech-sdk-for-ios"></a>Ottenere Speech SDK per iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Speech SDK di Servizi cognitivi per iOS viene attualmente distribuito come framework Cocoa.
Può essere scaricato da [questo sito Web](https://aka.ms/csspeech/iosbinary). Scaricare il file nella home directory.

## <a name="create-an-xcode-project"></a>Creare un progetto Xcode

Avviare Xcode e avviare un nuovo progetto selezionando **File** > **Nuovo** > **Progetto**.
Nella finestra di dialogo di selezione del modello scegliere **iOS Single View App**.

Nelle finestre di dialogo che seguono, effettuare le selezioni seguenti.

1. Nella finestra di dialogo **Opzioni progetto**:
    1. Immettere un nome per l'app di avvio rapido, ad esempio *helloworld*.
    1. Immettere un nome di organizzazione appropriato e l'identificatore dell'organizzazione, se si ha già di un account per sviluppatore Apple. Ai fini del test, usare un nome come *testorg*. Per firmare l'app, è necessario un profilo di provisioning corretto. Per altre informazioni, vedere il [sito per sviluppatori Apple](https://developer.apple.com/).
    1. Assicurarsi che per il progetto sia selezionato il linguaggio **Objective-C**.
    1. Deselezionare tutte le caselle di controllo per i test e il framework Core Data.

    ![Impostazioni del progetto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Selezionare la directory di progetto:
   1. Scegliere di inserire il progetto nella home directory. Con questo passaggio viene creata una directory helloworld nella directory home che contiene tutti i file del progetto Xcode.
   1. Disabilitare la creazione di un repository Git per il progetto di esempio.
   1. Modificare i percorsi nell'SDK nella schermata di impostazioni del progetto.
      1. Nella scheda **Generale** disponibile nell'intestazione **File binari incorporati** aggiungere la libreria SDK come framework selezionando **Aggiungi file binari incorporati** > **Aggiungi altro**. Passare alla directory home e selezionare il file `MicrosoftCognitiveServicesSpeech.framework`. Questa azione aggiunge la libreria SDK all'intestazione **Linked Framework and Libraries** (Framework e librerie collegati).
         ![Framework aggiunto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Passare alla scheda **Impostazioni di compilazione** e selezionare l'impostazione **Tutte**.
      1. Aggiungere la directory $(SRCROOT)/.. a **Framework Search Paths** (Percorsi di ricerca framework) sotto l'intestazione **Search Paths** (Percorsi di ricerca).

      ![Impostazione dei percorsi di ricerca framework](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurare l'interfaccia utente

L'app di esempio ha un'interfaccia utente molto semplice. Include due pulsanti per avviare il riconoscimento vocale da file o dall'input di un microfono e un'etichetta di testo per visualizzare il risultato. L'interfaccia utente viene configurata nella parte `Main.storyboard` del progetto. Aprire la visualizzazione XML dello storyboard facendo clic con il pulsante destro sulla voce `Main.storyboard` dell'albero del progetto e scegliendo **Apri come** > **Codice sorgente**.

Sostituire il codice XML generato automaticamente con questo codice:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Scaricare l'[esempio di file wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) facendo clic con il pulsante destro sul collegamento e scegliendo **Save target as** (Salva destinazione con nome).
   Aggiungere il file wav al progetto come risorsa trascinandolo da una finestra del Finder al livello radice della visualizzazione del progetto.
   Selezionare **Fine** nella finestra di dialogo seguente senza cambiare le impostazioni.
1. Sostituire il contenuto del file `ViewController.m` generato automaticamente con il codice seguente:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.
1. Sostituire la stringa `YourServiceRegion` con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.
1. Aggiungere la richiesta per l'accesso al microfono. Fare clic con il pulsante destro del mouse sulla voce `Info.plist` dell'albero del progetto e scegliere **Apri come** > **Codice sorgente**. Aggiungere le righe seguenti nella sezione `<dict>` e quindi salvare il file.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Rendere visibile l'output di debug selezionando **View** > **Debug area** > **Activate Console** (Visualizza > Area debug > Attiva console).
1. Scegliere il simulatore iOS o un dispositivo iOS connesso al computer di sviluppo come destinazione per l'app dall'elenco nel menu **Prodotto** > **Destinazione**.
1. Compilare ed eseguire l'esempio di codice nel simulatore iOS scegliendo **Prodotto** > **Esegui** dal menu. È anche possibile selezionare il pulsante **Riproduci**.
1. Dopo aver selezionato il pulsante **Recognize (File)** (Riconosci file) nell'app, verrà visualizzato il contenuto del file audio "What's the weather like?" nella parte inferiore dello schermo.

   ![App iOS simulata](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Dopo aver selezionato il pulsante **Recognize (Microphone)** (Riconosci microfono) nell'app e aver pronunciato alcune parole, verrà visualizzata la trascrizione di testo del parlato nella parte inferiore dello schermo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di Objective-C su GitHub](https://aka.ms/csspeech/samples)
