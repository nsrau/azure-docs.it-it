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
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: 8a53f1cfbde2f518848e7ef1104bf41ba4996961
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936399"
---
# <a name="how-to-use-batch-transcription"></a>Come usare la trascrizione batch

La trascrizione batch è ideale per la trascrizione di una grande quantità di audio nell'archiviazione. Usando l'API REST dedicata, è possibile puntare a file audio con un URI di firma di accesso condiviso e ricevere in modo asincrono i risultati della trascrizione.

L'API offre trascrizioni asincrone di sintesi vocale e altre funzionalità. È possibile usare l'API REST per esporre i metodi per:

- Creare richieste di elaborazione batch
- Eseguire una query sullo stato
- Scaricare i risultati della trascrizione
- Elimina le informazioni di trascrizione dal servizio

L'API dettagliata è disponibile come [documento Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A) sotto l'intestazione `Custom Speech transcriptions`.

I processi di trascrizione batch sono pianificati in base al massimo sforzo. Attualmente non è prevista alcuna stima per il momento in cui un processo cambierà in stato di esecuzione. Con il normale carico di sistema, il processo dovrebbe avvenire entro pochi minuti. Una volta nello stato di esecuzione, la trascrizione effettiva viene elaborata più velocemente dell'audio in tempo reale.

Accanto all'API di facile utilizzo, non è necessario distribuire endpoint personalizzati e non sono previsti requisiti di concorrenza da osservare.

## <a name="prerequisites"></a>Prerequisiti

### <a name="subscription-key"></a>Chiave di sottoscrizione

