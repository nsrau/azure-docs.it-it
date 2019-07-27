---
title: Flusso audio compresso di codec con l'SDK vocale-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire lo streaming di audio compresso in servizi vocali di Azure con l'SDK di riconoscimento vocale. Disponibile per C++, C#e Java per Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559554"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Uso dell'input audio compresso di codec con Speech SDK

L'API del **flusso di input audio compresso** dell'SDK vocale fornisce un modo per trasmettere audio compresso al servizio di riconoscimento vocale usando PullStream o PushStream.

> [!IMPORTANT]
> Il flusso audio compresso è supportato solo C++per C#, e Java in Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9).
> È necessario l'SDK di riconoscimento vocale 1.4.0 o versione successiva.

Per WAV/PCM, vedere la documentazione sulla voce principale.  Al di fuori di WAV/PCM, sono supportati i formati di input compressi di codec seguenti:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Prerequisiti per l'uso dell'input audio compresso dei codec

Installare queste dipendenze aggiuntive per usare l'input audio compresso con l'SDK di riconoscimento vocale per Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Codice di esempio con input audio compresso codec

Per eseguire lo streaming in un formato audio compresso per i servizi di `PullAudioInputStream` riconoscimento `PushAudioInputStream`vocale, creare o. Quindi, creare un `AudioConfig` oggetto da un'istanza della classe Stream, specificando il formato di compressione del flusso.

Si supponga di disporre di una classe di flusso di input `myPushStream` denominata e che usi Opus/OGG. Il codice potrebbe essere simile al seguente:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
