---
title: Come specificare la lingua di origine per il riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Speech SDK consente di specificare la lingua di origine durante la conversione di un riconoscimento vocale in un testo. Questo articolo descrive come usare i metodi FromConfig e SourceLanguageConfig per consentire al servizio di riconoscimento vocale di comprendere la lingua di origine e fornire una destinazione del modello personalizzata.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80235958"
---
# <a name="specify-source-language-for-speech-to-text"></a>Specificare la lingua di origine per il riconoscimento vocale

In questo articolo si apprenderà come specificare la lingua di origine per un input audio passato all'SDK vocale per il riconoscimento vocale. Viene inoltre fornito il codice di esempio per specificare un modello di riconoscimento vocale personalizzato per un migliore riconoscimento.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Come specificare la lingua di origine in C #

In questo esempio, la lingua di origine viene fornita in modo esplicito come `SpeechRecognizer` parametro mediante costrutto.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In questo esempio, la lingua di origine viene fornita `SourceLanguageConfig`utilizzando. `sourceLanguageConfig` Viene quindi passato come parametro per `SpeechRecognizer` costruire.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In questo esempio la lingua di origine e l'endpoint personalizzato vengono forniti `SourceLanguageConfig`utilizzando. `sourceLanguageConfig` Viene quindi passato come parametro per `SpeechRecognizer` costruire.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`e `EndpointId` i `SpeechConfig` metodi set sono deprecati dalla classe in C#. L'uso di questi metodi è sconsigliato e non deve essere usato quando si costruisce un `SpeechRecognizer`oggetto.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Come specificare la lingua di origine in C++

In questo esempio, la lingua di origine viene fornita in modo esplicito come parametro `FromConfig` usando il metodo.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

In questo esempio, la lingua di origine viene fornita `SourceLanguageConfig`utilizzando. `sourceLanguageConfig` Viene quindi passato come parametro a `FromConfig` quando si crea l'oggetto `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

In questo esempio la lingua di origine e l'endpoint personalizzato vengono forniti `SourceLanguageConfig`utilizzando. `sourceLanguageConfig` Viene passato come parametro a `FromConfig` quando si crea l'oggetto `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`e `SetEndpointId` sono metodi deprecati della `SpeechConfig` classe in C++ e Java. L'uso di questi metodi è sconsigliato e non deve essere usato quando si costruisce un `SpeechRecognizer`oggetto.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Come specificare la lingua di origine in Java

In questo esempio la lingua di origine viene fornita in modo esplicito durante la `SpeechRecognizer`creazione di un nuovo oggetto.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In questo esempio, la lingua di origine viene fornita `SourceLanguageConfig`utilizzando. `sourceLanguageConfig` Viene quindi passato come parametro durante la creazione di un nuovo `SpeechRecognizer`oggetto.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In questo esempio la lingua di origine e l'endpoint personalizzato vengono forniti `SourceLanguageConfig`utilizzando. `sourceLanguageConfig` Viene quindi passato come parametro durante la creazione di un nuovo `SpeechRecognizer`oggetto.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`e `setEndpointId` sono metodi deprecati della `SpeechConfig` classe in C++ e Java. L'uso di questi metodi è sconsigliato e non deve essere usato quando si costruisce un `SpeechRecognizer`oggetto.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Come specificare la lingua di origine in Python

In questo esempio, la lingua di origine viene fornita in modo esplicito come `SpeechRecognizer` parametro mediante costrutto.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

In questo esempio, la lingua di origine viene fornita `SourceLanguageConfig`utilizzando. `SourceLanguageConfig` Viene quindi passato come parametro per `SpeechRecognizer` costruire.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

In questo esempio la lingua di origine e l'endpoint personalizzato vengono forniti `SourceLanguageConfig`utilizzando. `SourceLanguageConfig` Viene quindi passato come parametro per `SpeechRecognizer` costruire.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`le `endpoint_id` `SpeechConfig` proprietà e sono deprecate dalla classe in Python. L'uso di queste proprietà è sconsigliato e non deve essere usato quando si costruisce un `SpeechRecognizer`oggetto.

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Come specificare la lingua di origine in JavaScript

Il primo passaggio consiste nel creare un `SpeechConfig`oggetto:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Specificare quindi la lingua di origine dell'audio con `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se si usa un modello personalizzato per il riconoscimento, è possibile specificare l'endpoint con `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Come specificare la lingua di origine in Objective-C

Il primo passaggio consiste nel creare un `speechConfig`oggetto:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Specificare quindi la lingua di origine dell'audio con `speechRecognitionLanguage`:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Se si usa un modello personalizzato per il riconoscimento, è possibile specificare l'endpoint con `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Vedere anche

* Per un elenco delle lingue e delle impostazioni locali supportate per il riconoscimento vocale, vedere [supporto](language-support.md)per le lingue.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