Come per tutte le funzionalità del servizio Voce, si crea una chiave di sottoscrizione dal [portale di Azure](https://portal.azure.com) seguendo la [guida introduttiva](get-started.md).

>[!NOTE]
> Per usare la trascrizione batch, è necessaria una sottoscrizione standard (S0) per il servizio di riconoscimento vocale. Le chiavi di sottoscrizione gratuita (F0) non funzioneranno. Per altre informazioni, vedere [prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelli personalizzati

Se si prevede di personalizzare i modelli acustici o di lingua, seguire i passaggi descritti in [personalizzare modelli acustici](how-to-customize-acoustic-models.md) e [progettare modelli di linguaggio di personalizzazione](how-to-customize-language-model.md). Per usare i modelli creati nella trascrizione batch, sono necessari gli ID del modello. È possibile recuperare l'ID modello quando si esaminano i dettagli del modello. Un endpoint personalizzato distribuito non è necessario per il servizio di trascrizione batch.

## <a name="the-batch-transcription-api"></a>API di trascrizione batch

### <a name="supported-formats"></a>Formati supportati

L'API di trascrizione batch supporta i formati seguenti:

| Format | Codec | Bitrate | Frequenza di campionamento |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bit | 8 kHz o 16 kHz, mono o stereo |
| MP3 | PCM | 16 bit | 8 kHz o 16 kHz, mono o stereo |
| OGG | OPUS | 16 bit | 8 kHz o 16 kHz, mono o stereo |

Per i flussi audio stereo, i canali sinistro e destro vengono suddivisi durante la trascrizione. Per ogni canale viene creato un file di risultati JSON. I timestamp generati per espressione consentono allo sviluppatore di creare una trascrizione finale ordinata.

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
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Proprietà di configurazione

Usare queste proprietà facoltative per configurare la trascrizione:

| Parametro | Description |
|-----------|-------------|
| `ProfanityFilterMode` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `None` che disabilita i filtri del contenuto volgare, `Masked` che sostituisce il contenuto volgare con gli asterischi, `Removed` che rimuove tutto il contenuto volgare dal risultato, o `Tags` che aggiunge tag "contenuti volgari". L'impostazione predefinita è `Masked`. |
| `PunctuationMode` | Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori accettati sono `None` che consente di disattivare la punteggiatura, `Dictated` che implica la punteggiatura esplicita, `Automatic` che permette al decodificatore di occuparsi della punteggiatura, o `DictatedAndAutomatic` che implica segni di punteggiatura dettata o automatica. |
| `AddWordLevelTimestamps` | Specifica se i timestamp a livello di parola devono essere aggiunti all'output. I valori accettati sono `true`, che abilita i timestamp a livello di parola, e `false` (valore predefinito), che li disabilita. |
| `AddSentiment` | Specifica che il sentimento deve essere aggiunto all'espressione. I valori accettati sono `true` che Abilita il sentimento per enunciato e `false` (valore predefinito) per disabilitarlo. |
| `AddDiarization` | Specifica che l'analisi della fase di esecuzione deve essere eseguita nell'input che dovrebbe essere canale mono contenente due voci. I valori accettati sono `true` che consentono di eseguire la `false` (valore predefinito) e di disabilitarlo. Richiede anche che `AddWordLevelTimestamps` sia impostato su true.|
|`TranscriptionResultsContainerUrl`|URL facoltativo con firma di accesso condiviso del [servizio](../../storage/common/storage-sas-overview.md) per un contenitore scrivibile in Azure. Il risultato verrà archiviato in questo contenitore.

### <a name="storage"></a>Archiviazione

La trascrizione batch supporta l' [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) per la lettura di audio e la scrittura di trascrizioni nell'archiviazione.

## <a name="the-batch-transcription-result"></a>Risultato della trascrizione batch

Per l'audio di input mono, viene creato un file di risultati della trascrizione. Per audio di input stereo, vengono creati due file di risultati della trascrizione. Ogni ha la struttura seguente:

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in milliseconds'
          "Duration": number                                'time in milliseconds'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in milliseconds'
                  "Duration": number                        'time in milliseconds'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Il risultato contiene i seguenti formati:

|Form|Contenuto|
|-|-|
|`Lexical`|Parole effettive riconosciute.
|`ITN`|Formato inverso-testo normalizzato del testo riconosciuto. Vengono applicate le abbreviazioni ("Doctor Smith" a "Dr Smith"), i numeri di telefono e altre trasformazioni.
|`MaskedITN`|Il form ITN con la maschera volgare applicata.
|`Display`|Il form di visualizzazione del testo riconosciuto. Sono incluse la punteggiatura aggiuntiva e la maiuscola.

## <a name="speaker-separation-diarization"></a>Separazione dei relatori

La deframmentazione è il processo di separazione degli altoparlanti in un audio. La pipeline batch supporta la registrazione ed è in grado di riconoscere due altoparlanti nelle registrazioni del canale mono. La funzionalità non è disponibile nelle registrazioni stereo.

L'output di tutte le trascrizioni contiene un `SpeakerId`. Se la non viene usata, verrà visualizzata `"SpeakerId": null` nell'output JSON. Per la tua operazione sono supportate due voci, quindi i relatori verranno identificati come `"1"` o `"2"`.

Per richiedere la predisposizione, è sufficiente aggiungere il parametro pertinente nella richiesta HTTP, come illustrato di seguito.

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

## <a name="sentiment-analysis"></a>Analisi dei sentimenti

La caratteristica relativa ai sentimenti stima il sentimento espresso nell'audio. Il sentimento è espresso da un valore compreso tra 0 e 1 per `Negative`, `Neutral`e `Positive` sentimenti. Ad esempio, è possibile usare l'analisi dei sentimenti negli scenari del Call Center:

- Ottieni informazioni dettagliate sulla soddisfazione dei clienti
- Ottenere informazioni dettagliate sulle prestazioni degli agenti (team che effettua le chiamate)
- Trovare il punto nel tempo esatto in cui una chiamata ha avuto un turno in una direzione negativa
- Cosa è andato bene quando si gira una chiamata negativa in una direzione positiva
- Identificare i clienti che desiderano e cosa non amano per un prodotto o un servizio

Il Punteggio viene valutato per segmento audio in base al formato lessicale. L'intero testo all'interno del segmento audio viene usato per calcolare il sentimento. Non viene calcolato alcun sentimento aggregato per l'intera trascrizione.

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

## <a name="best-practices"></a>Procedure consigliate

Il servizio di trascrizione può gestire un numero elevato di trascrizioni inviate. È possibile eseguire una query sullo stato delle trascrizioni tramite un `GET` nel [Metodo trascrizioni](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Tenere le informazioni restituite a una dimensione ragionevole specificando il parametro `take` (alcune centinaia). [Eliminare le trascrizioni](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regolarmente dal servizio una volta recuperati i risultati. In questo modo si garantiscono risposte rapide dalle chiamate di gestione della trascrizione.

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

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
