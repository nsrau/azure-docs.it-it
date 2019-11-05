---
title: 'Procedura: usare il rilevamento automatico della lingua per il riconoscimento vocale al servizio riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Speech SDK supporta il rilevamento automatico della lingua per il riconoscimento vocale. Quando si usa questa funzionalità, l'audio fornito viene confrontato con un elenco di lingue specificato e viene determinata la corrispondenza più probabile. Il valore restituito può quindi essere usato per selezionare il modello di lingua usato per la sintesi vocale.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: e156704d3ad54c3437f921fae536a497e1d94868
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506967"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Rilevamento automatico della lingua per il riconoscimento vocale

Il rilevamento automatico della lingua viene usato per determinare la corrispondenza più probabile per l'audio passato all'SDK di riconoscimento vocale rispetto a un elenco di lingue fornite. Il valore restituito dal rilevamento automatico della lingua viene quindi usato per selezionare il modello di lingua per la sintesi vocale, offrendo una trascrizione più accurata. Per visualizzare le lingue disponibili, vedere [supporto](language-support.md)per le lingue.

In questo articolo si apprenderà come usare `AutoDetectSourceLanguageConfig` per costruire un oggetto `SpeechRecognizer` e recuperare il linguaggio rilevato.

> [!IMPORTANT]
> Questa funzionalità è disponibile solo per l'SDK di riconoscimento C++ vocale per e l'SDK di riconoscimento vocale per Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Rilevamento automatico della lingua con l'SDK di riconoscimento vocale

Il rilevamento automatico della lingua dispone attualmente di un limite lato servizi di due lingue per rilevamento. Tenere presente questa limitazione quando si costruisce l'oggetto `AudoDetectSourceLanguageConfig`. Negli esempi seguenti verrà creato un `AutoDetectSourceLanguageConfig`, che verrà quindi usato per costruire una `SpeechRecognizer`.

>[!TIP]
> È anche possibile specificare un modello personalizzato da usare quando si esegue il riconoscimento vocale in un testo. Per altre informazioni, vedere [usare un modello personalizzato per il rilevamento automatico della lingua](#use-a-custom-model-for-automatic-language-detection).

I frammenti di codice seguenti illustrano come usare il rilevamento automatico della lingua nelle app:

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Usare un modello personalizzato per il rilevamento automatico della lingua

Oltre al rilevamento del linguaggio con i modelli di servizio vocale, è possibile specificare un modello personalizzato per il riconoscimento migliorato. Se non viene fornito un modello personalizzato, il servizio utilizzerà il modello di lingua predefinito.

Nei frammenti di codice riportati di seguito viene illustrato come specificare un modello personalizzato nella chiamata al servizio di riconoscimento vocale. Se il linguaggio rilevato è `en-US`, viene utilizzato il modello predefinito. Se il linguaggio rilevato è `fr-FR`, viene utilizzato l'endpoint per il modello personalizzato:

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
