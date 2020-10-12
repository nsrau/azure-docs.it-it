---
title: Query sull'endpoint contenitore sintesi vocale
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 7e5ea8dcddce31a414d983d14fba483eb388d5d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334677"
---
Il contenitore fornisce [API endpoint basate su REST](../rest-text-to-speech.md). Sono disponibili molti [progetti di codice sorgente di esempio](https://github.com/Azure-Samples/Cognitive-Speech-TTS) per la piattaforma, il Framework e le varianti del linguaggio disponibili.

Con i contenitori di sintesi vocale standard o neurale, è consigliabile fare affidamento sulle impostazioni locali e sulla voce del tag di immagine scaricato. Ad esempio, se è stato scaricato il `latest` tag le impostazioni locali predefinite sono `en-US` e la `AriaRUS` voce. L' `{VOICE_NAME}` argomento sarà quindi [`en-US-AriaRUS`](../language-support.md#standard-voices) . Vedere l'esempio di SSML di seguito:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Tuttavia, per la sintesi vocale *personalizzata* è necessario ottenere la **voce o il modello** dal [portale vocale personalizzato](https://aka.ms/custom-voice-portal). Il nome del modello personalizzato è sinonimo del nome della voce. Passare alla pagina **Training** e copiare la **voce o il modello** da usare come `{VOICE_NAME}` argomento.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modello Voice personalizzato-nome vocale":::

Vedere l'esempio di SSML di seguito:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Viene ora creata una richiesta HTTP POST, che fornisce alcune intestazioni e un payload di dati. Sostituire il `{VOICE_NAME}` segnaposto con il proprio valore.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


Questo comando:

* Costruisce una richiesta HTTP POST per l' `speech/synthesize/cognitiveservices/v1` endpoint.
* Specifica un' `Accept` intestazione di `audio/*`
* Specifica un' `Content-Type` intestazione di `application/ssml+xml` . per ulteriori informazioni, vedere il [corpo della richiesta](../rest-text-to-speech.md#request-body).
* Specifica un' `X-Microsoft-OutputFormat` intestazione di `riff-16khz-16bit-mono-pcm` , per altre opzioni vedere [output audio](../rest-text-to-speech.md#audio-outputs).
* Invia la richiesta [SSML (Speech Synthesis Markup Language)](../speech-synthesis-markup.md) fornita all' `{VOICE_NAME}` endpoint.