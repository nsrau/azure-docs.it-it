---
title: Scegliere una modalità di riconoscimento vocale con l'SDK vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come scegliere la modalità di riconoscimento migliore quando si usa l'SDK di riconoscimento vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 51bf005bdad4197120fed96894ac1cdd150738ee
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75935227"
---
# <a name="choose-a-speech-recognition-mode"></a>Scegliere una modalità di riconoscimento vocale

Quando si considerano le operazioni di riconoscimento vocale, l' [SDK vocale](speech-sdk.md) fornisce più modalità per elaborare la voce. Concettualmente, a volte detta *modalità di riconoscimento*. In questo articolo vengono confrontate le varie modalità di riconoscimento.

## <a name="recognize-once"></a>Riconosci una volta

Se si desidera elaborare ogni espressione una "frase" alla volta, utilizzare la funzione "Recognize once". Questo metodo rileverà un enunciato riconosciuto dall'input a partire dall'inizio del riconoscimento vocale rilevato fino alla pausa successiva. In genere, una pausa contrassegna la fine di una frase o una riga di riflessione.

Alla fine di un'espressione riconosciuta, il servizio interrompe l'elaborazione dell'audio dalla richiesta. Il limite massimo per il riconoscimento è una durata della frase di 20 secondi.

::: zone pivot="programming-language-csharp"

Per ulteriori informazioni sull'utilizzo della funzione `RecognizeOnceAsync`, vedere la [documentazione di .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync().ConfigureAwait(false);
```

::: zone-end
::: zone pivot="programming-language-cpp"

Per ulteriori informazioni sull'utilizzo della funzione `RecognizeOnceAsync`, vedere la [ C++ documentazione relativa all'SDK di sintesi vocale](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Per altre informazioni sull'uso della funzione `recognizeOnceAsync`, vedere la [documentazione di Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Per altre informazioni sull'uso della funzione `recognize_once`, vedere la [documentazione di Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Per altre lingue, vedere la [documentazione di riferimento per l'SDK di riconoscimento vocale](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Continuo

Se è necessario un riconoscimento con esecuzione prolungata, usare le funzioni di arresto Start e corrispondenti per il riconoscimento continuo. La funzione Start avvierà e continuerà l'elaborazione di tutti gli enunciati fino a quando non si richiama la funzione stop o fino a quando non viene superato il tempo di inattività. Quando si usa la modalità continua, assicurarsi di eseguire la registrazione ai vari eventi che verranno attivati in seguito all'occorrenza. Ad esempio, l'evento "riconosciuto" viene generato quando si verifica il riconoscimento vocale. Per gestire il riconoscimento è necessario disporre di un gestore eventi. Un limite di 10 minuti di tempo totale di riconoscimento vocale per sessione viene applicato dal servizio di riconoscimento vocale.

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
await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
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

Per altre lingue, vedere la [documentazione di riferimento per l'SDK di riconoscimento vocale](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Dettatura

Quando si usa il riconoscimento continuo, è possibile abilitare l'elaborazione della dettatura usando la funzione "Enable dettation" corrispondente. Questa modalità farà sì che l'istanza di configurazione vocale interpreti le descrizioni di parole delle strutture di frase, ad esempio la punteggiatura. Ad esempio, l'espressione "vivi nel punto interrogativo della città" verrebbe interpretata come il testo "vivi in città?".

::: zone pivot="programming-language-csharp"

Per ulteriori informazioni sull'utilizzo della funzione `EnableDictation`, vedere la [documentazione di .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Per ulteriori informazioni sull'utilizzo della funzione `EnableDictation`, vedere la [ C++ documentazione relativa all'SDK di sintesi vocale](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Per altre informazioni sull'uso della funzione `enableDictation`, vedere la [documentazione di Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Per altre informazioni sull'uso della funzione `enable_dictation`, vedere la [documentazione di Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Per altre lingue, vedere la [documentazione di riferimento per l'SDK di riconoscimento vocale](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare altri esempi di SDK di riconoscimento vocale su GitHub](https://aka.ms/csspeech/samples)
