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
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: c6ab43b530c045eb4f2920b65f601ba981dfc8a2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020758"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Audio Stream compressi con Speech SDK

il SDK di riconoscimento vocale **Stream di Input Audio Compressed** API fornisce un modo per lo streaming di audio compressi per il servizio riconoscimento vocale usando PullStream o PushStream.

> [!IMPORTANT]
> Lo streaming di audio compresso è supportato solo per C++, C#e Java su Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9).
> Il supporto è limitato a MP3 e sono state/OGG.

## <a name="prerequisites"></a>Prerequisiti

Installare le dipendenze aggiuntive per l'uso di input audio compressi con Speech SDK per Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Audio compresso streaming

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

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
