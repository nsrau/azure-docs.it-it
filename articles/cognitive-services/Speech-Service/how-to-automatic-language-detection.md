---
title: Come utilizzare il rilevamento automatico della lingua per la sintesi vocale
titleSuffix: Azure Cognitive Services
description: Speech SDK supporta il rilevamento automatico della lingua per la sintesi vocale. Quando si utilizza questa funzionalità, l'audio fornito viene confrontato con un elenco fornito di lingue e viene determinata la corrispondenza più probabile. Il valore restituito può quindi essere utilizzato per selezionare il modello di linguaggio utilizzato per la sintesi vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5592fc3e50db892c6abb09fc2516b8e1c03f0f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239593"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Rilevamento automatico della lingua per la sintesi vocale

Il rilevamento automatico della lingua viene utilizzato per determinare la corrispondenza più probabile per l'audio passato all'SDK di riconoscimento vocale rispetto a un elenco di lingue fornite. Il valore restituito dal rilevamento automatico della lingua viene quindi utilizzato per selezionare il modello linguistico per la sintesi vocale, fornendo una trascrizione più accurata. Per sapere quali lingue sono disponibili, vedere [Supporto della lingua](language-support.md).

In questo articolo verrà illustrato come `AutoDetectSourceLanguageConfig` utilizzare `SpeechRecognizer` per costruire un oggetto e recuperare la lingua rilevata.

> [!IMPORTANT]
> Questa funzionalità è disponibile solo per l'SDK di riconoscimento vocale per C, C, Java e Python.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Rilevamento automatico della lingua con Speech SDK

Il rilevamento automatico della lingua ha attualmente un limite lato servizi di due lingue per rilevamento. Tenere presente questa limitazione `AudoDetectSourceLanguageConfig` durante la costruzione dell'oggetto. Negli esempi riportati di seguito `AutoDetectSourceLanguageConfig`verrà creato un `SpeechRecognizer`oggetto , quindi verrà utilizzato per costruire un file .

> [!TIP]
> È inoltre possibile specificare un modello personalizzato da utilizzare quando si esegue la sintesi vocale. Per ulteriori informazioni, consultate Usare un modello personalizzato per il [rilevamento automatico della lingua.](#use-a-custom-model-for-automatic-language-detection)

I frammenti di codice seguenti illustrano come usare il rilevamento automatico della lingua nelle app:The following snippets illustrate how to use automatic language detection in your apps:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Usare un modello personalizzato per il rilevamento automatico della linguaUse a custom model for automatic language detection

Oltre al rilevamento della lingua tramite i modelli del servizio di riconoscimento vocale, è possibile specificare un modello personalizzato per il riconoscimento avanzato. Se non viene fornito un modello personalizzato, il servizio utilizzerà il modello di linguaggio predefinito.

I frammenti seguenti illustrano come specificare un modello personalizzato nella chiamata al servizio di riconoscimento vocale. Se la lingua `en-US`rilevata è , viene utilizzato il modello predefinito. Se la lingua `fr-FR`rilevata è , viene utilizzato l'endpoint per il modello personalizzato:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di riferimento di Speech SDK](speech-sdk.md)
