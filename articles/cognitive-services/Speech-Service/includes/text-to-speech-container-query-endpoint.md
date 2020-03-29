---
title: Endpoint del contenitore di sintesi vocale della queryQuery Text-to-speech container endpoint
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73491141"
---
Il contenitore fornisce [API endpoint basate su REST.](../rest-text-to-speech.md) Sono disponibili molti progetti di [codice sorgente di esempio](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) per le varianti di piattaforma, framework e lingua.

Con il contenitore *Standard Text-to-speech,* è necessario fare affidamento sulle impostazioni locali e sulla voce del tag immagine scaricato. Ad esempio, se `latest` hai scaricato il `en-US` tag, le impostazioni locali predefinite sono e la `JessaRUS` voce. L'argomento `{VOICE_NAME}` sarebbe [`en-US-JessaRUS`](../language-support.md#standard-voices)quindi . Vedere l'esempio SSML di seguito:See the example SSML below:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Tuttavia, per la sintesi *vocale personalizzata* è necessario ottenere il **modello Voice /** dal portale vocale [personalizzato.](https://aka.ms/custom-voice-portal) Il nome del modello personalizzato è sinonimo del nome vocale. Passare alla pagina **Formazione** e copiare la voce `{VOICE_NAME}` / **modello** da utilizzare come argomento.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modello vocale personalizzato - nome vocale":::

Vedere l'esempio SSML di seguito:See the example SSML below:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Costruiamo una richiesta HTTP POST, fornendo alcune intestazioni e un payload di dati. Sostituire `{VOICE_NAME}` il segnaposto con un valore personalizzato.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Questo comando:

* Costruisce una richiesta HTTP `speech/synthesize/cognitiveservices/v1` POST per l'endpoint.
* Specifica un'intestazione `Accept` di`audio/*`
* Specifica un'intestazione `Content-Type` di `application/ssml+xml`, per ulteriori informazioni, vedere corpo della [richiesta](../rest-text-to-speech.md#request-body).
* Specifica un'intestazione `X-Microsoft-OutputFormat` di `riff-16khz-16bit-mono-pcm`, per altre opzioni vedere Output [audio](../rest-text-to-speech.md#audio-outputs).
* Invia la richiesta [SSML (Speech Synthesis Markup Language)](../speech-synthesis-markup.md) specificata all'endpoint. `{VOICE_NAME}`