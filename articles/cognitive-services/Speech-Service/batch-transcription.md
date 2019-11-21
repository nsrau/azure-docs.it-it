---
title: How to use Batch Transcription - Speech Service
titleSuffix: Azure Cognitive Services
description: La trascrizione batch è ideale se si desidera trascrivere una grande quantità di audio in un archivio, ad esempio BLOB di Azure. Usando l'API REST dedicata è possibile puntare ai file audio con un URI di firma di accesso condiviso (SAS) e ricevere trascrizioni in modo asincrono.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 5418b378c2c3cff09dbccbaa7b7240c61bbb583e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221521"
---
# <a name="why-use-batch-transcription"></a>Perché usare la trascrizione batch?

La trascrizione batch è ideale se si desidera trascrivere una grande quantità di audio in un archivio, ad esempio BLOB di Azure. Usando l'API REST dedicata è possibile puntare ai file audio con un URI di firma di accesso condiviso (SAS) e ricevere trascrizioni in modo asincrono.

## <a name="prerequisites"></a>Prerequisiti

### <a name="subscription-key"></a>Chiave di sottoscrizione

Come per tutte le funzionalità del servizio Voce, si crea una chiave di sottoscrizione dal [portale di Azure](https://portal.azure.com) seguendo la [guida introduttiva](get-started.md). Se si prevede di ottenere trascrizioni dai modelli di base, la creazione di una chiave è l'unica operazione da eseguire.

>[!NOTE]
> Per usare la trascrizione batch è necessaria una sottoscrizione standard (S0) per i servizi di riconoscimento vocale. Le chiavi di sottoscrizione gratuita (F0) non funzioneranno. Per altre informazioni, vedere [prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelli personalizzati

Se si prevede di personalizzare modelli acustici o linguistici, seguire le procedure descritte in [Esercitazione: Creare un modello acustico personalizzato](how-to-customize-acoustic-models.md) e [Esercitazione: Creare un modello linguistico personalizzato](how-to-customize-language-model.md). Per usare i modelli creati in una trascrizione batch, è necessario disporre dei relativi ID modello. Non si tratta dell'ID di endpoint che si trova nella visualizzazione Dettagli endpoint, ma dell'ID del modello che è possibile recuperare selezionando i dettagli del modello.

## <a name="the-batch-transcription-api"></a>API di trascrizione batch

L'API di trascrizione batch offre la trascrizione asincrona della voce in testo scritto insieme ad altre funzionalità. Si tratta di un'API REST che espone metodi per eseguire le operazioni seguenti:

1. Creare richieste di elaborazione batch
1. Eseguire query sullo stato
1. Scaricare le trascrizioni

> [!NOTE]
> L'API di trascrizione batch è ideale per i call center che in genere accumulano migliaia di ore di contenuto audio. Facilita la trascrizione di grandi quantità di registrazioni audio.

### <a name="supported-formats"></a>Formati supportati

L'API di trascrizione batch supporta i formati seguenti:

| Format | Codec | Bitrate | Frequenza di campionamento |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bit | 8 o 16 kHz, mono, stereo |
| MP3 | PCM | 16 bit | 8 o 16 kHz, mono, stereo |
| OGG | OPUS | 16 bit | 8 o 16 kHz, mono, stereo |

Per i flussi audio stereo, l'API di trascrizione batch divide i canali sinistro e destro durante la trascrizione. I due file JSON con il risultato vengono creati ognuno da un singolo canale. I timestamp per espressione consentono allo sviluppatore di creare una trascrizione finale ordinata. Questa richiesta di esempio include proprietà per il filtro di contenuti volgari, la punteggiatura e i timestamp a livello di parola.

### <a name="configuration"></a>Configurazione

I parametri di configurazione vengono forniti in formato JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> L'API di trascrizione batch usa un servizio REST per richiedere le trascrizioni, il relativo stato e i risultati associati. È possibile usare l'API da qualsiasi linguaggio. La sezione seguente descrive come viene usata l'API.

### <a name="configuration-properties"></a>Proprietà di configurazione

Use these optional properties to configure transcription:

| Parametro | Description |
|-----------|-------------|
| `ProfanityFilterMode` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `None` che disabilita i filtri del contenuto volgare, `masked` che sostituisce il contenuto volgare con gli asterischi, `removed` che rimuove tutto il contenuto volgare dal risultato, o `tags` che aggiunge tag "contenuti volgari". L'impostazione predefinita è `masked`. |
| `PunctuationMode` | Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori accettati sono `None` che consente di disattivare la punteggiatura, `dictated` che implica la punteggiatura esplicita, `automatic` che permette al decodificatore di occuparsi della punteggiatura, o `dictatedandautomatic` che implica segni di punteggiatura dettata o automatica. |
 | `AddWordLevelTimestamps` | Specifica se i timestamp a livello di parola devono essere aggiunti all'output. I valori accettati sono `true`, che abilita i timestamp a livello di parola, e `false` (valore predefinito), che li disabilita. |
 | `AddSentiment` | Specifies sentiment should be added to the utterance. Accepted values are `true` which enables sentiment per utterance and `false` (the default value) to disable it. |
 | `AddDiarization` | Specifies that diarization analysis should be carried out on the input which is expected to be mono channel containing two voices. Accepted values are `true` which enables diarization and `false` (the default value) to disable it. It also requires `AddWordLevelTimestamps` to be set to true.|

### <a name="storage"></a>Archiviazione

Batch transcription supports [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) for reading audio and writing transcriptions to storage.

## <a name="webhooks"></a>Webhook

Polling for transcription status may not be the most performant, or provide the best user experience. To poll for status, you can register callbacks, which will notify the client when long-running transcription tasks have completed.

For more details, see [Webhooks](webhooks.md).

## <a name="speaker-separation-diarization"></a>Speaker Separation (Diarization)

Diarization is the process of separating speakers in a piece of audio. Our Batch pipeline supports Diarization and is capable of recognizing two speakers on mono channel recordings.

To request that your audio transcription request is processed for diarization, you simply have to add the relevant parameter in the HTTP request as shown below.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Word level timestamps would also have to be 'turned on' as the parameters in the above request indicate.

The corresponding audio will contain the speakers identified by a number (currently we support only two voices, so the speakers will be identified as 'Speaker 1 'and 'Speaker 2') followed by the transcription output.

Also note that Diarization is not available in Stereo recordings. Furthermore, all JSON output will contain the Speaker tag. If diarization is not used, it will show 'Speaker: Null' in the JSON output.

> [!NOTE]
> Diarization is available in all regions and for all locales!

## <a name="sentiment"></a>Sentiment

Sentiment is a new feature in Batch Transcription API and is an important feature in the call center domain. Customers can use the `AddSentiment` parameters to their requests to

1.  Get insights on customer satisfaction
2.  Get insight on the performance of the agents (team taking the calls)
3.  Pinpoint the exact point in time when a call took a turn in a negative direction
4.  Pinpoint what went well when turning negative calls to positive
5.  Identify what customers like and what they dislike about a product or a service

Sentiment is scored per audio segment where an audio segment is defined as the time lapse between the start of the utterance (offset) and the detection silence of end of byte stream. The entire text within that segment is used to calculate sentiment. We DO NOT calculate any aggregate sentiment values for the entire call or the entire speech of each channel. These aggregations are left to the domain owner to further apply.

Sentiment is applied on the lexical form.

A JSON output sample looks like below:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
The feature uses a Sentiment model, which is currently in Beta.

## <a name="sample-code"></a>Codice di esempio

Complete samples are available in the [GitHub sample repository](https://aka.ms/csspeech/samples) inside the `samples/batch` subdirectory.

È necessario personalizzare il codice di esempio con le informazioni della sottoscrizione, l'area di servizio, l'URI di firma di accesso condiviso che punta al file audio da trascrivere e gli ID di modello in caso si voglia usare un modello acustico o linguistico personalizzato.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Il codice di esempio configura il client e invia la richiesta di trascrizione. Esegue quindi il polling delle informazioni sullo stato e stampa i dettagli relativi allo stato di avanzamento della trascrizione.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Per informazioni dettagliate sulle chiamate precedenti, vedere il [documento di Swagger](https://westus.cris.ai/swagger/ui/index). L'esempio completo illustrato qui è disponibile in [GitHub](https://aka.ms/csspeech/samples), nella sottodirectory `samples/batch`.

Prendere nota della configurazione asincrona per l'inserimento dell'audio e la ricezione dello stato della trascrizione. Viene creato un client HTTP .NET. Il metodo `PostTranscriptions` consente di inviare i dettagli del file audio e il metodo `GetTranscriptions` consente di ricevere i risultati. `PostTranscriptions` restituisce un handle e `GetTranscriptions` usa l'handle per crearne uno nuovo per ottenere lo stato della trascrizione.

Il codice di esempio corrente non specifica un modello personalizzato. Per la trascrizione dei file, il servizio userà i modelli di base. Per specificare i modelli, è possibile passare nello stesso metodo gli ID per il modello acustico e per quello linguistico.

> [!NOTE]
> Per le trascrizioni di base non è necessario dichiarare l'ID dei modelli di base. Se si specifica solo l'ID di un modello linguistico (e non un ID di modello acustico), viene automaticamente selezionato un modello acustico corrispondente. Se si specifica solo l'ID di un modello acustico, viene automaticamente selezionato un modello linguistico corrispondente.

## <a name="download-the-sample"></a>Scaricare l'esempio

L'esempio è disponibile nella directory `samples/batch` nel [repository di esempi GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> I processi di trascrizione batch vengono pianificati con il criterio del massimo sforzo, senza alcuna stima del momento in cui un processo passerà allo stato di esecuzione. Una volta nello stato di esecuzione, la trascrizione effettiva viene elaborata più velocemente dell'audio in tempo reale.

## <a name="next-steps"></a>Passaggi successivi

* [Accedere alla versione di prova del servizio Voce](https://azure.microsoft.com/try/cognitive-services/)
