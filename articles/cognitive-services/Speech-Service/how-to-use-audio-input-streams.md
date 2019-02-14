---
title: Concetti relativi al flusso di input audio di Speech SDK
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità dell'API del flusso di input audio di Speech SDK.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: f34504dddaa21cc162476d1fdb462e7be5c48629
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877304"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Informazioni sull'API del flusso di input audio di Speech SDK

L'API del **flusso di input audio** di Speech SDK fornisce un modo per eseguire lo streaming dei flussi audio nei sistemi di riconoscimento invece di usare il microfono o le API del file di input.

Quando si usano flussi di input audio, sono necessari i passaggi riportati di seguito:

- Identificare il formato del flusso audio. Il formato deve essere supportato da Speech SDK e dal servizio Voce. Attualmente sono supportate solo le seguenti configurazioni:

  Campioni audio in formato PCM, un canale, 16000 campioni al secondo, 32000 byte al secondo, due allineamenti in blocco (16 bit inclusa la spaziatura interna per un campione), 16 bit per campione.

  Il codice corrispondente nell'SDK per creare il formato audio sarà simile al seguente:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Assicurarsi che il codice possa fornire i dati audio RAW in base a queste specifiche. Se i dati dell’origine audio non corrispondono ai formati supportati, l'audio deve essere transcodificato nel formato richiesto.

- Creare la classe di flusso di input audio personalizzato da `PullAudioInputStreamCallback`. Implementare i membri `Read()` e `Close()`. La firma esatta della funzione dipende dalla lingua, ma il codice sarà simile a questo esempio di codice:

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Creare una configurazione audio in base al formato audio e al flusso di input. Quando si crea il sistema di riconoscimento, passare sia la configurazione del riconoscimento vocale normale che la configurazione dell'input audio. Ad esempio: 

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
