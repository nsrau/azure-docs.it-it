---
title: Come tenere traccia dell'utilizzo della memoria speech SDK - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Speech Service SDK supporta numerosi linguaggi di programmazione per la conversione da testo a testo e sintesi vocale, oltre alla traduzione vocale. In questo articolo vengono illustrati gli strumenti di gestione della memoria incorporati nell'SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456432"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Come tenere traccia dell'utilizzo della memoria di Speech SDK

Speech SDK si basa su una base di codice nativo proiettata in più linguaggi di programmazione tramite una serie di livelli di interoperabilità. Ogni proiezione specifica del linguaggio ha funzioni idiomaticamente corrette per gestire il ciclo di vita dell'oggetto. Inoltre, speech SDK include strumenti di gestione della memoria per tenere traccia dell'utilizzo delle risorse con la registrazione degli oggetti e i limiti degli oggetti. 

## <a name="how-to-read-object-logs"></a>Come leggere i registri oggetti

Se [la registrazione dell'SDK vocale è abilitata](how-to-use-logging.md), i tag di rilevamento vengono generati per consentire l'osservazione degli oggetti cronologici. Questi tag includono: 

* `TrackHandle` o `StopTracking` 
* Tipo di oggetto
* Il numero corrente di oggetti tracciati del tipo di oggetto e il numero corrente di cui si tiene traccia.

Ecco un log di esempio:Here's a sample log: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Impostare una soglia di avviso

È possibile creare una soglia di avviso e, se tale soglia viene superata (presupponendo che la registrazione sia abilitata), viene registrato un messaggio di avviso. Il messaggio di avviso contiene un dump di tutti gli oggetti esistenti insieme al relativo conteggio. Queste informazioni possono essere utilizzate per comprendere meglio i problemi. 

Per abilitare una soglia di avviso, è necessario specificarla su un `SpeechConfig` oggetto. Questo oggetto viene controllato quando viene creato un nuovo sistema di riconoscimento. Negli esempi seguenti si supponga di aver creato un'istanza di `SpeechConfig` called `config`:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Il valore predefinito per questa proprietà è 10.000.The default value for this property is 10,000.

## <a name="set-an-error-threshold"></a>Impostare una soglia di errore 

Utilizzando l'SDK vocale, è possibile impostare il numero massimo di oggetti consentiti in un determinato momento. Se questa impostazione è abilitata, quando viene raggiunto il numero massimo, i tentativi di creare nuovi oggetti di riconoscimento avranno esito negativo. Gli oggetti esistenti continueranno a funzionare.

Di seguito è riportato un errore di esempio:Here's a sample error:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Per abilitare una soglia di errore, è necessario specificarla su un `SpeechConfig` oggetto. Questo oggetto viene controllato quando viene creato un nuovo sistema di riconoscimento. Negli esempi seguenti si supponga di aver creato un'istanza di `SpeechConfig` called `config`:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Il valore predefinito per questa proprietà è il `size_t` valore massimo specifico della piattaforma per un tipo di dati. Un riconoscimento tipico consumerà da 7 a 10 oggetti interni.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere l'abbonamento alla versione di valutazione del servizio di riconoscimento vocaleGet your Speech service trial subscription](get-started.md)
* [Informazioni su come riconoscere il riconoscimento vocale con un microfono](quickstarts/speech-to-text-from-microphone.md)