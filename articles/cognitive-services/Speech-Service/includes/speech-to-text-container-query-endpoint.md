---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1552dda28291112a2412bdf956bc49a0b541d7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95996437"
---
Il contenitore fornisce le API dell'endpoint di query basate su WebSocket, a cui si accede tramite l' [SDK di riconoscimento vocale](../index.yml). Per impostazione predefinita, l'SDK vocale usa i servizi di riconoscimento vocale online. Per usare il contenitore, è necessario modificare il metodo di inizializzazione.

> [!TIP]
> Quando si usa l'SDK di riconoscimento vocale con i contenitori, non è necessario specificare la chiave di sottoscrizione della risorsa vocale di Azure [o un Bearer token di autenticazione](../rest-speech-to-text.md#authentication).

Vedere gli esempi seguenti.

# <a name="c"></a>[C#](#tab/csharp)

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Per usare questa chiamata con l' [host](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?preserve-view=true&view=azure-dotnet)del contenitore:

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

Per usare questa chiamata con l' [endpoint](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)contenitore:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---