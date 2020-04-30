---
title: Che cos'è la trascrizione in batch-servizio vocale
titleSuffix: Azure Cognitive Services
description: La trascrizione batch è ideale se si desidera trascrivere una grande quantità di audio in un archivio, ad esempio BLOB di Azure. Usando l'API REST dedicata è possibile puntare ai file audio con un URI di firma di accesso condiviso (SAS) e ricevere trascrizioni in modo asincrono.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208619"
---
# <a name="what-is-batch-transcription"></a>Che cos'è la trascrizione batch?

La trascrizione batch è un set di operazioni API REST che consente di trascrivere una grande quantità di audio nell'archivio. È possibile puntare a file audio con un URI di firma di accesso condiviso (SAS) e ricevere in modo asincrono i risultati della trascrizione.

La trascrizione asincrona di sintesi vocale è solo una delle funzionalità. È possibile usare le API REST per la trascrizione batch per chiamare i metodi seguenti:



|    Operazione di trascrizione batch                                             |    Metodo    |    Chiamata API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Crea una nuova trascrizione.                                              |    POST      |    API/speechtotext/v 2.0/trascrizioni            |
|    Recupera un elenco di trascrizioni per la sottoscrizione autenticata.    |    GET       |    API/speechtotext/v 2.0/trascrizioni            |
|    Ottiene un elenco di impostazioni locali supportate per le trascrizioni offline.              |    GET       |    API/speechtotext/v 2.0/trascrizioni/impostazioni locali    |
|    Aggiorna i dettagli modificabili della trascrizione identificata dal relativo ID.    |    PATCH     |    API/speechtotext/v 2.0/trascrizioni/{ID}       |
|    Elimina l'attività di trascrizione specificata.                                 |    DELETE    |    API/speechtotext/v 2.0/trascrizioni/{ID}       |
|    Ottiene la trascrizione identificata dall'ID specificato.                        |    GET       |    API/speechtotext/v 2.0/trascrizioni/{ID}       |




È possibile esaminare e testare l'API dettagliata, disponibile come [documento di spavalderia](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), sotto l'intestazione `Custom Speech transcriptions`.

I processi di trascrizione batch sono pianificati in base al massimo sforzo. Attualmente non è prevista alcuna stima per il momento in cui un processo viene modificato in stato di esecuzione. Con il normale carico di sistema, il processo dovrebbe avvenire entro pochi minuti. Una volta nello stato di esecuzione, la trascrizione effettiva viene elaborata più velocemente dell'audio in tempo reale.

Accanto all'API di facile utilizzo, non è necessario distribuire endpoint personalizzati e non sono previsti requisiti di concorrenza da osservare.

## <a name="prerequisites"></a>Prerequisiti

### <a name="subscription-key"></a>Chiave di sottoscrizione

