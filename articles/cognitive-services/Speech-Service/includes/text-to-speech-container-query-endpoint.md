---
title: Query sull'endpoint contenitore sintesi vocale
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491141"
---
Il contenitore fornisce [API endpoint basate su REST](../rest-text-to-speech.md). Sono disponibili molti [progetti di codice sorgente di esempio](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) per la piattaforma, il Framework e le varianti del linguaggio disponibili.

Con il contenitore *di sintesi vocale standard* , è consigliabile fare affidamento sulle impostazioni locali e sulla voce del tag di immagine scaricato. Ad esempio, se è stato scaricato il tag `latest` le impostazioni locali predefinite sono `en-US` e il `JessaRUS` voce. Il `{VOICE_NAME}` argomento verrebbe quindi [`en-US-JessaRUS`](../language-support.md#standard-voices). Vedere l'esempio di SSML di seguito:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Tuttavia, per la sintesi vocale *personalizzata* è necessario ottenere la **voce o il modello** dal [portale vocale personalizzato](https://aka.ms/custom-voice-portal). Il nome del modello personalizzato è sinonimo del nome della voce. Passare alla pagina **Training** e copiare la **voce o il modello** da usare come argomento `{VOICE_NAME}`.
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

Viene ora creata una richiesta HTTP POST, che fornisce alcune intestazioni e un payload di dati. Sostituire il segnaposto `{VOICE_NAME}` con il proprio valore.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Questo comando:

* Costruisce una richiesta HTTP POST per l'endpoint `speech/synthesize/cognitiveservices/v1`.
* Specifica un'intestazione `Accept` di `audio/*`
* Specifica un'intestazione `Content-Type` di `application/ssml+xml`. per ulteriori informazioni, vedere il [corpo della richiesta](../rest-text-to-speech.md#request-body).
* Specifica un'intestazione `X-Microsoft-OutputFormat` di `riff-16khz-16bit-mono-pcm`, per altre opzioni vedere [output audio](../rest-text-to-speech.md#audio-outputs).
* Invia la richiesta [SSML (Speech Synthesis Markup Language)](../speech-synthesis-markup.md) data la `{VOICE_NAME}` all'endpoint.