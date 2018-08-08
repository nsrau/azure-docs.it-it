---
title: Concetti relativi ad AudioInputStream
description: Panoramica delle funzionalità dell'API AudioInputStream.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: b3e12fbc616c8d67b557102c6094467e119a23f1
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281906"
---
# <a name="about-the-audio-input-stream-api"></a>Informazioni sull’API del flusso di input audio

L'API del **flusso di input audio** fornisce un modo per eseguire lo streaming dei flussi audio nei riconoscitori invece di utilizzare il microfono o le API del file di input.

## <a name="api-overview"></a>Panoramica delle API

L'API utilizza due componenti, `AudioInputStream` (i dati audio non elaborati) e `AudioInputStreamFormat`.

`AudioInputStreamFormat` definisce il formato dei dati audio. Può essere confrontato con la struttura `WAVEFORMAT` standard per i file wave in Windows.

  - `FormatTag`

    Il formato dell'audio. Speech SDK supporta attualmente solo `format 1` (PCM - little-endian).

  - `Channels`

    Il numero di canali. L'attuale servizio di riconoscimento vocale supporta materiale audio di un solo canale (mono).

  - `SamplesPerSec`

    La frequenza di campionamento. Una registrazione tipica dal microfono ha 16000 campioni al secondo.

  - `AvgBytesPerSec`

    Media byte per secondo, calcolata come `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. I byte medi al secondo possono essere diversi per i flussi audio che utilizzano bitrate variabili.

  - `BlockAlign`

    Le dimensioni di un singolo frame, calcolate come `Channels * ceil(wBitsPerSample, 8)`. A causa della spaziatura interna, il valore effettivo potrebbe essere maggiore di questo valore.

  - `BitsPerSample`

    Bit per campione. Un tipico flusso audio utilizza 16 bit per campione (qualità CD).

La classe di base `AudioInputStream` sarà sovrascritta dall'adattatore di flusso personalizzato. Questo adattatore deve implementare queste funzioni:

   - `GetFormat()`

     Questa funzione viene richiamata per ottenere il formato del flusso audio. Ottiene un puntatore al buffer di AudioInputStreamFormat.

   - `Read()`

     Questa funzione viene richiamata per ottenere i dati dal flusso audio. Un parametro è un puntatore al buffer in cui copiare i dati audio. Il secondo parametro è la dimensione del buffer. La funzione restituisce il numero di byte copiati nel buffer. Il valore restituito di `0` indica la fine del flusso.

   - `Close()`

     Questa funzione viene richiamata per chiudere il flusso audio.

## <a name="usage-examples"></a>Esempi di uso

In generale, quando si utilizzano flussi di ingresso audio, sono necessari i passaggi riportati di seguito:

  - Identificare il formato del flusso audio. Il formato deve essere supportato da SDK e dal servizio di riconoscimento vocale. Attualmente sono supportate le seguenti configurazioni:

    Un tag di formato audio (PCM), un canale, 16000 campioni al secondo, 32000 byte al secondo, due allineamenti in blocco (16 bit inclusa la spaziatura interna per un campione), 16 bit per campione

  - Assicurarsi che il codice possa fornire i dati audio RAW relativi alle specifiche sopra identificate. Se i dati dell’origine audio non corrispondono ai formati supportati, l'audio deve essere transcodificato nel formato richiesto.

  - Derivare la classe di flusso di input audio personalizzato da `AudioInputStream`. Implementare l’operazione `GetFormat()`, `Read()` e `Close()`. La firma esatta della funzione dipende dalla lingua, ma il codice sarà simile a questo esempio di codice:

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - Utilizzare il flusso di input audio:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - In alcune lingue `contosoStream` deve essere eliminato esplicitamente al termine il riconoscimento. Non è possibile rilasciare AudioStream prima che venga letto l'input completo. In uno scenario che utilizza `StopContinuousRecognitionAsync` e `StopContinuousRecognitionAsync`, richiede un concetto illustrato in questo esempio:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sul riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
