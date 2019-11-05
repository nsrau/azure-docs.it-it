---
title: Query sull'endpoint del contenitore sintesi vocale
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: a55cf5ea6aa696d0cf0cdd619dc22839d748d83c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491148"
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

a questa chiamata usando l' [endpoint](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet)del contenitore:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

a questa chiamata usando l' [endpoint](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)del contenitore:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

***
