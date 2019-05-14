---
title: 'Avvio rapido: Riconoscere i contenuti vocali, Objective-C - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Objective-C in iOS con Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: chlandsi
ms.openlocfilehash: 347969ac129faa9cbe841be097e2bc7fd66c6b8e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020562"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in Objective-C in iOS con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un'applicazione iOS in Objective-C usando Speech SDK di Servizi cognitivi per trascrivere il riconoscimento vocale dal microfono o da un file con audio registrato.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, ecco un elenco di prerequisiti:

* Una [chiave di sottoscrizione](get-started.md) per il servizio di riconoscimento vocale
* Un computer macOS con [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) o versione successiva
* Destinazione impostata su iOS versione 9.3 o successiva

## <a name="get-the-speech-sdk-for-ios"></a>Ottenere Speech SDK per iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `1.5.0`.

Speech SDK di Servizi cognitivi per iOS viene attualmente distribuito come un Framework di Cocoa.
Può essere scaricato da [questa pagina](https://aka.ms/csspeech/iosbinary). Scaricare il file nella home directory.

## <a name="create-an-xcode-project"></a>Creare un progetto Xcode

Avviare Xcode e avviare un nuovo progetto facendo clic su **File** > **Nuovo** > **Progetto**.
Nella finestra di dialogo di selezione del modello, scegliere il modello "iOS Single View App".

Nelle finestre di dialogo che seguono, effettuare le selezioni seguenti:

1. Finestra di dialogo Opzioni di progetto
    1. Immettere un nome per l'app di Guida introduttiva, ad esempio `helloworld`.
    1. Immettere un nome di organizzazione appropriato e l'identificatore dell'organizzazione, se si dispone già di un account Apple Developer. A scopo di test è possibile scegliere qualsiasi nome, ad esempio `testorg`. Per firmare l'app, è necessario un profilo di provisioning corretto. Per dettagli, visitare il [sito Apple Developer](https://developer.apple.com/).
    1. Assicurarsi che Objective-C venga scelto come lingua per il progetto.
    1. Disabilitare tutte le caselle di spunta per i test e i dati principali.
    ![Impostazioni progetto](media/sdk/qs-objectivec-project-settings.png)
1. Selezionare la directory di progetto
    1. Scegliere di inserire il progetto nella home directory. Verrà creata una directory `helloworld` nella home directory che contiene tutti i file del progetto Xcode.
    1. Disabilitare la creazione di un repository Git per il progetto di esempio.
    1. Modificare i percorsi nel SDK in *Impostazioni di progetto*.
        1. Nella scheda **Generale** sotto l'intestazione **Embedded Binaries** (File binari incorporati) aggiungere la libreria SDK come framework: **Add embedded binaries** > **Add other...** (Aggiungi file binari incorporati > Aggiungi altro) > Passare alla home directory e scegliere il file `MicrosoftCognitiveServicesSpeech.framework`. La libreria SDK verrà quindi aggiunta automaticamente all'intestazione **Linked Framework and Libraries** (Framework e librerie collegate).
        ![Framework aggiunto](media/sdk/qs-objectivec-framework.png)
        1. Andare alla scheda **Impostazioni di compilazione** e attivare le impostazioni **Tutte**.
        1. Aggiungere la directory `$(SRCROOT)/..` ai *Percorsi di ricerca Framework* sotto l'intestazione **Percorsi di ricerca**.
        ![Impostazione percorso di ricerca Framework](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurare l'interfaccia utente

L'app di esempio avrà un'interfaccia utente molto semplice: Due pulsanti per avviare il riconoscimento vocale da file o dall'input del microfono e un'etichetta di testo per visualizzare il risultato.
L'interfaccia utente viene configurata nella parte `Main.storyboard` del progetto.
Aprire la visualizzazione XML dello storyboard facendo clic con il pulsante destro sulla voce `Main.storyboard` dell'albero del progetto e selezionando **Apri come...**   >  **Codice sorgente**.
Sostituire il codice XML generato automaticamente con questo codice:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Scaricare l'[esempio di file wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) facendo clic con il pulsante destro sul collegamento e scegliendo **Salva come...** . Aggiungere il file wav al progetto come risorsa trascinandolo da una finestra del Finder al livello radice della visualizzazione del progetto.
   Fare clic su **Fine** nella finestra di dialogo seguente senza modificare le impostazioni.
1. Sostituire il contenuto del file generato automaticamente `ViewController.m`:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.
1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).
1. Aggiungere la richiesta per l'accesso al microfono. Fare clic con il pulsante destro del mouse sulla voce `Info.plist` dell'albero del progetto e selezionare **Apri come** > **Codice sorgente**. Aggiungere le righe seguenti nella sezione `<dict>` e quindi salvare il file.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Compilazione ed esecuzione dell'esempio

1. Rendere visibile l'output di debug (**Visualizza** > **Area di debug** > **Attivare Console**).
1. Scegliere il simulatore iOS o un dispositivo iOS connesso al computer di sviluppo come destinazione per l'app dall'elenco nel menu **Prodotto** -> **Destinazione**.
1. Compilare ed eseguire l'esempio di codice nel simulatore iOS selezionando **Prodotto** -> **Esegui** dal menu o facendo clic sul pulsante **Riproduci**.
   Speech SDK attualmente supporta solo le piattaforme iOS a 64 bit.
1. Dopo avere fatto clic sul pulsante "Riconosci (file)" nell'app, verrà visualizzato il contenuto del file audio "Che tempo fa?" nella parte inferiore dello schermo.

   ![App iOS simulata](media/sdk/qs-objectivec-simulated-app.png)

1. Dopo avere fatto clic sul pulsante "Riconosci (microfono)" nell'app e avere pronunciato alcune parole, verrà visualizzato il testo pronunciato nella parte inferiore dello schermo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di Objective-C su GitHub](https://aka.ms/csspeech/samples)