Come per tutte le funzionalità del servizio Voce, si crea una chiave di sottoscrizione dal [portale di Azure](https://portal.azure.com) seguendo la [guida introduttiva](get-started.md).

>[!NOTE]
> Per usare la trascrizione batch, è necessaria una sottoscrizione standard (S0) per il servizio di riconoscimento vocale. Le chiavi di sottoscrizione gratuite (F0) non funzionano. Per altre informazioni, vedere [prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelli personalizzati

Se si prevede di personalizzare i modelli acustici o di lingua, seguire i passaggi descritti in [personalizzare modelli acustici](how-to-customize-acoustic-models.md) e [progettare modelli di linguaggio di personalizzazione](how-to-customize-language-model.md). Per usare i modelli creati nella trascrizione batch, sono necessari gli ID del modello. È possibile recuperare l'ID modello quando si esaminano i dettagli del modello. Un endpoint personalizzato distribuito non è necessario per il servizio di trascrizione batch.

## <a name="the-batch-transcription-api"></a>API di trascrizione batch

### <a name="supported-formats"></a>Formati supportati

L'API di trascrizione batch supporta i formati seguenti:

| Format | Codec | Bitrate | Frequenza di campionamento                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bit  | 8 kHz o 16 kHz, mono o stereo |
| MP3    | PCM   | 16 bit  | 8 kHz o 16 kHz, mono o stereo |
| OGG    | OPUS  | 16 bit  | 8 kHz o 16 kHz, mono o stereo |

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

:::row:::
   :::column span="1":::
      **Parametro**
   :::column-end:::
   :::column span="2":::
      **Descrizione**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. `None` I valori accettati sono la disabilitazione del `Masked` filtro per la volgarità, la sostituzione `Removed` della volgarità con gli asterischi, la rimozione di `Tags` tutti i messaggi profani dal risultato o l'aggiunta di tag di "volgarità". L'impostazione predefinita è `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori accettati `None` sono la disabilitazione `Dictated` della punteggiatura, per implicare la punteggiatura esplicita (pronunciata), `Automatic` per consentire `DictatedAndAutomatic` al decodificatore di gestire la punteggiatura o di usare la punteggiatura dettata e automatica. L'impostazione predefinita è `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Specifica se i timestamp a livello di parola devono essere aggiunti all'output. I valori accettati sono `true` l'abilitazione dei timestamp `false` a livello di parola e (valore predefinito) per disabilitarla.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Specifica se l'analisi dei sentimenti deve essere applicata all'espressione. I valori accettati sono `true` abilitare `false` e (valore predefinito) per disabilitarlo. Per informazioni dettagliate, vedere [analisi del sentiment](#sentiment-analysis) .
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Specifica che l'analisi della fase di esecuzione deve essere eseguita nell'input, che dovrebbe essere canale mono contenente due voci. I valori accettati sono `true` l'abilitazione della diaria e `false` (valore predefinito) per disabilitarla. È anche necessario `AddWordLevelTimestamps` che sia impostato su true.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL facoltativo con firma di accesso condiviso del [servizio](../../storage/common/storage-sas-overview.md) per un contenitore scrivibile in Azure. Il risultato viene archiviato in questo contenitore.
:::row-end:::

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
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
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
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
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

:::row:::
   :::column span="1":::
      **Form**
   :::column-end:::
   :::column span="2":::
      **Contenuto**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      Parole effettive riconosciute.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Formato inverso-testo normalizzato del testo riconosciuto. Vengono applicate le abbreviazioni ("Doctor Smith" a "Dr Smith"), i numeri di telefono e altre trasformazioni.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      Il form ITN con la maschera volgare applicata.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      Il form di visualizzazione del testo riconosciuto. Sono inclusi la punteggiatura e le maiuscole aggiunte.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separazione dei relatori

La deframmentazione è il processo di separazione degli altoparlanti in un audio. La pipeline batch supporta la registrazione ed è in grado di riconoscere due altoparlanti nelle registrazioni del canale mono. La funzionalità non è disponibile nelle registrazioni stereo.

L'output di `SpeakerId`tutte le trascrizioni contiene. Se la non viene usata, viene visualizzata `"SpeakerId": null` nell'output JSON. Per la tua operazione sono supportate due voci, quindi i relatori `"1"` sono `"2"`identificati come o.

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

## <a name="sentiment-analysis"></a>Analisi del sentiment

La caratteristica relativa ai sentimenti stima il sentimento espresso nell'audio. Il sentimento è espresso da un valore compreso tra 0 e 1 `Negative`per `Neutral`i sentimenti `Positive` , e. Ad esempio, è possibile usare l'analisi dei sentimenti negli scenari del Call Center:

- Ottieni informazioni dettagliate sulla soddisfazione dei clienti
- Ottenere informazioni dettagliate sulle prestazioni degli agenti (team che effettua le chiamate)
- Trovare il punto nel tempo esatto in cui una chiamata ha avuto un turno in una direzione negativa
- Cosa è andato bene quando si gira una chiamata negativa in una direzione positiva
- Identificare i clienti che desiderano e cosa non amano per un prodotto o un servizio

Il Punteggio viene valutato per segmento audio in base al formato lessicale. L'intero testo all'interno del segmento audio viene usato per calcolare il sentimento. Non viene calcolato alcun sentimento aggregato per l'intera trascrizione. L'analisi dei sentimenti è attualmente disponibile solo in lingua inglese.

> [!NOTE]
> Si consiglia invece di usare Microsoft API Analisi del testo. Offre funzionalità più avanzate oltre all'analisi dei sentimenti, ad esempio l'estrazione di frasi chiave, il rilevamento automatico della lingua e altro ancora. È possibile trovare informazioni ed esempi nella [documentazione di analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/).
>

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

Il servizio di trascrizione può gestire un numero elevato di trascrizioni inviate. È possibile eseguire una query sullo stato delle trascrizioni tramite `GET` un oggetto nel [Metodo trascrizioni](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Tenere le informazioni restituite a una dimensione ragionevole specificando `take` il parametro (alcune centinaia). [Eliminare le trascrizioni](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regolarmente dal servizio una volta recuperati i risultati. Ciò garantisce risposte rapide dalle chiamate di gestione della trascrizione.

## <a name="sample-code"></a>Codice di esempio

Gli esempi completi sono disponibili nel [repository di esempio GitHub](https://aka.ms/csspeech/samples) all' `samples/batch` interno della sottodirectory.

È necessario personalizzare il codice di esempio con le informazioni della sottoscrizione, l'area di servizio, l'URI di firma di accesso condiviso che punta al file audio da trascrivere e gli ID di modello in caso si voglia usare un modello acustico o linguistico personalizzato.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Il codice di esempio configura il client e Invia la richiesta di trascrizione. Esegue quindi il polling delle informazioni sullo stato e i dettagli di stampa sullo stato della trascrizione.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Per dettagli completi sulle chiamate precedenti, vedere il [documento di spavalderia](https://westus.cris.ai/swagger/ui/index). L'esempio completo illustrato qui è disponibile in [GitHub](https://aka.ms/csspeech/samples), nella sottodirectory `samples/batch`.

Prendere nota della configurazione asincrona per l'inserimento dell'audio e la ricezione dello stato della trascrizione. Viene creato un client HTTP .NET. Il metodo `PostTranscriptions` consente di inviare i dettagli del file audio e il metodo `GetTranscriptions` consente di ricevere i risultati. `PostTranscriptions` restituisce un handle e `GetTranscriptions` usa l'handle per crearne uno nuovo per ottenere lo stato della trascrizione.

Il codice di esempio corrente non specifica un modello personalizzato. Per la trascrizione dei file, il servizio userà i modelli di base. Per specificare i modelli, è possibile passare nello stesso metodo gli ID per il modello acustico e per quello linguistico.

> [!NOTE]
> Per le trascrizioni di base non è necessario dichiarare l'ID dei modelli di base. Se si specifica solo l'ID di un modello linguistico (e non un ID di modello acustico), viene automaticamente selezionato un modello acustico corrispondente. Se si specifica solo l'ID di un modello acustico, viene automaticamente selezionato un modello linguistico corrispondente.

## <a name="download-the-sample"></a>Scaricare l'esempio

L'esempio è disponibile nella directory `samples/batch` nel [repository di esempi GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
