---
title: 'Avvio rapido: Riconoscere la voce, le finalità e le entità, Python - Servizio Voce'
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
ms.openlocfilehash: f2a46b8a0868aaa1097dd7f1ae738a2a3e06e3b8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772954"
---
## <a name="prerequisites"></a>Prerequisites

Prima di iniziare:

* Se si tratta del primo progetto Python, usare questa guida per <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md" target="_blank">creare un progetto di esempio vuoto</a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md" target="_blank">Installare Speech SDK per l'ambiente di sviluppo</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Creare un'app LUIS per il riconoscimento delle finalità

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Aprire il progetto

1. Aprire l'IDE preferito.
2. Creare un nuovo progetto e un file denominato `quickstart.py`, quindi aprirlo.

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere codice che funga da scheletro del progetto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Prima di inizializzare un oggetto `IntentRecognizer`, è necessario creare una configurazione che usi la chiave e l'area della risorsa di previsione di LUIS.

Inserire questo codice in `quickstart.py`. Assicurarsi di aggiornare questi valori:

* Sostituire `"YourLanguageUnderstandingSubscriptionKey"` con la chiave di previsione di LUIS. 
* Sostituire `"YourLanguageUnderstandingServiceRegion"` con l'area di LUIS. 

>[!TIP]
> Per informazioni su come trovare questi valori, vedere [Creare un'app LUIS per il riconoscimento delle finalità](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Questo esempio crea l'oggetto `SpeechConfig` usando la chiave e l'area di LUIS. Per un elenco completo dei metodi disponibili, vedere [Classe SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

## <a name="initialize-an-intentrecognizer"></a>Inizializzare un oggetto IntentRecognizer

Creare ora un oggetto `IntentRecognizer`. Inserire questo codice immediatamente sotto la configurazione di Voce.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Aggiungere un oggetto LanguageUnderstandingModel e le finalità

È necessario associare un oggetto `LanguageUnderstandingModel` allo strumento di riconoscimento di finalità e aggiungere le finalità da riconoscere. Verranno usate le finalità del dominio predefinito per la domotica. 

Inserire questo codice sotto `IntentRecognizer`. Assicurarsi di sostituire `"YourLanguageUnderstandingAppId"` con l'ID dell'app LUIS. 

>[!TIP]
> Per informazioni su come trovare questo valore, vedere [Creare un'app LUIS per il riconoscimento delle finalità](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Riconoscere una finalità

Dall'oggetto `IntentRecognizer` chiamare il metodo `recognize_once()`. Questo metodo consente al servizio Voce di rilevare che si sta inviando una singola frase per il riconoscimento e di interrompere il riconoscimento vocale una volta identificata la frase.

Inserire questo codice sotto il modello:

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Visualizzare i risultati (o gli errori) del riconoscimento

Quando il servizio Voce restituisce il risultato del riconoscimento, in genere lo si usa per qualche scopo. Per semplicità, in questo caso il risultato verrà stampato sulla console.

Sotto la chiamata a `recognize_once()` aggiungere il codice seguente: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Controllare il codice

A questo punto il codice dovrà avere questo aspetto:  

> [!NOTE]
> In questa versione sono stati aggiunti alcuni commenti.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

Eseguire l'esempio dalla console o nell'IDE:

```
python quickstart.py
```

I successivi 15 secondi di input vocale dal microfono verranno riconosciuti e registrati nella finestra della console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
