---
title: 'Guida introduttiva: Tradurre la voce in voce, Python - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: e09d3fd2e880a01522b59d60a80f448192c0b689
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505228"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=python)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=python)

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire `quickstart.py` e sostituire tutto il codice in questo file con quanto segue.

    ````python
    import azure.cognitiveservices.speech as speechsdk

    speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"

    def translate_speech_to_speech():

        # Creates an instance of a speech translation config with specified subscription key and service region.
        # Replace with your own subscription key and service region (e.g., "westus").
        translation_config = speechsdk.translation.SpeechTranslationConfig(subscription=speech_key, region=service_region)

        # Sets source and target languages.
        # Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
        fromLanguage = 'en-US'
        toLanguage = 'de'
        translation_config.speech_recognition_language = fromLanguage
        translation_config.add_target_language(toLanguage)

        # Sets the synthesis output voice name.
        # Replace with the languages of your choice, from list found here: https://aka.ms/speech/tts-languages
        translation_config.voice_name = "de-DE-Hedda"

        # Creates a translation recognizer using and audio file as input.
        recognizer = speechsdk.translation.TranslationRecognizer(translation_config=translation_config)

        # Prepare to handle the synthesized audio data.
        def synthesis_callback(evt):
            size = len(evt.result.audio)
            print('AUDIO SYNTHESIZED: {} byte(s) {}'.format(size, '(COMPLETED)' if size == 0 else ''))

        recognizer.synthesizing.connect(synthesis_callback)

        # Starts translation, and returns after a single utterance is recognized. The end of a
        # single utterance is determined by listening for silence at the end or until a maximum of 15
        # seconds of audio is processed. It returns the recognized text as well as the translation.
        # Note: Since recognize_once() returns only a single utterance, it is suitable only for single
        # shot recognition like command or query.
        # For long-running multi-utterance recognition, use start_continuous_recognition() instead.
        print("Say something...")
        result = recognizer.recognize_once()

        # Check the result
        if result.reason == speechsdk.ResultReason.TranslatedSpeech:
            print("RECOGNIZED '{}': {}".format(fromLanguage, result.text))
            print("TRANSLATED into {}: {}".format(toLanguage, result.translations['de']))
        elif result.reason == speechsdk.ResultReason.RecognizedSpeech:
            print("RECOGNIZED: {} (text could not be translated)".format(result.text))
        elif result.reason == speechsdk.ResultReason.NoMatch:
            print("NOMATCH: Speech could not be recognized: {}".format(result.no_match_details))
        elif result.reason == speechsdk.ResultReason.Canceled:
            print("CANCELED: Reason={}".format(result.cancellation_details.reason))
            if result.cancellation_details.reason == speechsdk.CancellationReason.Error:
                print("CANCELED: ErrorDetails={}".format(result.cancellation_details.error_details))

    translate_speech_to_speech()
    ````

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](../../../../regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate a `quickstart.py`.

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

1. Eseguire l'esempio dalla console o nell'IDE:

   ```
   python quickstart.py
   ```

1. Pronunciare una frase o un'espressione in inglese. L'applicazione trasmette il parlato ai servizi Voce, che effettuano la traduzione e la trascrizione in testo (in questo caso in tedesco). I servizi Voce inviano quindi nuovamente l'audio sintetizzato e il testo all'applicazione per la visualizzazione.

   ```
   Say something...
   AUDIO SYNTHESIZED: 76784 byte(s)
   AUDIO SYNTHESIZED: 0 byte(s) (COMPLETE)
   RECOGNIZED 'en-US': What's the weather in Seattle?
   TRANSLATED into 'de': Wie ist das Wetter in Seattle?
   ```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
