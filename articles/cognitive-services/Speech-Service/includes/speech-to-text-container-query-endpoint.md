---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1773e22a54cc86e7736c91e4be757caa0250cbf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421931"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Riconoscimento vocale o da Riconoscimento vocale personalizzato a testo

Il contenitore fornisce le API dell'endpoint di query basate su WebSocket, a cui si accede tramite l' [SDK di riconoscimento vocale](../index.yml). Per impostazione predefinita, l'SDK vocale usa i servizi di riconoscimento vocale online. Per usare il contenitore, è necessario modificare il metodo di inizializzazione.

> [!TIP]
> Quando si usa l'SDK di riconoscimento vocale con i contenitori, non è necessario specificare la chiave di sottoscrizione della risorsa vocale di Azure [o un Bearer token di autenticazione](../rest-speech-to-text.md#authentication).

Vedere gli esempi seguenti.

# <a name="c"></a>[C#](#tab/csharp)

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a questa chiamata usando l' [host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)del contenitore:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

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
