---
title: Audio Stream compressi con Speech SDK - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire lo streaming di audio compressi a servizi di riconoscimento vocale con Speech SDK. Disponibili per C++, C#e Java per Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012423"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Audio Stream compressi con Speech SDK

il SDK di riconoscimento vocale **Stream di Input Audio Compressed** API fornisce un modo per lo streaming di audio compressi per il servizio riconoscimento vocale usando PullStream o PushStream.

> [!IMPORTANT]
> Lo streaming di audio compresso è supportato solo per C++, C#e Java su Linux (Ubuntu 16.04 o 18.04 Ubuntu).
> Il supporto è limitato a MP3 e sono state/OGG.

## <a name="prerequisites"></a>Prerequisiti

È necessario installare queste dipendenze per l'uso di input audio compressi con Speech SDK per Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Audio compresso streaming

Per eseguire lo streaming in un formato audio compresso per i servizi di riconoscimento vocale, creare `PullAudioInputStream` o `PushAudioInputStream`. Creare quindi un `AudioConfig` da un'istanza della classe del flusso, che specifica il formato di compressione del flusso.

Si supponga di avere una classe di flusso di input denominata `myPushStream` e Usa sono state/OGG. Questo è ciò che il codice potrebbe essere simile: 

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

* [Ottenere la sottoscrizione di valutazione di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni su come riconoscimento vocale inC#](quickstart-csharp-dotnet-windows.md)
