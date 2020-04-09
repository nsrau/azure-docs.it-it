---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c029165a4994e5e3f73399fb00a1775b67128e44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879021"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Sintesi vocale o Sintesi vocale personalizzata

Il contenitore fornisce le API dell'endpoint di query basato su websocket, a cui si accede tramite [Speech SDK.](../index.yml) Per impostazione predefinita, Speech SDK usa i servizi vocali online. Per usare il contenitore, è necessario modificare il metodo di inizializzazione.

> [!TIP]
> Quando si usa Speech SDK con i contenitori, non è necessario fornire la chiave di sottoscrizione della risorsa di riconoscimento vocale di Azure [o un token di connessione dell'autenticazione.](../rest-speech-to-text.md#authentication)

Vedere gli esempi seguenti.

# <a name="c"></a>[C#](#tab/csharp)

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a questa chiamata utilizzando [l'host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)contenitore :

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

a questa chiamata utilizzando [l'host](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)contenitore :

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
