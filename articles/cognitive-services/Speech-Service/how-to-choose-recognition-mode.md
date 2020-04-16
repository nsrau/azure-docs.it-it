---
title: Scegliere una modalità di riconoscimento vocale con Speech SDK
titleSuffix: Azure Cognitive Services
description: Scopri come scegliere la modalità di riconoscimento migliore quando usi Speech SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5fdca371e9188ef69068ddbcaa416cbb2b44054c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402149"
---
# <a name="choose-a-speech-recognition-mode"></a>Scegliere una modalità di riconoscimento vocale

Quando si considerano le operazioni di riconoscimento vocale-testo, [Speech SDK](speech-sdk.md) fornisce più modalità per l'elaborazione vocale. Concettualmente, a volte chiamato la modalità di *riconoscimento*. In questo articolo vengono confrontate le varie modalità di riconoscimento.

## <a name="recognize-once"></a>Riconosci una volta

Se si desidera elaborare ogni espressione una "frase" alla volta, utilizzare la funzione "riconosci una volta". Questo metodo rileverà un'espressione riconosciuta dall'input a partire dall'inizio del riconoscimento vocale rilevato fino alla pausa successiva. Di solito, una pausa segna la fine di una frase o di una linea di pensiero.

Al termine di un'espressione riconosciuta, il servizio interrompe l'elaborazione dell'audio da tale richiesta. Il limite massimo per il riconoscimento è una durata della frase di 20 secondi.

::: zone pivot="programming-language-csharp"

Per ulteriori informazioni `RecognizeOnceAsync` sull'utilizzo della funzione, vedere la [documentazione di .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Per ulteriori informazioni `RecognizeOnceAsync` sull'utilizzo della funzione, vedere la documentazione di [C. Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Per ulteriori informazioni `recognizeOnceAsync` sull'utilizzo della funzione, vedere la [documentazione](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)di Java Speech SDK .

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Per altre informazioni `recognize_once` sull'uso della funzione, vedere i documenti di [Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Per altre lingue, vedere i documenti di [riferimento su Speech SDK.](speech-to-text.md#speech-sdk-reference-docs)

::: zone-end

## <a name="continuous"></a>Continuo

Se è necessario il riconoscimento a esecuzione prolungata, utilizzare le funzioni start e stop corrispondenti per il riconoscimento continuo. La funzione start avvierà e continuerà l'elaborazione di tutte le espressioni fino a quando non si richiama la funzione stop o fino a quando non è trascorso troppo tempo in silenzio. Quando si utilizza la modalità continua, assicurarsi di registrarsi ai vari eventi che verranno attivati al momento del verificarsi. Ad esempio, l'evento "riconosciuto" viene generato quando si verifica il riconoscimento vocale. È necessario disporre di un gestore eventi sul posto per gestire il riconoscimento.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end
::: zone pivot="programming-language-more"

Per altre lingue, vedere i documenti di [riferimento su Speech SDK.](speech-to-text.md#speech-sdk-reference-docs)

::: zone-end

## <a name="dictation"></a>Dettatura

Quando si utilizza il riconoscimento continuo, è possibile abilitare l'elaborazione della dettatura utilizzando la funzione "Abilita dettatura" corrispondente. Questa modalità farà sì che l'istanza di configurazione vocale interpreti le descrizioni delle parole delle strutture della frase, ad esempio la punteggiatura. Ad esempio, l'espressione "Segno di interrogazione in città" verrebbe interpretata come il testo "Vivete in città?".

::: zone pivot="programming-language-csharp"

Per ulteriori informazioni `EnableDictation` sull'utilizzo della funzione, vedere la [documentazione di .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Per ulteriori informazioni `EnableDictation` sull'utilizzo della funzione, vedere la documentazione di [C. Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Per ulteriori informazioni `enableDictation` sull'utilizzo della funzione, vedere la [documentazione](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)di Java Speech SDK .

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Per altre informazioni `enable_dictation` sull'uso della funzione, vedere i documenti di [Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Per altre lingue, vedere i documenti di [riferimento su Speech SDK.](speech-to-text.md#speech-sdk-reference-docs)

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplora altri esempi di Speech SDK su GitHub](https://aka.ms/csspeech/samples)
