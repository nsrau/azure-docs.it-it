---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77563310"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Sintesi vocale o Sintesi vocale personalizzata

Il contenitore fornisce le API dell'endpoint di query basato su websocket, a cui si accede tramite [Speech SDK.](../index.yml) Per impostazione predefinita, Speech SDK usa i servizi vocali online. Per usare il contenitore, è necessario modificare il metodo di inizializzazione.

> [!TIP]
> Quando si usa Speech SDK con i contenitori, non è necessario fornire la chiave di sottoscrizione della risorsa di riconoscimento vocale di Azure [o un token di connessione dell'autenticazione.](../rest-speech-to-text.md#authentication)

Vedere gli esempi seguenti.

# <a name="c"></a>[C #](#tab/csharp)

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
