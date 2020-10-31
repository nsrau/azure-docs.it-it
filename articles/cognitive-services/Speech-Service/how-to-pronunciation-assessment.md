---
title: Come usare l'SDK di riconoscimento vocale per la valutazione della pronuncia
titleSuffix: Azure Cognitive Services
description: Speech SDK supporta la valutazione della pronuncia, che consente di valutare la qualità di pronuncia dell'input vocale, con indicatori di accuratezza, fluidità, completezza e così via.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 245a00acb07d1c0e769a243413fccdf64d544f5a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133685"
---
# <a name="pronunciation-assessment"></a>Valutazione della pronuncia

La valutazione della pronuncia valuta la pronuncia vocale e fornisce commenti e suggerimenti sull'accuratezza e la fluidità dell'audio parlato.
Con la valutazione della pronuncia, gli Learner del linguaggio possono esercitarsi, ottenere commenti e suggerimenti istantanei e migliorare la loro pronuncia in modo che possano comunicare e presentare in tutta sicurezza.
Gli educatori possono usare la funzionalità per valutare la pronuncia di più speaker in tempo reale.

In questo articolo si apprenderà come impostare `PronunciationAssessmentConfig` e recuperare `PronunciationAssessmentResult` usando l'SDK di riconoscimento vocale.

> [!NOTE]
> La funzionalità di valutazione della pronuncia è attualmente disponibile solo nelle aree `westus` , `eastasia` e e `centralindia` supporta solo la lingua `en-US` .

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Valutazione della pronuncia con l'SDK di riconoscimento vocale

Negli esempi seguenti verrà creato un oggetto, che verrà `PronunciationAssessmentConfig` quindi applicato a `SpeechRecognizer` .

I frammenti di codice seguenti illustrano come usare il rilevamento automatico della lingua nelle app:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Parametri di configurazione della valutazione della pronuncia

Questa tabella elenca i parametri di configurazione per la valutazione della pronuncia.

| Parametro | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| ReferenceText | Testo su cui verrà valutata la pronuncia. | Necessario |
| GradingSystem | Sistema di punti per la taratura dei punteggi. I valori accettati sono `FivePoint` e `HundredMark`. L'impostazione predefinita è `FivePoint`. | Facoltativo |
| Granularità | Granularità della valutazione. I valori accettati sono `Phoneme` , che mostra il punteggio sul livello full-text, Word e fonema, `Word` , che mostra il punteggio a livello di testo intero e di parola, `FullText` , che mostra il punteggio solo sul livello full-text. L'impostazione predefinita è `Phoneme`. | Facoltativo |
| EnableMiscue | Abilita il calcolo miscue. Con questa funzionalità abilitata, le parole pronunciate verranno confrontate con il testo di riferimento e verranno contrassegnate con omissione/inserimento in base al confronto. I valori accettati sono `False` e `True`. L'impostazione predefinita è `False`. | Facoltativo |
| ScenarioId | GUID che indica un sistema di punti personalizzato. | Facoltativo |

### <a name="pronunciation-assessment-result-parameters"></a>Parametri dei risultati della valutazione della pronuncia

Questa tabella elenca i parametri di risultato della valutazione della pronuncia.

| Parametro | Descrizione |
|-----------|-------------|
| `AccuracyScore` | Accuratezza della pronuncia del riconoscimento vocale. L'accuratezza indica quanto i fonemi corrispondono alla pronuncia di un altoparlante nativo. Il Punteggio di accuratezza di Word e full-text viene aggregato dal punteggio di accuratezza del livello fonema. |
| `FluencyScore` | La fluidità del parlato specificato. La fluidità indica il livello di precisione con cui il riconoscimento vocale corrisponde all'utilizzo di interruzioni automatiche da parte di un altoparlante nativo. |
| `CompletenessScore` | Completezza del riconoscimento vocale, determinato calcolando il rapporto tra parole pronunciate e input di testo di riferimento. |
| `PronunciationScore` | Punteggio complessivo che indica la qualità della pronuncia della voce specificata. Viene aggregato da `AccuracyScore` `FluencyScore` e con il `CompletenessScore` peso. |
| `ErrorType` | Questo valore indica se una parola viene omessa, inserita o pronunciata male rispetto a `ReferenceText` . I valori possibili sono `None` (ovvero nessun errore in questa parola) `Omission` , `Insertion` e `Mispronunciation` . |

## <a name="next-steps"></a>Passaggi successivi

<!-- TODO: update the sample links after release -->

<!-- ::: zone pivot="programming-language-csharp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-cpp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-java"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-python"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-objectivec"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) on GitHub for automatic language detection
::: zone-end -->

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
