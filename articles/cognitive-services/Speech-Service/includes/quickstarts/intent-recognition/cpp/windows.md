---
title: 'Guida introduttiva: Riconoscere la voce, le finalità e le entità, C++ - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 277548c859370a700973d9f60556fe38e94548b0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280443"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
>
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Creare un'applicazione LUIS (Language Understanding) e ottenere una chiave dell'endpoint](../../../../quickstarts/create-luis.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="open-your-project-in-visual-studio"></a>Aprire il progetto in Visual Studio

Il primo passaggio consiste nel verificare che il progetto sia aperto in Visual Studio.

1. Avviare Visual Studio 2019.
2. Caricare il progetto e aprire `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere codice che funga da scheletro del progetto. Si noti che è stato creato un metodo asincrono denominato `recognizeIntent()`.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Prima di poter inizializzare un oggetto `IntentRecognizer`, è necessario creare una configurazione che usi la chiave e l'area dell'endpoint LUIS. Inserire questo codice nel metodo `recognizeIntent()`.

Questo esempio usa il metodo `FromSubscription()` per creare `SpeechConfig`. Per un elenco completo dei metodi disponibili, vedere [Classe SpeechConfig](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

> [!NOTE]
> È importante usare la chiave dell'endpoint LUIS e non le chiavi di avvio o di creazione perché per il riconoscimento finalità voce è valida solo la chiave dell'endpoint. Per istruzioni su come ottenere la chiave corretta, vedere [Creare un'applicazione LUIS e ottenere una chiave dell'endpoint](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

## <a name="initialize-an-intentrecognizer"></a>Inizializzare un oggetto IntentRecognizer

Creare ora un oggetto `IntentRecognizer`. Inserire questo codice nel metodo `recognizeIntent()`, immediatamente sotto la configurazione di Voce.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Aggiungere un oggetto LanguageUnderstandingModel e le finalità

A questo punto è necessario associare un oggetto `LanguageUnderstandingModel` al riconoscimento di finalità e aggiungere le finalità da riconoscere.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>Riconoscere una finalità

Dall'oggetto `IntentRecognizer` chiamare il metodo `RecognizeOnceAsync()`. Questo metodo consente al servizio Voce di rilevare che si sta inviando una singola frase per il riconoscimento e di interrompere il riconoscimento vocale una volta identificata la frase.
Per semplicità, attendere il completamento della funzionalità restituita.

All'interno dell'istruzione using aggiungere questo codice: [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>Visualizzare i risultati (o gli errori) del riconoscimento

Quando il servizio Voce restituisce il risultato del riconoscimento, in genere lo si usa per qualche scopo. Per semplicità, in questo caso il risultato verrà stampato sulla console.

All'interno dell'istruzione using, sotto `RecognizeOnceAsync()`, aggiungere il codice seguente: [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>Controllare il codice

A questo punto il codice dovrà avere questo aspetto:  
(in questa versione sono stati aggiunti alcuni commenti) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

A questo punto è possibile compilare l'app e testare il riconoscimento vocale con il servizio Voce.

1. **Compilare il codice**: dalla barra dei menu di Visual Studio scegliere **Compila** > **Compila soluzione**.
2. **Avviare l'app**: dalla barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.
3. **Avviare il riconoscimento**: verrà richiesto di pronunciare una frase in inglese. La voce viene inviata al servizio Voce, trascritta come testo e visualizzata nella console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]