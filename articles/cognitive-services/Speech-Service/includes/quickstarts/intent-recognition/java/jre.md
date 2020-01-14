---
title: 'Avvio rapido: Riconoscere la voce, le finalità e le entità, Java - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/02/2020
ms.topic: include
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 420350f1fa119b53844a3c3f28405ced1c20fb91
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660511"
---
## <a name="prerequisites"></a>Prerequisites

Prima di iniziare:

* Se si tratta del primo progetto Java (JRE), usare questa guida per <a href="../quickstarts/create-project.md?tabs=jre" target="_blank">creare un progetto di esempio vuoto</a>.
* <a href="../quickstarts/setup-platform.md?tabs=jre" target="_blank">Installare Speech SDK per l'ambiente di sviluppo</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Creare un'app LUIS per il riconoscimento delle finalità

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Aprire il progetto

1. Aprire l'IDE preferito.
2. Caricare il progetto e aprire `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere codice che funga da scheletro del progetto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Prima di inizializzare un oggetto `IntentRecognizer`, è necessario creare una configurazione che usi la chiave e l'area della risorsa di previsione di LUIS.  

Inserire il codice nel blocco try/catch in `main()`. Assicurarsi di aggiornare questi valori:

* Sostituire `"YourLanguageUnderstandingSubscriptionKey"` con la chiave di previsione di LUIS. 
* Sostituire `"YourLanguageUnderstandingServiceRegion"` con l'area di LUIS. 

>[!TIP]
> Per informazioni su come trovare questi valori, vedere [Creare un'app LUIS per il riconoscimento delle finalità](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Questo esempio usa il metodo `FromSubscription()` per creare `SpeechConfig`. Per un elenco completo dei metodi disponibili, vedere [Classe SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

## <a name="initialize-an-intentrecognizer"></a>Inizializzare un oggetto IntentRecognizer

Creare ora un oggetto `IntentRecognizer`. Inserire questo codice immediatamente sotto la configurazione di Voce.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Aggiungere un oggetto LanguageUnderstandingModel e le finalità

È necessario associare un oggetto `LanguageUnderstandingModel` allo strumento di riconoscimento di finalità e aggiungere le finalità da riconoscere. Verranno usate le finalità del dominio predefinito per la domotica. 

Inserire questo codice sotto `IntentRecognizer`. Assicurarsi di sostituire `"YourLanguageUnderstandingAppId"` con l'ID dell'app LUIS. 

>[!TIP]
> Per informazioni su come trovare questo valore, vedere [Creare un'app LUIS per il riconoscimento delle finalità](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Riconoscere una finalità

Dall'oggetto `IntentRecognizer` chiamare il metodo `recognizeOnceAsync()`. Questo metodo consente al servizio Voce di rilevare che si sta inviando una singola frase per il riconoscimento e di interrompere il riconoscimento vocale una volta identificata la frase.

Inserire questo codice sotto il modello:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Visualizzare i risultati (o gli errori) del riconoscimento

Quando il servizio Voce restituisce il risultato del riconoscimento, in genere lo si usa per qualche scopo. Per semplicità, in questo caso il risultato verrà stampato sulla console.

Inserire questo codice sotto la chiamata a `recognizeOnceAsync()`: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Rilasciare le risorse

È importante rilasciare le risorse vocali quando non è più necessario usarle. Inserire questo codice alla fine del blocco try/catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Controllare il codice

A questo punto il codice dovrà avere questo aspetto:  

> [!NOTE]
> In questa versione sono stati aggiunti alcuni commenti.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
I successivi 15 secondi di input vocale dal microfono verranno riconosciuti e registrati nella finestra della console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]