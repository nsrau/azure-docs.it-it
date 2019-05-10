---
title: Codec Stream compressi audio con Speech SDK - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire lo streaming di audio compressi a servizi di riconoscimento vocale con Speech SDK. Disponibili per C++, C#e Java per Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 41a55eca321cbe1bfa23a889b8e3ce7c701ce769
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468050"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Usando i codec di compressione input audio con Speech SDK

il SDK di riconoscimento vocale **Stream di Input Audio Compressed** API fornisce un modo per lo streaming di audio compressi per il servizio riconoscimento vocale usando PullStream o PushStream.

> [!IMPORTANT]
> Lo streaming di audio compresso è supportato solo per C++, C#e Java su Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9).

Per wav/PCM vedere la documentazione principale di riconoscimento vocale.  Di fuori di PCM/wav, sono supportati i formati di input di codec di compressione seguenti:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Prerequisiti per l'uso di codec di compressione input audio

Installare le dipendenze aggiuntive per l'uso di input audio compressi con Speech SDK per Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Codice di esempio tramite codec di compressione input audio

Per eseguire lo streaming in un formato audio compresso per i servizi di riconoscimento vocale, creare `PullAudioInputStream` o `PushAudioInputStream`. Creare quindi un `AudioConfig` da un'istanza della classe del flusso, che specifica il formato di compressione del flusso.

Si supponga di avere una classe di flusso di input denominata `myPushStream` e Usa sono state/OGG. Il codice potrebbe essere simile al seguente:

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
