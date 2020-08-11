---
title: Come tenere traccia dell'utilizzo della memoria dell'SDK vocale-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: L'SDK del servizio riconoscimento vocale supporta numerosi linguaggi di programmazione per la conversione di sintesi vocale e sintesi vocale, oltre alla traduzione vocale. Questo articolo illustra gli strumenti di gestione della memoria incorporati nell'SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6be6ca00b2bc5d7b35fb71437809754f129df96
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054636"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Come tenere traccia dell'utilizzo della memoria dell'SDK vocale

Speech SDK si basa su una base di codice nativa proiettata in più linguaggi di programmazione tramite una serie di livelli di interoperabilità. Ogni proiezione specifica del linguaggio ha idiomatically le funzionalità corrette per gestire il ciclo di vita dell'oggetto. Inoltre, l'SDK vocale include gli strumenti di gestione della memoria per tenere traccia dell'utilizzo delle risorse con la registrazione degli oggetti e i limiti degli oggetti. 

## <a name="how-to-read-object-logs"></a>Come leggere i log degli oggetti

Se [la registrazione dell'SDK di riconoscimento vocale è abilitata](how-to-use-logging.md), i tag di rilevamento vengono generati per consentire l'osservazione cronologica degli oggetti. Questi tag includono: 

* `TrackHandle` o `StopTracking` 
* Tipo di oggetto
* Il numero corrente di oggetti che registrano il tipo di oggetto e il numero corrente rilevato.

Di seguito è riportato un esempio di log: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Impostare una soglia di avviso

È possibile scegliere di creare una soglia di avviso e se tale soglia viene superata (presupponendo che la registrazione sia abilitata), viene registrato un messaggio di avviso. Il messaggio di avviso contiene un dump di tutti gli oggetti esistenti insieme al relativo conteggio. Queste informazioni possono essere usate per comprendere meglio i problemi. 

Per abilitare una soglia di avviso, è necessario specificarla in un `SpeechConfig` oggetto. Questo oggetto viene verificato quando viene creato un nuovo riconoscimento. Negli esempi seguenti si presuppone che sia stata creata un'istanza di `SpeechConfig` denominata `config` :

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
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Il valore predefinito per questa proprietà è 10.000.

## <a name="set-an-error-threshold"></a>Imposta una soglia di errore 

Con l'SDK di riconoscimento vocale è possibile impostare il numero massimo di oggetti consentiti in un determinato momento. Se questa impostazione è abilitata, quando viene raggiunto il numero massimo, i tentativi di creare nuovi oggetti Recognizer avranno esito negativo. Gli oggetti esistenti continueranno a funzionare.

Ecco un errore di esempio:

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

Per abilitare una soglia di errore, è necessario specificarla in un `SpeechConfig` oggetto. Questo oggetto viene verificato quando viene creato un nuovo riconoscimento. Negli esempi seguenti si presuppone che sia stata creata un'istanza di `SpeechConfig` denominata `config` :

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
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Il valore predefinito per questa proprietà è il valore massimo specifico della piattaforma per un `size_t` tipo di dati. Un riconoscimento tipico utilizzerà tra 7 e 10 oggetti interni.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sull'SDK di riconoscimento vocale](speech-sdk.md)