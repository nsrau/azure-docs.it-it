---
title: Riconoscimento vocale sintesi Markup Language (SSML) - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Uso di Speech Synthesis Markup Language per controllare la pronuncia e la prosodia nella sintesi vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9871e0106ee6caf11c5a1e24459fbd2044f5f3d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65021447"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) è un linguaggio di markup basato su XML che fornisce un modo per controllare la pronuncia e la *prosodia* della sintesi vocale. La prosodia è il ritmo e tono della voce, ovvero la sua musicalità, se si vuole. È possibile specificare le parole foneticamente, fornire suggerimenti per l'interpretazione dei numeri, inserire pause, controllare tono, volume e frequenza e altro ancora. Per altre informazioni, vedere [Speech Synthesis Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) (Speech Synthesis Markup Language (SSML) versione 1.0). 

Per un elenco completo delle lingue supportate, delle impostazioni locali e delle voci (neurale e standard), vedere [Supporto per le lingue](language-support.md#text-to-speech).

Le sezioni seguenti offrono esempi di attività di sintesi vocale comuni.

## <a name="adjust-speaking-style-for-neural-voices"></a>Regolare di parlare di voci neurale

Per modificare il modo di parlare, quando si utilizza una delle voci neurale, è possibile usare SSML.

Per impostazione predefinita, il servizio di sintesi vocale sintetizza testo in uno stile neutro. Le voci neurale estendono SSML con un `<mstts:express-as>` elemento che converte il testo in sintesi vocale in pronuncia diversi stili. Attualmente, i tag di stile sono supportati solo con queste voci:

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`.

A proposito delle modifiche di stile può essere applicato a livello di frase. Gli stili variano in base vocali. Se un tipo di stile non è supportato, il servizio restituirà la sintesi vocale come stile predefinito neutro.

| Chiamata vocale | Style | Descrizione | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | Esprime un'emozione che è positivo e soddisfazione |
| | type=`empathy` | Esprime un senso di occuparsi e comprensione |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Esprime un tono formale, simile a trasmissioni notizie |
| | type=`sentiment ` | Fornisce un messaggio tocca o una storia |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>Aggiungere un'interruzione
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Cambiare la velocità del parlato

Velocità di pronuncia può essere applicato a voices standard a livello di frase o word. Mentre velocità di pronuncia applicabile solo a neurale voices a livello di frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Pronuncia
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Cambiare il volume

Volume modifiche possono essere applicate a voices standard a livello di frase o word. Mentre le modifiche di volumi è applicabile solo ai neurale voices a livello di frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Cambiare il tono

Modifiche di tono possono essere applicate a voices standard a livello di frase o word. Mentre le modifiche di tono è applicabile solo ai neurale voices a livello di frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Cambiare il profilo di intonazione

> [!IMPORTANT]
> Modifiche di contour passo non sono supportate con voci neurale.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Usare più voci
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Passaggi successivi

* [Supporto per le lingue: voci, impostazioni locali, lingue](language-support.md)
