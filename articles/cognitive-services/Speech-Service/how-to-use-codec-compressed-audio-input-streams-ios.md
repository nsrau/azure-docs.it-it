---
title: Flusso audio compresso di codec con l'SDK di riconoscimento vocale in iOS-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire lo streaming di audio compresso in servizi vocali di Azure con l'SDK di riconoscimento vocale in iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 9a66e4ecf2230caad233a4eff12c0fadc95409d5
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803811"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Uso dell'input audio compresso di codec con Speech SDK in iOS

L'API del **flusso di input audio compresso** dell'SDK vocale fornisce un modo per trasmettere audio compresso al servizio di riconoscimento vocale usando un flusso pull o push.

> [!IMPORTANT]
> Speech SDK versione 1.7.0 o successiva è necessario per lo streaming di audio compresso in iOS. È supportato anche per [ C++, C#e Java in Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) e [Java in Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

Per WAV/PCM, vedere la documentazione sulla voce principale.  Al di fuori di WAV/PCM, sono supportati i formati di input compressi di codec seguenti:

- MP3
- OPUS/OGG
- FLAC
- ALEGGE nel contenitore WAV
- MULAW nel contenitore WAV

## <a name="prerequisites"></a>Prerequisiti

La gestione dell'audio compresso viene implementata con [GStreamer](https://gstreamer.freedesktop.org).
Per motivi di licenza, queste funzioni non possono essere fornite con l'SDK, ma una libreria wrapper che contiene queste funzioni deve essere compilata dagli sviluppatori di applicazioni e fornita con le app che usano l'SDK.
Per compilare questa libreria wrapper, scaricare e installare prima l' [SDK di GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg).
Scaricare quindi il progetto Xcode per la [libreria wrapper](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).
Aprire il progetto in Xcode e compilarlo per la destinazione del **dispositivo iOS generico** . non funzionerà per la compilazione per una destinazione specifica.
L'istruzione di compilazione genererà un bundle di Framework dinamico con una libreria dinamica per tutte le architetture necessarie con il nome `GStreamerWrapper.framework`.
Questo Framework deve essere incluso in tutte le app che usano flussi audio compressi con Speech Services SDK.

Per eseguire questa operazione, applicare le impostazioni seguenti nel progetto Xcode:

1. Copiare il `GStreamerWrapper.framework` appena compilato e il Framework di cognitive Services Speech SDK, che è possibile scaricare da [qui](https://aka.ms/csspeech/iosbinary), alla directory contenente il progetto di esempio.
1. Modificare i percorsi dei Framework nelle *impostazioni del progetto*.
    1. Nella scheda **Generale** sotto l'intestazione **Embedded Binaries** (File binari incorporati) aggiungere la libreria SDK come framework: **Aggiungi file binari incorporati** > **Aggiungi altro...** > passare alla directory scelta e selezionare entrambi i Framework.
    1. Andare alla scheda **Impostazioni di compilazione** e attivare le impostazioni **Tutte**.
1. Aggiungere la directory `$(SRCROOT)/..` ai *Percorsi di ricerca Framework* sotto l'intestazione **Percorsi di ricerca**.

## <a name="example-code-using-codec-compressed-audio-input"></a>Codice di esempio con input audio compresso codec

Per eseguire lo streaming in un formato audio compresso per i servizi di riconoscimento vocale, creare un `SPXPullAudioInputStream` o `SPXPushAudioInputStream`.
Nel frammento di codice seguente viene illustrato come creare un `SPXAudioConfiguration` da un'istanza di un `SPXPushAudioInputStream`, specificando MP3 come formato di compressione del flusso.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

Il frammento di codice successivo Mostra come i dati audio compressi possono essere letti da un file e inseriti nel `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
