---
title: Che cos'è la trascrizione batch - Servizio di riconoscimento vocale
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
ms.openlocfilehash: ee7fbddade055c11f5870aa5a588a2fd02f10a23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131593"
---
# <a name="what-is-batch-transcription"></a>Che cos'è la trascrizione batch?

La trascrizione batch è un set di operazioni dell'API REST che consente di trascrivere una grande quantità di audio nell'archiviazione. È possibile puntare a file audio con un URI di firma di accesso condiviso e ricevere in modo asincrono i risultati della trascrizione.

La trascrizione asincrona vocale testrina è solo una delle funzionalità. È possibile utilizzare le API REST di trascrizione batch per chiamare i metodi seguenti:You can use batch transcription REST APIs to call the following methods:



|    Operazione trascrizione batch                                             |    Metodo    |    Chiamata ALL'API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Crea una nuova trascrizione.                                              |    POST      |    api/speechtotext/v2.0/transcriptioni            |
|    Recupera un elenco di trascrizioni per la sottoscrizione autenticata.    |    GET       |    api/speechtotext/v2.0/transcriptioni            |
|    Ottiene un elenco di impostazioni locali supportate per le trascrizioni offline.              |    GET       |    api/speechtotext/v2.0/transcriptions/locales    |
|    Aggiorna i dettagli modificabili della trascrizione identificata dal relativo ID.    |    PATCH     |    api/speechtotext/v2.0/trascrizioni/       |
|    Elimina l'attività di trascrizione specificata.                                 |    Elimina    |    api/speechtotext/v2.0/trascrizioni/       |
|    Ottiene la trascrizione identificata dall'ID specificato.                        |    GET       |    api/speechtotext/v2.0/trascrizioni/       |




È possibile rivedere e testare l'API dettagliata, disponibile come `Custom Speech transcriptions` [documento Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), sotto l'intestazione .

I processi di trascrizione batch vengono programmati nel modo più necessario. Attualmente non è disponibile alcuna stima per quando un processo passa allo stato di esecuzione. In condizioni normali di carico del sistema, dovrebbe avvenire in pochi minuti. Una volta in esecuzione, la trascrizione effettiva viene elaborata più velocemente rispetto all'audio in tempo reale.

Accanto all'API di facile utilizzo, non è necessario distribuire endpoint personalizzati e non sono previsti requisiti di concorrenza da osservare.

## <a name="prerequisites"></a>Prerequisiti

### <a name="subscription-key"></a>Chiave di sottoscrizione

