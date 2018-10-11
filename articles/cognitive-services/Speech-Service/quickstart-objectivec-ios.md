---
title: 'Guida introduttiva: Riconoscimento vocale in Objective-C su iOS con Speech SDK di Servizi cognitivi'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in Objective-C su iOS con Speech SDK di Servizi cognitivi
services: cognitive-services
author: chlandsi
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: chlandsi
ms.openlocfilehash: 3945bf0ae6edc0af0db90efca6811aeb22494592
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883433"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-cognitive-services-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale in Objective-C su iOS con Speech SDK di Servizi cognitivi

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un'applicazione iOS in Objective-C usando Speech SDK di Servizi cognitivi per trascrivere il riconoscimento vocale.

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Un Mac con Xcode 9.4.1 installato come ambiente di sviluppo iOS. Questa esercitazione è destinata alle versioni iOS 11.4. Se non si dispone di Xcode, è possibile installarlo dall'[App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>Ottenere Speech SDK per iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `1.0.0`.

Speech SDK di Servizi cognitivi per Mac e iOS viene attualmente distribuito come un Framework di Cocoa.
È possibile scaricarlo da https://aka.ms/csspeech/iosbinary. Scaricare il file nella home directory.


## <a name="create-an-xcode-project"></a>Creare un progetto Xcode 

Avviare Xcode e avviare un nuovo progetto facendo clic su **File** > **Nuovo** > **Progetto**.
Nella finestra di dialogo di selezione del modello, scegliere il modello "iOS Single View App".

Nelle finestre di dialogo che seguono, effettuare le selezioni seguenti:

1. Finestra di dialogo Opzioni di progetto
    1. Immettere un nome per l'app di Guida introduttiva, ad esempio `helloworld`.
    1. Immettere un nome di organizzazione appropriato e l'identificatore dell'organizzazione, se si dispone già di un account Apple Developer. A scopo di test è possibile scegliere qualsiasi nome, ad esempio `testorg`. Per firmare l'app, è anche necessario un profilo di provisioning corretto. Consultare il [sito Apple developer](https://developer.apple.com/) per informazioni dettagliate.
    1. Assicurarsi che Objective-C venga scelto come lingua per il progetto.
    1. Disabilitare tutte le caselle di spunta per i test e i dati principali.
    ![Impostazioni progetto](media/sdk/qs-objectivec-project-settings.png)
1. Selezionare la directory di progetto
    1. Scegliere di inserire il progetto nella home directory. Verrà creato una directory `helloworld` nella home directory che contiene tutti i file per il progetto Xcode.
    1. Disabilitare la creazione di un repository Git per il progetto di esempio.
    1. Modificare i percorsi nel SDK in *Impostazioni di progetto*.
        1. Nella scheda **Generale** disponibile nell'intestazione **File binari incorporati**, aggiungere la libreria SDK come un framework: **Aggiungi file binari incorporati** > **Aggiungi altri...**  > Passare alla home directory e scegliere il file `MicrosoftCognitiveServicesSpeech.framework`. Verrà aggiunta automaticamente anche la libreria SDK all'intestazione **Framework e librerie collegati**.
        ![Framework aggiunto](media/sdk/qs-objectivec-framework.png)
        1. Andare alla scheda **Impostazioni di compilazione** e attivare le impostazioni **Tutte**.
        1. Aggiungere la directory `$(SRCROOT)/..` ai *Percorsi di ricerca Framework* sotto l'intestazione **Percorsi di ricerca**.
        ![Impostazione percorso di ricerca Framework](media/sdk/qs-objectivec-framework-search-paths.png)


## <a name="set-up-the-ui"></a>Configurare l'interfaccia utente

L'app di esempio avrà un'interfaccia utente molto semplice: un pulsante per avviare l'elaborazione del file e un'etichetta di testo per visualizzare il risultato.
L'interfaccia utente viene configurata nella parte `Main.storyboard` del progetto.
Aprire la visualizzazione XML dello storyboard facendo clic con il pulsante destro sulla voce `Main.storyboard` dell'albero del progetto e selezionando **Apri come...**   >  **Codice sorgente**.
Sostituire il XML generato automaticamente con questo:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Scaricare l'[esempio di file wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) facendo clic con il pulsante destro sul collegamento e scegliendo **Salva come...** . Aggiungere il file wav al progetto come risorsa trascinandolo da una finestra del Finder al livello radice della visualizzazione del progetto.
Fare clic su **Fine** nella finestra di dialogo seguente senza modificare le impostazioni.
1. Sostituire il contenuto del file generato automaticamente `ViewController.m`:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Aggiungere la richiesta per l'accesso al microfono. Fare clic con il pulsante destro sulla voce di info.plist dell'albero del progetto e selezionare **Apri come...**   >  **Codice sorgente**. Aggiungere le righe seguenti nella sezione `<dict>` e quindi salvare il file.
    ```xml
    <key>NSMicrophoneUsageDescription</key>

    <string>Need microphone access for speech recognition from microphone.</string>
    ```
1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.
1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).


## <a name="building-and-running-the-sample"></a>Compilazione ed esecuzione dell'esempio

1. Rendere visibile l'output di debug (**Visualizza** > **Area di debug** > **Attivare Console**).
1. Compilare ed eseguire l'esempio di codice nel simulatore iOS selezionando **Prodotto** -> **Esegui** dal menu o facendo clic sul pulsante **Riproduci**. Per l'esecuzione in un dispositivo iOS, connettere il dispositivo al computer di sviluppo e selezionare il dispositivo come destinazione in esecuzione. Speech SDK attualmente supporta solo la piattaforma iOS a 64 bit.
1. Dopo aver fatto clic sul pulsante "Recognize!" nell'app, verrà visualizzato il contenuto del file audio. "Che tempo fa?" nella parte inferiore dello schermo.

 ![App iOS simulata](media/sdk/qs-objectivec-simulated-app.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/objectivec-ios`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scaricare gli esempi](speech-sdk.md#get-the-samples)
