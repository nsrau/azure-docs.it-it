---
title: 'Guida introduttiva: Riconoscere la voce, le finalità e le entità, Python - Servizio Voce'
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
ms.openlocfilehash: f39ddc8a3460bc026bdac96c18b5bea4d6ecfc0f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280437"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
>
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Creare un'applicazione LUIS (Language Understanding) e ottenere una chiave dell'endpoint](../../../../quickstarts/create-luis.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>Aprire il progetto

Aprire Quickstart.py nell'editor di Python.

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere stringhe di codice che svolgano la funzione di scheletro del progetto.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Prima di poter inizializzare un oggetto `IntentRecognizer`, è necessario creare una configurazione che usi la chiave e l'area dell'endpoint LUIS. Inserire quindi questo codice.

Questo esempio crea l'oggetto `SpeechConfig` usando la chiave e l'area di LUIS. Per un elenco completo dei metodi disponibili, vedere [Classe SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

> [!NOTE]
> È importante usare la chiave dell'endpoint LUIS e non le chiavi di avvio o di creazione perché per il riconoscimento finalità voce è valida solo la chiave dell'endpoint. Per istruzioni su come ottenere la chiave corretta, vedere [Creare un'applicazione LUIS e ottenere una chiave dell'endpoint](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>Inizializzare un oggetto IntentRecognizer

Creare ora un oggetto `IntentRecognizer`. Inserire questo codice immediatamente sotto la configurazione di Voce.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Aggiungere LanguageUnderstandingModel e Intents

A questo punto è necessario associare un oggetto `LanguageUnderstandingModel` al riconoscimento di finalità e aggiungere le finalità da riconoscere.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Riconoscere una finalità

Dall'oggetto `IntentRecognizer` chiamare il metodo `recognize_once()`. Questo metodo consente al servizio Voce di rilevare che si sta inviando una singola frase per il riconoscimento e di interrompere il riconoscimento vocale una volta identificata la frase.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Visualizzare i risultati (o gli errori) del riconoscimento

Quando il servizio Voce restituisce il risultato del riconoscimento, in genere lo si usa per qualche scopo. Per semplicità, in questo caso il risultato verrà stampato sulla console.

All'interno dell'istruzione using, sotto la chiamata a `recognize_once()`, aggiungere il codice seguente: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Controllare il codice

A questo punto il codice dovrà avere questo aspetto:  
(in questa versione sono stati aggiunti alcuni commenti) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

Eseguire l'esempio dalla console o nell'IDE:

    ````
    python quickstart.py
    ````

I successivi 15 secondi di input vocale dal microfono verranno riconosciuti e registrati nella finestra della console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
