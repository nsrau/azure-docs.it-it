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
ms.date: 10/26/2019
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 94b8fb026b61b52e8096cf54e1db30a6c260c04b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109964"
---
# <a name="specify-source-language-for-speech-to-text"></a>Specificare la lingua di origine per il riconoscimento vocale

In questo articolo si apprenderà come specificare la lingua di origine per un input audio passato all'SDK vocale per il riconoscimento vocale. Viene inoltre fornito il codice di esempio per specificare un modello di riconoscimento vocale personalizzato per un migliore riconoscimento.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Come specificare la lingua di origine inC#

Il primo passaggio consiste nel creare un `SpeechConfig`:

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Specificare quindi la lingua di origine dell'audio con `SpeechRecognitionLanguage`:

```csharp
speechConfig.SpeechRecognitionLanguage = "de-DE";
```

Se si usa un modello personalizzato per il riconoscimento, è possibile specificare l'endpoint con `EndpointId`:

```csharp
speechConfig.EndpointId = "The Endpoint ID for your custom model.";
```

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Come specificare la lingua di origine inC++

In questo esempio, la lingua di origine viene fornita in modo esplicito come parametro usando il metodo `FromConfig`.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

In questo esempio, la lingua di origine viene fornita utilizzando `SourceLanguageConfig`. Quindi, la `sourceLanguageConfig` viene passata come parametro per `FromConfig` quando si crea il `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

In questo esempio la lingua di origine e l'endpoint personalizzato vengono forniti utilizzando `SourceLanguageConfig`. Il `sourceLanguageConfig` viene passato come parametro a `FromConfig` quando si crea il `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` e `SetEndpointId` sono metodi deprecati della classe `SpeechConfig` in C++ e Java. L'uso di questi metodi è sconsigliato e non deve essere usato quando si costruisce un `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Come specificare la lingua di origine in Java

In questo esempio, la lingua di origine viene fornita in modo esplicito quando si crea una nuova `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In questo esempio, la lingua di origine viene fornita utilizzando `SourceLanguageConfig`. Quindi, la `sourceLanguageConfig` viene passata come parametro durante la creazione di una nuova `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In questo esempio la lingua di origine e l'endpoint personalizzato vengono forniti utilizzando `SourceLanguageConfig`. Quindi, la `sourceLanguageConfig` viene passata come parametro durante la creazione di una nuova `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` e `setEndpointId` sono metodi deprecati della classe `SpeechConfig` in C++ e Java. L'uso di questi metodi è sconsigliato e non deve essere usato quando si costruisce un `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Come specificare la lingua di origine in Python

Il primo passaggio consiste nel creare un `speech_config`:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Specificare quindi la lingua di origine dell'audio con `speech_recognition_language`:

```Python
speech_config.speech_recognition_language="de-DE"
```

Se si usa un modello personalizzato per il riconoscimento, è possibile specificare l'endpoint con `endpoint_id`:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Come specificare la lingua di origine in JavaScript

Il primo passaggio consiste nel creare un `SpeechConfig`:

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

Il primo passaggio consiste nel creare un `speechConfig`:

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
