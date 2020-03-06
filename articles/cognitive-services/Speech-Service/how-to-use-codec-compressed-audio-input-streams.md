---
title: Flusso audio compresso di codec con l'SDK vocale-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come trasmettere l'audio compresso al servizio riconoscimento vocale con l'SDK di riconoscimento vocale. Disponibile per C++, C#e Java per Linux, Java in Android e Objective-C in iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2b530da06b02091ce66ff7c116f3e17ddcc22497
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331111"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Uso dell'input audio compresso di codec con Speech SDK

L'API del **flusso di input audio compresso** dell'SDK vocale fornisce un modo per trasmettere audio compresso al servizio di riconoscimento vocale usando PullStream o PushStream.

> [!IMPORTANT]
> Il flusso audio di input compresso è attualmente C++supportato C#per, e Java in Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). È supportata anche per [Java in Android](how-to-use-codec-compressed-audio-input-streams-android.md) e [Objective-C nella piattaforma iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> La versione 1.7.0 o successiva dell'SDK vocale è obbligatoria (versione 1.10.0 o successiva per RHEL 8, CentOS 8).

Per WAV/PCM, vedere la documentazione sulla voce principale.  Al di fuori di WAV/PCM, sono supportati i formati di input compressi di codec seguenti:

- MP3
- OPUS/OGG
- FLAC
- ALEGGE nel contenitore WAV
- MULAW nel contenitore WAV

## <a name="prerequisites"></a>Prerequisiti

La gestione dell'audio compresso viene implementata con [GStreamer](https://gstreamer.freedesktop.org). Per motivi di licenza i binari gstreamer non vengono compilati e collegati con l'SDK di riconoscimento vocale. Lo sviluppatore di applicazioni deve quindi installare il codice seguente in 18,04, 16,04 e Debian 9 per usare l'audio di input compresso.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

In RHEL/CentOS 8:

```sh
sudo yum install gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

> [!NOTE]
> In RHEL/CentOS 8 seguire le istruzioni su [come configurare OpenSSL per Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="example-code-using-codec-compressed-audio-input"></a>Codice di esempio con input audio compresso codec

Per trasmettere in streaming un formato audio compresso al servizio riconoscimento vocale, creare `PullAudioInputStream` o `PushAudioInputStream`. Quindi, creare un `AudioConfig` da un'istanza della classe Stream, specificando il formato di compressione del flusso.

Si supponga di disporre di una classe di flusso di input denominata `myPushStream` e che usi OPUS/OGG. Il codice potrebbe essere simile al seguente:

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
* [Vedere come riconoscere il riconoscimento vocale in Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
