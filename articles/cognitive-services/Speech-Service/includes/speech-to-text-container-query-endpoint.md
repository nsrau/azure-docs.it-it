---
title: Query sull'endpoint del contenitore sintesi vocale
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2d96385f2d2d34d161739c55228220cf28871048
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132604"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Riconoscimento vocale o da Riconoscimento vocale personalizzato a testo

Il contenitore fornisce le API dell'endpoint di query basate su WebSocket, a cui si accede tramite l' [SDK di riconoscimento vocale](../index.md). Per impostazione predefinita, l'SDK vocale usa i servizi di riconoscimento vocale online. Per usare il contenitore, è necessario modificare il metodo di inizializzazione.

> [!TIP]
> Quando si usa l'SDK di riconoscimento vocale con i contenitori, non è necessario specificare la chiave di sottoscrizione della risorsa vocale di Azure [o un Bearer token di autenticazione](../rest-speech-to-text.md#authentication).

Vedere gli esempi seguenti.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a questa chiamata usando l' [host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)del contenitore:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

a questa chiamata usando l' [host](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)del contenitore:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
