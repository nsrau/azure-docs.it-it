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
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1b134fd3d09eeda340e7323638a36b68336242c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362028"
---
# <a name="specify-source-language-for-speech-to-text"></a>Specificare la lingua di origine per il riconoscimento vocale

In questo articolo si apprenderà come specificare la lingua di origine per un input audio passato all'SDK vocale per il riconoscimento vocale. Viene inoltre fornito il codice di esempio per specificare un modello di riconoscimento vocale personalizzato per un migliore riconoscimento.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Come specificare la lingua di origine in C #

Nell'esempio seguente, la lingua di origine viene fornita in modo esplicito come parametro mediante `SpeechRecognizer` costrutto.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Nell'esempio seguente, la lingua di origine viene fornita utilizzando `SourceLanguageConfig` . `sourceLanguageConfig`Viene quindi passato come parametro per `SpeechRecognizer` costruire.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Nell'esempio seguente vengono forniti la lingua di origine e l'endpoint personalizzato utilizzando `SourceLanguageConfig` . `sourceLanguageConfig`Viene quindi passato come parametro per `SpeechRecognizer` costruire.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` e `EndpointId` i metodi set sono deprecati dalla `SpeechConfig` classe in C#. L'uso di questi metodi è sconsigliato e non deve essere usato quando si costruisce un oggetto `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Come specificare la lingua di origine in C++

Nell'esempio seguente, la lingua di origine viene fornita in modo esplicito come parametro usando il `FromConfig` metodo.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Nell'esempio seguente, la lingua di origine viene fornita utilizzando `SourceLanguageConfig` . `sourceLanguageConfig`Viene quindi passato come parametro a `FromConfig` quando si crea l'oggetto `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Nell'esempio seguente vengono forniti la lingua di origine e l'endpoint personalizzato utilizzando `SourceLanguageConfig` . `sourceLanguageConfig`Viene passato come parametro a quando si `FromConfig` crea l'oggetto `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` e `SetEndpointId` sono metodi deprecati della `SpeechConfig` classe in C++ e Java. L'uso di questi metodi è sconsigliato e non deve essere usato quando si costruisce un oggetto `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Come specificare la lingua di origine in Java

Nell'esempio seguente la lingua di origine viene fornita in modo esplicito durante la creazione di un nuovo oggetto `SpeechRecognizer` .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Nell'esempio seguente, la lingua di origine viene fornita utilizzando `SourceLanguageConfig` . `sourceLanguageConfig`Viene quindi passato come parametro durante la creazione di un nuovo oggetto `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Nell'esempio seguente vengono forniti la lingua di origine e l'endpoint personalizzato utilizzando `SourceLanguageConfig` . `sourceLanguageConfig`Viene quindi passato come parametro durante la creazione di un nuovo oggetto `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` e `setEndpointId` sono metodi deprecati della `SpeechConfig` classe in C++ e Java. L'uso di questi metodi è sconsigliato e non deve essere usato quando si costruisce un oggetto `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Come specificare la lingua di origine in Python

Nell'esempio seguente, la lingua di origine viene fornita in modo esplicito come parametro mediante `SpeechRecognizer` costrutto.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

Nell'esempio seguente, la lingua di origine viene fornita utilizzando `SourceLanguageConfig` . `SourceLanguageConfig`Viene quindi passato come parametro per `SpeechRecognizer` costruire.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

Nell'esempio seguente vengono forniti la lingua di origine e l'endpoint personalizzato utilizzando `SourceLanguageConfig` . `SourceLanguageConfig`Viene quindi passato come parametro per `SpeechRecognizer` costruire.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language``endpoint_id`le proprietà e sono deprecate dalla `SpeechConfig` classe in Python. L'uso di queste proprietà è sconsigliato e non deve essere usato quando si costruisce un oggetto `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Come specificare la lingua di origine in JavaScript

Il primo passaggio consiste nel creare un oggetto `SpeechConfig` :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Specificare quindi la lingua di origine dell'audio con `speechRecognitionLanguage` :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se si usa un modello personalizzato per il riconoscimento, è possibile specificare l'endpoint con `endpointId` :

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Come specificare la lingua di origine in Objective-C

Nell'esempio seguente, la lingua di origine viene fornita in modo esplicito come parametro mediante `SPXSpeechRecognizer` costrutto.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

Nell'esempio seguente, la lingua di origine viene fornita utilizzando `SPXSourceLanguageConfiguration` . `SPXSourceLanguageConfiguration`Viene quindi passato come parametro per `SPXSpeechRecognizer` costruire.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

Nell'esempio seguente vengono forniti la lingua di origine e l'endpoint personalizzato utilizzando `SPXSourceLanguageConfiguration` . `SPXSourceLanguageConfiguration`Viene quindi passato come parametro per `SPXSpeechRecognizer` costruire.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage``endpointId`le proprietà e sono deprecate dalla `SPXSpeechConfiguration` classe in Objective-C. L'uso di queste proprietà è sconsigliato e non deve essere usato quando si costruisce un oggetto `SPXSpeechRecognizer` .

::: zone-end

## <a name="see-also"></a>Vedere anche

* Per un elenco delle lingue e delle impostazioni locali supportate per il riconoscimento vocale, vedere [supporto](language-support.md)per le lingue.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)