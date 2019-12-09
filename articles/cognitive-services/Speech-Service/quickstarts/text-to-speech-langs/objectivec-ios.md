---
title: 'Guida introduttiva: Sintesi vocale, Objective-C - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sulla sintesi vocale in Objective-C in iOS con Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 09ee7f0918bce469186688a5d0e82441bb2e5457
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817779"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Guida introduttiva: Sintesi vocale in Objective-C in iOS con Speech SDK

Questo articolo spiega come creare un'app iOS in Objective-C usando Speech SDK di Servizi cognitivi per eseguire la sintesi vocale.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, ecco un elenco di prerequisiti:

* Una [chiave di sottoscrizione](~/articles/cognitive-services/Speech-Service/get-started.md) per il servizio Voce
* Un computer macOS con [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) o versione successiva
* Destinazione impostata su iOS versione 9.3 o successiva

## <a name="get-the-speech-sdk-for-ios"></a>Ottenere Speech SDK per iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Questa esercitazione non funzionerà con versioni dell'SDK precedenti alla 1.7.0.

Speech SDK di Servizi cognitivi per iOS viene attualmente distribuito come un Framework di Cocoa.
Può essere usato in progetti Xcode come [CocoaPod](https://cocoapods.org/) oppure scaricato da https://aka.ms/csspeech/iosbinary e collegato manualmente. Questa guida usa CocoaPod.

## <a name="create-an-xcode-project"></a>Creare un progetto Xcode

Avviare Xcode e avviare un nuovo progetto facendo clic su **File** > **Nuovo** > **Progetto**.
Nella finestra di dialogo di selezione del modello, scegliere il modello "iOS Single View App".

Nelle finestre di dialogo che seguono, effettuare le selezioni seguenti:

1. Finestra di dialogo Opzioni di progetto
    1. Immettere un nome per l'app di Guida introduttiva, ad esempio `helloworld`.
    1. Immettere un nome di organizzazione appropriato e l'identificatore dell'organizzazione, se si dispone già di un account Apple Developer. A scopo di test è possibile scegliere qualsiasi nome, ad esempio `testorg`. Per firmare l'app, è necessario un profilo di provisioning corretto. Per dettagli, visitare il [sito Apple Developer](https://developer.apple.com/).
    1. Assicurarsi che Objective-C venga scelto come lingua per il progetto.
    1. Disabilitare tutte le caselle di spunta per i test e i dati principali.
    ![Impostazioni progetto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. Selezionare la directory di progetto
    1. Scegliere di inserire il progetto nella home directory. Verrà creata una directory `helloworld` nella home directory che contiene tutti i file del progetto Xcode.
    1. Disabilitare la creazione di un repository Git per il progetto di esempio.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installare l'SDK come CocoaPod

1. Installare il gestore di dipendenze di CocoaPod come descritto in queste [istruzioni per l'installazione](https://guides.cocoapods.org/using/getting-started.html).
1. Passare alla directory dell'app di esempio (`helloworld`). Inserire nella directory un file di testo denominato `Podfile` e il contenuto seguente:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Passare alla directory `helloworld` in un terminale ed eseguire il comando `pod install`. Verrà generata un'area di lavoro Xcode `helloworld.xcworkspace` contenente l'app di esempio e Speech SDK come dipendenza. Quest'area di lavoro verrà usata di seguito.

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Aprire l'area di lavoro `helloworld.xcworkspace` in Xcode.
1. Sostituire il contenuto del file generato automaticamente `AppDelegate.m`:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Sostituire il contenuto del file generato automaticamente `ViewController.m`:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.
1. Sostituire la stringa `YourServiceRegion` con la [regione](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Rendere visibile l'output di debug (**Visualizza** > **Area di debug** > **Attivare Console**).
1. Scegliere il simulatore iOS o un dispositivo iOS connesso al computer di sviluppo come destinazione per l'app dall'elenco nel menu **Prodotto** > **Destinazione**.
1. Compilare ed eseguire l'esempio di codice nel simulatore iOS selezionando **Prodotto** > **Esegui** dal menu o facendo clic sul pulsante **Riproduci**.

   ![App iOS simulata](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Dopo aver immesso un testo e aver fatto clic sul pulsante nell'app, si dovrebbe sentire l'audio sintetizzato riprodotto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di Objective-C su GitHub](https://aka.ms/csspeech/samples)

