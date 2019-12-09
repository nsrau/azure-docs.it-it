---
title: 'Guida introduttiva: Riconoscere la voce, le finalità e le entità, Java - Servizio Voce'
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
ms.openlocfilehash: 49aac37d298a1d2cd52e815ae6fa5e08e6cc80c2
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815895"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
>
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Creare un'applicazione LUIS (Language Understanding) e ottenere una chiave dell'endpoint](../../../../quickstarts/create-luis.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>Aprire il progetto

Caricare il progetto e aprire `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere stringhe di codice che svolgano la funzione di scheletro del progetto.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Prima di poter inizializzare un oggetto `IntentRecognizer`, è necessario creare una configurazione che usi la chiave e l'area dell'endpoint LUIS. Inserire il codice nel blocco try o catch nel metodo main

Questo esempio usa il metodo `FromSubscription()` per creare `SpeechConfig`. Per un elenco completo dei metodi disponibili, vedere [Classe SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

> [!NOTE]
> È importante usare la chiave dell'endpoint LUIS e non le chiavi di avvio o di creazione perché per il riconoscimento finalità voce è valida solo la chiave dell'endpoint. Per istruzioni su come ottenere la chiave corretta, vedere [Creare un'applicazione LUIS e ottenere una chiave dell'endpoint](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-an-intentrecognizer"></a>Inizializzare un oggetto IntentRecognizer

Creare ora un oggetto `IntentRecognizer`. Inserire questo codice immediatamente sotto la configurazione di Voce.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Aggiungere LanguageUnderstandingModel e Intents

A questo punto è necessario associare un oggetto `LanguageUnderstandingModel` al riconoscimento di finalità e aggiungere le finalità da riconoscere.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Riconoscere una finalità

Dall'oggetto `IntentRecognizer` chiamare il metodo `recognizeOnceAsync()`. Questo metodo consente al servizio Voce di rilevare che si sta inviando una singola frase per il riconoscimento e di interrompere il riconoscimento vocale una volta identificata la frase.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Visualizzare i risultati (o gli errori) del riconoscimento

Quando il servizio Voce restituisce il risultato del riconoscimento, in genere lo si usa per qualche scopo. Per semplicità, in questo caso il risultato verrà stampato sulla console.

Sotto la chiamata a `recognizeOnceAsync()` aggiungere il codice seguente: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Rilasciare le risorse

È importante rilasciare le risorse vocali quando non è più necessario usarle. Inserire questo codice alla fine del blocco try o catch: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Controllare il codice

A questo punto il codice dovrà avere questo aspetto:  
(in questa versione sono stati aggiunti alcuni commenti) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
I successivi 15 secondi di input vocale dal microfono verranno riconosciuti e registrati nella finestra della console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]