Come per tutte le funzionalità del servizio Voce, si crea una chiave di sottoscrizione dal [portale di Azure](https://portal.azure.com) seguendo la [guida introduttiva](get-started.md).

>[!NOTE]
> Per usare la trascrizione batch, è necessaria una sottoscrizione standard (S0) per il servizio di riconoscimento vocale. Le chiavi di abbonamento gratuite (F0) non funzionano. Per ulteriori informazioni, vedere [prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelli personalizzati

Se si prevede di personalizzare modelli acustici o linguistici, seguire i passaggi descritti in Personalizzare i [modelli acustici](how-to-customize-acoustic-models.md) e progettare i [modelli di linguaggio](how-to-customize-language-model.md)di personalizzazione . Per utilizzare i modelli creati nella trascrizione batch, sono necessari i relativi ID modello. È possibile recuperare l'ID del modello quando si esaminano i dettagli del modello. Un endpoint personalizzato distribuito non è necessario per il servizio di trascrizione batch.

## <a name="the-batch-transcription-api"></a>API di trascrizione batch

### <a name="supported-formats"></a>Formati supportati

L'API di trascrizione batch supporta i formati seguenti:

| Format | Codec | Bitrate | Frequenza di campionamento                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bit  | 8 kHz o 16 kHz, mono o stereo |
| MP3    | PCM   | 16 bit  | 8 kHz o 16 kHz, mono o stereo |
| OGG    | OPUS  | 16 bit  | 8 kHz o 16 kHz, mono o stereo |

Per i flussi audio stereo, i canali sinistro e destro vengono divisi durante la trascrizione. Per ogni canale, viene creato un file di risultati JSON. I timestamp generati per ogni espressione consentono allo sviluppatore di creare una trascrizione finale ordinata.

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

Usare queste proprietà facoltative per configurare la trascrizione:Use these optional properties to configure transcription:

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
      Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori `None` accettati sono disabilitare il filtro volgarità, `Masked` sostituire volgarità con asterischi, `Removed` rimuovere tutte le parolacce dal risultato o `Tags` aggiungere tag "profanity". L'impostazione predefinita è `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori `None` accettati sono per `Dictated` disabilitare la punteggiatura, implicare `Automatic` la punteggiatura esplicita (parlata), `DictatedAndAutomatic` lasciare che il decodificatore tratti la punteggiatura o utilizzare la punteggiatura dettata e automatica. L'impostazione predefinita è `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Specifica se i timestamp a livello di parola devono essere aggiunti all'output. I valori `true` accettati consentono di `false` abilitare i timestamp a livello di parola e (il valore predefinito) per disabilitarlo.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Specifica se l'analisi del sentiment deve essere applicata all'espressione. I valori `true` accettati `false` sono l'abilitazione e (il valore predefinito) per disabilitarlo.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Specifica che l'analisi della diarizzazione deve essere eseguita sull'input, che dovrebbe essere un canale mono contenente due voci. I valori `true` accettati consentono `false` la diarizzazione e (il valore predefinito) per disabilitarla. Richiede anche `AddWordLevelTimestamps` di essere impostato su true.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL facoltativo con [sAS del servizio](../../storage/common/storage-sas-overview.md) in un contenitore scrivibile in Azure.Optional URL with service SAS to a writeable container in Azure. Il risultato viene archiviato in questo contenitore.
:::row-end:::

### <a name="storage"></a>Archiviazione

La trascrizione batch supporta [l'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) di Azure per la lettura di audio e la scrittura di trascrizioni nell'archiviazione.

## <a name="the-batch-transcription-result"></a>Risultato della trascrizione batch

Per l'audio di input mono, viene creato un file di risultati della trascrizione. Per l'audio di input stereo, vengono creati due file di risultati di trascrizione. Ognuno ha questa struttura:

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

Il risultato contiene i seguenti moduli:

| Form        | Contenuto                                                                                                                                                  |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Lexical`   | Le parole effettive riconosciute.                                                                                                                             |
| `ITN`       | Forma inversa-testo-normalizzata del testo riconosciuto. Abbreviazioni ("medafa" a "dr smith"), vengono applicati numeri di telefono e altre trasformazioni. |
| `MaskedITN` | Forma ITN con mascheratura volgarità applicata.                                                                                                             |
| `Display`   | Forma di visualizzazione del testo riconosciuto. Sono incluse punteggiatura e maiuscole aggiuntive.                                                             |

## <a name="speaker-separation-diarization"></a>Separazione degli altoparlanti (diarizzazione)

La diarizzazione è il processo di separazione degli altoparlanti in un pezzo di audio. La nostra pipeline Batch supporta la diarizzazione ed è in grado di riconoscere due altoparlanti sulle registrazioni a canale monocanale. La funzione non è disponibile nelle registrazioni stereo.

L'output di trascrizione `SpeakerId`contiene un file . Se la diarizzazione non `"SpeakerId": null` viene utilizzata, viene visualizzata nell'output JSON. Per la diarizzazione supportiamo due voci, `"1"` `"2"`in modo che gli altoparlanti sono identificati come o .

Per richiedere la diarizzazione, è sufficiente aggiungere il parametro pertinente nella richiesta HTTP, come illustrato di seguito.

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

I timestamp a livello di parola devono anche essere "attivati" come indicano i parametri nella richiesta precedente.

## <a name="sentiment-analysis"></a>Analisi del sentiment

La funzione di valutazione stima il sentiment espresso nell'audio. Il sentiment è espresso da un `Negative`valore `Neutral`compreso tra 0 e 1 per , e `Positive` sentimento. Ad esempio, l'analisi del sentiment può essere utilizzata negli scenari del servizio clienti:For example, sentiment analysis can be used in call center scenarios:

- Ottieni informazioni sulla soddisfazione del cliente
- Ottenere informazioni sulle prestazioni degli agenti (team che prende le chiamate)
- Trovare il punto esatto nel tempo in cui una chiamata ha preso una svolta in una direzione negativa
- Che cosa è andato bene quando si trasforma una chiamata negativa in una direzione positiva
- Identificare ciò che piace ai clienti e cosa non gli piace di un prodotto o di un servizio

Il sentiment viene assegnato per segmento audio in base alla forma lessicale. L'intero testo all'interno di tale segmento audio viene utilizzato per calcolare il sentiment. Non viene calcolato alcun sentiment aggregato per l'intera trascrizione. Attualmente l'analisi del sentiment è disponibile solo per la lingua inglese.

Un esempio di output JSON è simile al seguente:A JSON output sample looks like below:

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

Il servizio di trascrizione è in grado di gestire un gran numero di trascrizioni inviate. È possibile interrogare lo stato delle trascrizioni `GET` tramite un metodo di [trascrizione](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Mantenere le informazioni restituite a una `take` dimensione ragionevole specificando il parametro (alcune centinaia). Eliminare regolarmente [le trascrizioni](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) dal servizio dopo aver recuperato i risultati. Ciò garantisce risposte rapide dalle chiamate di gestione della trascrizione.

## <a name="sample-code"></a>Codice di esempio

Esempi completi sono disponibili nel repository `samples/batch` di esempio [GitHub](https://aka.ms/csspeech/samples) all'interno della sottodirectory.

> [!NOTE]
> La funzionalità di trascrizione batch viene esposta tramite l'API REST descritta in precedenza. In questo modo la trascrizione Batch può essere usata da quasi tutti i linguaggi o ambienti di programmazione che supporta REST. Gli esempi seguenti e gli esempi in GitHub sono meramente rappresentativi e **non** connotano limiti su dove è possibile usare l'API.

È necessario personalizzare il codice di esempio con le informazioni della sottoscrizione, l'area di servizio, l'URI di firma di accesso condiviso che punta al file audio da trascrivere e gli ID di modello in caso si voglia usare un modello acustico o linguistico personalizzato.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Il codice di esempio imposta il client e invia la richiesta di trascrizione. Viene quindi eseguito il polling delle informazioni sullo stato e dei dettagli sull'avanzamento della trascrizione.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Per i dettagli completi sulle chiamate precedenti, consulta il nostro [documento Swagger](https://westus.cris.ai/swagger/ui/index). L'esempio completo illustrato qui è disponibile in [GitHub](https://aka.ms/csspeech/samples), nella sottodirectory `samples/batch`.

Prendere nota della configurazione asincrona per l'inserimento dell'audio e la ricezione dello stato della trascrizione. Viene creato un client HTTP .NET. Il metodo `PostTranscriptions` consente di inviare i dettagli del file audio e il metodo `GetTranscriptions` consente di ricevere i risultati. `PostTranscriptions` restituisce un handle e `GetTranscriptions` usa l'handle per crearne uno nuovo per ottenere lo stato della trascrizione.

Il codice di esempio corrente non specifica un modello personalizzato. Per la trascrizione dei file, il servizio userà i modelli di base. Per specificare i modelli, è possibile passare nello stesso metodo gli ID per il modello acustico e per quello linguistico.

> [!NOTE]
> Per le trascrizioni di base non è necessario dichiarare l'ID dei modelli di base. Se si specifica solo l'ID di un modello linguistico (e non un ID di modello acustico), viene automaticamente selezionato un modello acustico corrispondente. Se si specifica solo l'ID di un modello acustico, viene automaticamente selezionato un modello linguistico corrispondente.

## <a name="download-the-sample"></a>Scaricare l'esempio

L'esempio è disponibile nella directory `samples/batch` nel [repository di esempi GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
