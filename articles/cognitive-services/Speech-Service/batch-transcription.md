---
title: Come usare la trascrizione batch - Servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: La trascrizione batch è ideale se si desidera trascrivere una grande quantità di audio in un archivio, ad esempio BLOB di Azure. Usando l'API REST dedicata è possibile puntare ai file audio con un URI di firma di accesso condiviso (SAS) e ricevere trascrizioni in modo asincrono.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 1828cdce66104424cc7845fea89127219e6b77a0
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137273"
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
  "locale": "<locale to us, for example en-US>",
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

Usare queste proprietà facoltative per configurare la trascrizione:

| Parametro | Descrizione |
|-----------|-------------|
| `ProfanityFilterMode` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `none` che disabilita i filtri del contenuto volgare, `masked` che sostituisce il contenuto volgare con gli asterischi, `removed` che rimuove tutto il contenuto volgare dal risultato, o `tags` che aggiunge tag "contenuti volgari". L'impostazione predefinita è `masked`. |
| `PunctuationMode` | Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori accettati sono `none` che consente di disattivare la punteggiatura, `dictated` che implica la punteggiatura esplicita, `automatic` che permette al decodificatore di occuparsi della punteggiatura, o `dictatedandautomatic` che implica segni di punteggiatura dettata o automatica. |
 | `AddWordLevelTimestamps` | Specifica se i timestamp a livello di parola devono essere aggiunti all'output. I valori accettati sono `true`, che abilita i timestamp a livello di parola, e `false` (valore predefinito), che li disabilita. |
 | `AddSentiment` | Specifica dei sentimenti devono essere aggiunte alle utterance. Valori accettati sono `true` che consente di sentimenti per ogni utterance e `false` (valore predefinito) per disabilitarlo. |

### <a name="storage"></a>Archiviazione

Batch supporta di trascrizione [archiviazione Blob di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) per la lettura di audio e trascrizioni di scrittura in un archivio.

## <a name="webhooks"></a>Webhook 

Polling di uno stato di trascrizione potrebbe non essere il più efficiente o fornire la migliore esperienza utente. Per eseguire il polling dello stato, è possibile registrare i callback, che è possibile informare il client al completamento di attività con esecuzione prolungata trascrizione.

Per altre informazioni, vedere [Webhook](webhooks.md).

## <a name="speaker-separation-diarization"></a>Separazione degli altoparlanti (Diarization)

Diarization è il processo di separazione relatori in un frammento di audio. La pipeline di Batch supporta Diarization ed è in grado di riconoscere gli due altoparlanti sulle registrazioni di canale mono.

Per richiedere che la richiesta di trascrizione audio viene elaborata per diarization, è sufficiente aggiungere il parametro in questione nella richiesta HTTP come illustrato di seguito.

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

Timestamp di Word a livello sarebbe inoltre necessario 'attivata' perché indicano i parametri nella richiesta precedente. 

L'audio corrispondente conterrà i relatori principali identificati da un numero (attualmente sono supportate solo due voci, in modo che i relatori principali verranno identificati come ' relatore 1 ' e 'Relatore 2') seguito dall'output trascrizione.

Si noti inoltre che Diarization non è disponibile in registrazioni Stereo. Inoltre, tutti i JSON output conterrà il tag del relatore. Se non viene utilizzato diarization, verrà indicato ' relatore: Null' nell'output JSON.

Di seguito sono elencate le impostazioni locali supportate.

| Linguaggio | locale |
|--------|-------|
| Inglese | en-US |
| Cinese | zh-CN |
| Deutsch | de-DE |

## <a name="sentiment"></a>Valutazione

Sentiment è una nuova funzionalità nell'API di Batch la trascrizione e costituisce un'importante funzionalità del dominio di centro di chiamata. I clienti possono usare il `AddSentiment` parametri alle loro richieste di 

1.  Ottieni informazioni dettagliate sulla soddisfazione dei clienti
2.  Ottieni informazioni dettagliate sulle prestazioni degli agenti (team richiede le chiamate)
3.  Individuare il punto esatto nel tempo quando una chiamata ha avuto un giro in direzione negativa
4.  Individuare la causa anche quando si attiva chiamate negative a positivo
5.  Identificare cosa, come i clienti e quali sono graditi di un prodotto o un servizio

Punteggio del sentiment è per ogni segmento di audio in cui un segmento di audio è definito come lasso di tempo tra l'inizio di utterance (offset) e l'inattività di rilevamento della fine del flusso di byte. L'intero testo all'interno di tale segmento viene utilizzato per il calcolo del sentiment. Non viene calcolare i valori di aggregazione del sentiment per la chiamata intera o del contenuto vocale intero di ogni canale. Queste aggregazioni vengono lasciate al proprietario del dominio per applicare ulteriormente.

Sentiment viene applicato al formato lessicale.

Un esempio di output JSON simile al seguente:

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
La funzionalità Usa un modello del Sentiment, che è attualmente in versione Beta.

## <a name="sample-code"></a>Codice di esempio

Esempi completi sono disponibili nel [repository GitHub di esempio](https://aka.ms/csspeech/samples) all'interno di `samples/batch` sottodirectory.

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

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
