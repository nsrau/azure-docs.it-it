---
title: Come usare il servizio di traduzione vocale di batch
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
ms.openlocfilehash: 158a99b1691e59fa58207f3c9291ca9d37a6679c
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538123"
---
# <a name="why-use-batch-transcription"></a>Perché usare la trascrizione batch?

La trascrizione batch è ideale se si desidera trascrivere una grande quantità di audio in un archivio, ad esempio BLOB di Azure. Usando l'API REST dedicata è possibile puntare ai file audio con un URI di firma di accesso condiviso (SAS) e ricevere trascrizioni in modo asincrono.

## <a name="prerequisites"></a>prerequisiti

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

Usare queste proprietà facoltative per configurare la trascrizione:

| . | DESCRIZIONE |
|-----------|-------------|
| `ProfanityFilterMode` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `None` che disabilita i filtri del contenuto volgare, `masked` che sostituisce il contenuto volgare con gli asterischi, `removed` che rimuove tutto il contenuto volgare dal risultato, o `tags` che aggiunge tag "contenuti volgari". L'impostazione predefinita è `masked`. |
| `PunctuationMode` | Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori accettati sono `None` che consente di disattivare la punteggiatura, `dictated` che implica la punteggiatura esplicita, `automatic` che permette al decodificatore di occuparsi della punteggiatura, o `dictatedandautomatic` che implica segni di punteggiatura dettata o automatica. |
 | `AddWordLevelTimestamps` | Specifica se i timestamp a livello di parola devono essere aggiunti all'output. I valori accettati sono `true`, che abilita i timestamp a livello di parola, e `false` (valore predefinito), che li disabilita. |
 | `AddSentiment` | Specifica che il sentimento deve essere aggiunto all'espressione. I valori accettati sono `true` che Abilita il sentimento per enunciato e `false` (valore predefinito) per disabilitarlo. |
 | `AddDiarization` | Specifica che l'analisi della fase di esecuzione deve essere eseguita nell'input che dovrebbe essere canale mono contenente due voci. I valori accettati sono `true` che consentono di eseguire la `false` (valore predefinito) e di disabilitarlo. Richiede anche che `AddWordLevelTimestamps` sia impostato su true.|

### <a name="storage"></a>Archiviazione

La trascrizione batch supporta l' [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) per la lettura di audio e la scrittura di trascrizioni nell'archiviazione.

## <a name="speaker-separation-diarization"></a>Separazione dei relatori

La deframmentazione è il processo di separazione degli altoparlanti in un audio. La pipeline batch supporta la registrazione ed è in grado di riconoscere due altoparlanti nelle registrazioni del canale mono.

Per richiedere che la richiesta di trascrizione audio venga elaborata per la diaria, è sufficiente aggiungere il parametro pertinente nella richiesta HTTP, come illustrato di seguito.

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

È necessario che i timestamp a livello di parola siano anch ' essi attivati perché i parametri nella richiesta precedente indicano. 

L'audio corrispondente conterrà gli altoparlanti identificati da un numero (attualmente sono supportate solo due voci, quindi gli altoparlanti verranno identificati come ' Speaker 1' è Speaker 2') seguiti dall'output della trascrizione.

Si noti inoltre che la registrazione non è disponibile nelle registrazioni stereo. Inoltre, tutto l'output JSON conterrà il tag del relatore. Se la non viene usata, verrà visualizzata la voce ' Speaker: null ' nell'output JSON.

> [!NOTE]
> La predisposizione è disponibile in tutte le aree geografiche e per tutte le impostazioni locali.

## <a name="sentiment"></a>Valutazione

Il sentimento è una nuova funzionalità dell'API di trascrizione di batch ed è una funzionalità importante del dominio del Call Center. I clienti possono usare i parametri di `AddSentiment` per le richieste a

1.  Ottieni informazioni dettagliate sulla soddisfazione dei clienti
2.  Ottenere informazioni dettagliate sulle prestazioni degli agenti (team che effettua le chiamate)
3.  Individuare il punto nel tempo esatto in cui una chiamata ha avuto un turno in una direzione negativa
4.  Individuare le prestazioni ottimali quando si attivano le chiamate negative a positive
5.  Identificare i clienti che desiderano e cosa non amano per un prodotto o un servizio

Il Punteggio viene valutato per segmento audio in cui un segmento audio viene definito come intervallo di tempo tra l'inizio dell'espressione (offset) e il silenzio di rilevamento della fine del flusso di byte. L'intero testo all'interno di tale segmento viene usato per calcolare il sentimento. NON vengono calcolati i valori di valutazione aggregati per l'intera chiamata o l'intero riconoscimento vocale di ogni canale. Queste aggregazioni vengono lasciate al proprietario del dominio per essere applicate ulteriormente.

Il sentimento viene applicato al formato lessicale.

Un esempio di output JSON ha un aspetto simile al seguente:

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
La funzionalità Usa un modello di valutazione, che è attualmente in versione beta.

## <a name="sample-code"></a>Codice di esempio

Gli esempi completi sono disponibili nel [repository di esempio GitHub](https://aka.ms/csspeech/samples) all'interno della sottodirectory `samples/batch`.

È necessario personalizzare il codice di esempio con le informazioni della sottoscrizione, l'area di servizio, l'URI di firma di accesso condiviso che punta al file audio da trascrivere e gli ID di modello in caso si voglia usare un modello acustico o linguistico personalizzato.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Il codice di esempio configurerà il client e invierà la richiesta di trascrizione. Esegue quindi il polling delle informazioni sullo stato e stampa i dettagli relativi allo stato di avanzamento della trascrizione.

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
