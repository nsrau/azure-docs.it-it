---
title: Come specificare la lingua di origine per la sintesi vocale
titleSuffix: Azure Cognitive Services
description: Speech SDK consente di specificare la lingua di origine durante la conversione del riconoscimento vocale in testo. In questo articolo viene descritto come utilizzare il FromConfig e SourceLanguageConfig metodi per consentire al servizio di riconoscimento vocale di conoscere il linguaggio di origine e fornire una destinazione del modello personalizzato.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235958"
---
# <a name="specify-source-language-for-speech-to-text"></a>Specificare la lingua di origine per la sintesi vocale

In questo articolo verrà illustrato come specificare la lingua di origine per un input audio passato all'SDK vocale per il riconoscimento vocale. Inoltre, viene fornito codice di esempio per specificare un modello vocale personalizzato per un riconoscimento migliorato.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Come specificare il linguaggio di origine in CHow to specify source language in C #

In questo esempio, il linguaggio di `SpeechRecognizer` origine viene fornito in modo esplicito come parametro utilizzando construct.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In questo esempio, il linguaggio di origine viene fornito utilizzando `SourceLanguageConfig`. Quindi, `sourceLanguageConfig` l'oggetto viene `SpeechRecognizer` passato come parametro da costruire.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In questo esempio, il linguaggio di `SourceLanguageConfig`origine e l'endpoint personalizzato vengono forniti tramite . Quindi, `sourceLanguageConfig` l'oggetto viene `SpeechRecognizer` passato come parametro da costruire.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`e `EndpointId` set di metodi `SpeechConfig` sono deprecati dalla classe in C . L'utilizzo di questi metodi è sconsigliato e non deve `SpeechRecognizer`essere utilizzato durante la costruzione di un oggetto .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Come specificare il linguaggio di origine in C

In questo esempio, il linguaggio di origine `FromConfig` viene fornito in modo esplicito come parametro utilizzando il metodo .

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

In questo esempio, il linguaggio di origine viene fornito utilizzando `SourceLanguageConfig`. Quindi, `sourceLanguageConfig` l'oggetto viene `FromConfig` passato come `recognizer`parametro a durante la creazione dell'oggetto .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

In questo esempio, il linguaggio di `SourceLanguageConfig`origine e l'endpoint personalizzato vengono forniti tramite . L'oggetto `sourceLanguageConfig` viene passato `FromConfig` come `recognizer`parametro a durante la creazione dell'oggetto .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`e `SetEndpointId` sono metodi deprecati dalla `SpeechConfig` classe in C . L'utilizzo di questi metodi è sconsigliato e non deve `SpeechRecognizer`essere utilizzato durante la costruzione di un oggetto .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Come specificare il linguaggio di origine in Java

In questo esempio, il linguaggio di `SpeechRecognizer`origine viene fornito in modo esplicito quando si crea un nuovo oggetto .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

In questo esempio, il linguaggio di origine viene fornito utilizzando `SourceLanguageConfig`. Quindi, `sourceLanguageConfig` l'oggetto viene passato come `SpeechRecognizer`parametro durante la creazione di un nuovo oggetto .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

In questo esempio, il linguaggio di `SourceLanguageConfig`origine e l'endpoint personalizzato vengono forniti tramite . Quindi, `sourceLanguageConfig` l'oggetto viene passato come `SpeechRecognizer`parametro durante la creazione di un nuovo oggetto .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`e `setEndpointId` sono metodi deprecati dalla `SpeechConfig` classe in C . L'utilizzo di questi metodi è sconsigliato e non deve `SpeechRecognizer`essere utilizzato durante la costruzione di un oggetto .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Come specificare il linguaggio sorgente in Python

In questo esempio, il linguaggio di `SpeechRecognizer` origine viene fornito in modo esplicito come parametro utilizzando construct.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

In questo esempio, il linguaggio di origine viene fornito utilizzando `SourceLanguageConfig`. Quindi, `SourceLanguageConfig` l'oggetto viene `SpeechRecognizer` passato come parametro da costruire.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

In questo esempio, il linguaggio di `SourceLanguageConfig`origine e l'endpoint personalizzato vengono forniti tramite . Quindi, `SourceLanguageConfig` l'oggetto viene `SpeechRecognizer` passato come parametro da costruire.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`e `endpoint_id` le proprietà sono `SpeechConfig` deprecate dalla classe in Python. L'utilizzo di queste proprietà è sconsigliato e non deve `SpeechRecognizer`essere utilizzato durante la costruzione di un oggetto .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Come specificare il linguaggio di origine in Javascript

Il primo passo è `SpeechConfig`quello di creare un :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Successivamente, specificare la lingua `speechRecognitionLanguage`di origine dell'audio con:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se si utilizza un modello personalizzato per il riconoscimento, è possibile specificare l'endpoint con: `endpointId`

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Come specificare il linguaggio di origine in Objective-C

Il primo passo è `speechConfig`quello di creare un :

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Successivamente, specificare la lingua `speechRecognitionLanguage`di origine dell'audio con:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Se si utilizza un modello personalizzato per il riconoscimento, è possibile specificare l'endpoint con: `endpointId`

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Vedere anche

* Per un elenco delle lingue e delle impostazioni locali supportate per la sintesi vocale, vedere [Supporto della lingua](language-support.md).

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento di Speech SDK](speech-sdk.md)
