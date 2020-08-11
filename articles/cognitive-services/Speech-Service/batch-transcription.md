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
ms.openlocfilehash: 3e7f310f37bd016a73c589db3c9a23e197465427
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053917"
---
# <a name="what-is-batch-transcription"></a>Che cos'è la trascrizione batch?

La trascrizione batch è un set di operazioni API REST che consente di trascrivere una grande quantità di audio nell'archivio. È possibile puntare a file audio con un URI di firma di accesso condiviso (SAS) e ricevere in modo asincrono i risultati della trascrizione. Con la nuova API v 3.0 è possibile scegliere di trascrivere uno o più file audio oppure elaborare un intero contenitore di archiviazione.

La trascrizione asincrona di sintesi vocale è solo una delle funzionalità. È possibile usare le API REST per la trascrizione batch per chiamare i metodi seguenti:



|    Operazione di trascrizione batch                                             |    Metodo    |    Chiamata API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Crea una nuova trascrizione.                                              |    POST      |    speechtotext/v 3.0/trascrizioni            |
|    Recupera un elenco di trascrizioni per la sottoscrizione autenticata.    |    GET       |    speechtotext/v 3.0/trascrizioni            |
|    Ottiene un elenco di impostazioni locali supportate per le trascrizioni offline.              |    GET       |    speechtotext/v 3.0/trascrizioni/impostazioni locali    |
|    Aggiorna i dettagli modificabili della trascrizione identificata dal relativo ID.    |    PATCH     |    speechtotext/v 3.0/trascrizioni/{ID}       |
|    Elimina l'attività di trascrizione specificata.                                 |    DELETE    |    speechtotext/v 3.0/trascrizioni/{ID}       |
|    Ottiene la trascrizione identificata dall'ID specificato.                        |    GET       |    speechtotext/v 3.0/trascrizioni/{ID}       |
|    Ottiene i file di risultati della trascrizione identificata dall'ID specificato.    |    GET       |    speechtotext/v 3.0/trascrizioni/{ID}/file |




È possibile esaminare e testare l'API dettagliata, disponibile come [documento di spavalderia](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

I processi di trascrizione batch sono pianificati in base al massimo sforzo. Attualmente non è prevista alcuna stima per il momento in cui un processo viene modificato in stato di esecuzione. Con il normale carico di sistema, il processo dovrebbe avvenire entro pochi minuti. Una volta nello stato di esecuzione, la trascrizione effettiva viene elaborata più velocemente dell'audio in tempo reale.

Accanto all'API di facile utilizzo, non è necessario distribuire endpoint personalizzati e non sono previsti requisiti di concorrenza da osservare.

## <a name="prerequisites"></a>Prerequisiti

### <a name="subscription-key"></a>Chiave di sottoscrizione

Come per tutte le funzionalità del servizio Voce, si crea una chiave di sottoscrizione dal [portale di Azure](https://portal.azure.com) seguendo la [guida introduttiva](get-started.md).

>[!NOTE]
> Per usare la trascrizione batch, è necessaria una sottoscrizione standard (S0) per il servizio di riconoscimento vocale. Le chiavi di sottoscrizione gratuite (F0) non funzionano. Per altre informazioni, vedere [prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modelli personalizzati

Se si prevede di personalizzare i modelli, attenersi alla procedura descritta in [personalizzazione acustica](how-to-customize-acoustic-models.md) e [personalizzazione della lingua](how-to-customize-language-model.md). Per usare i modelli creati nella trascrizione batch, è necessario il percorso del modello. È possibile recuperare il percorso del modello quando si esaminano i dettagli del modello ( `self` proprietà). Un endpoint personalizzato distribuito *non è necessario* per il servizio di trascrizione batch.

## <a name="the-batch-transcription-api"></a>API di trascrizione batch

### <a name="supported-formats"></a>Formati supportati

L'API di trascrizione batch supporta i formati seguenti:

| Formato | Codec | Bitrate | Frequenza di campionamento                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bit  | 8 kHz o 16 kHz, mono o stereo |
| MP3    | PCM   | 16 bit  | 8 kHz o 16 kHz, mono o stereo |
| OGG    | OPUS  | 16 bit  | 8 kHz o 16 kHz, mono o stereo |

Per i flussi audio stereo, i canali sinistro e destro vengono suddivisi durante la trascrizione. Per ogni canale viene creato un file di risultati JSON. I timestamp generati per espressione consentono allo sviluppatore di creare una trascrizione finale ordinata.

### <a name="configuration"></a>Configurazione

I parametri di configurazione vengono forniti come JSON (uno o più file singoli):

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

I parametri di configurazione vengono forniti come JSON (elaborazione di un intero contenitore di archiviazione):

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

Per usare modelli sottoposti a training personalizzati nelle trascrizioni batch, è possibile farvi riferimento come illustrato di seguito:

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
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
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `None` la disabilitazione del filtro per la volgarità, `Masked` la sostituzione della volgarità con gli asterischi, `Removed` la rimozione di tutti i messaggi profani dal risultato o l' `Tags` aggiunta di tag di "volgarità". L'impostazione predefinita è `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori accettati sono `None` la disabilitazione della punteggiatura, `Dictated` per implicare la punteggiatura esplicita (pronunciata), `Automatic` per consentire al decodificatore di gestire la punteggiatura o `DictatedAndAutomatic` di usare la punteggiatura dettata e automatica. L'impostazione predefinita è `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Specifica se i timestamp a livello di parola devono essere aggiunti all'output. I valori accettati sono `true` l'abilitazione dei timestamp a livello di parola e `false` (valore predefinito) per disabilitarla.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Specifica che l'analisi della fase di esecuzione deve essere eseguita nell'input, che dovrebbe essere canale mono contenente due voci. I valori accettati sono `true` l'abilitazione della diaria e `false` (valore predefinito) per disabilitarla. È anche necessario che `wordLevelTimestampsEnabled` sia impostato su true.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Matrice facoltativa di numeri di canale da elaborare. Qui è possibile specificare un subset dei canali disponibili nel file audio da elaborare, ad esempio `0` solo. Se non specificato, `0` i canali e `1` vengono trascritti come predefiniti.
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Durata facoltativa per eliminare automaticamente le trascrizioni dopo aver completato la trascrizione. `timeToLive`È utile nelle trascrizioni di elaborazione di massa per assicurarsi che vengano eliminate, ad esempio `PT12H` . Se non è specificato o impostato su `PT0H` , la trascrizione non verrà eliminata automaticamente.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL facoltativo con firma di accesso condiviso del [servizio](../../storage/common/storage-sas-overview.md) per un contenitore scrivibile in Azure. Il risultato viene archiviato in questo contenitore. Quando non è specificato, Microsoft archivia i risultati in un contenitore di archiviazione gestito da Microsoft. Quando la trascrizione viene eliminata chiamando la [trascrizione Delete](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription), verranno eliminati anche i dati del risultato.
:::row-end:::

### <a name="storage"></a>Archiviazione

La trascrizione batch supporta l' [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) per la lettura di audio e la scrittura di trascrizioni nell'archiviazione.

## <a name="the-batch-transcription-result"></a>Risultato della trascrizione batch

Per ogni audio di input, viene creato un file di risultati della trascrizione. È possibile ottenere l'elenco dei file di risultati chiamando [Get trascrizioni files](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles). Questo metodo restituisce un elenco di file di risultati per la trascrizione. Per trovare il file di trascrizione per un file di input specifico, filtrare tutti i file restituiti con `kind`  ==  `Transcription` e `name`  ==  `{originalInputName.suffix}.json` .

Ogni file di risultati della trascrizione ha il formato seguente:

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
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
      `lexical`
   :::column-end:::
   :::column span="2":::
      Parole effettive riconosciute.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Formato inverso-testo normalizzato del testo riconosciuto. Vengono applicate le abbreviazioni ("Doctor Smith" a "Dr Smith"), i numeri di telefono e altre trasformazioni.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      Il form ITN con la maschera volgare applicata.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      Il form di visualizzazione del testo riconosciuto. Sono inclusi la punteggiatura e le maiuscole aggiunte.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separazione dei relatori

La deframmentazione è il processo di separazione degli altoparlanti in un audio. La pipeline batch supporta la registrazione ed è in grado di riconoscere due altoparlanti nelle registrazioni del canale mono. La funzionalità non è disponibile nelle registrazioni stereo.

L'output della trascrizione con la registrazione abilitata contiene una `Speaker` voce per ogni frase trascritta. Se la non viene usata, la proprietà `Speaker` non è presente nell'output JSON. Per la tua operazione sono supportate due voci, quindi i relatori sono identificati come `1` o `2` .

Per richiedere la predisposizione, è sufficiente aggiungere il parametro pertinente nella richiesta HTTP, come illustrato di seguito.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

I timestamp a livello di parola devono essere abilitati perché i parametri nella richiesta precedente indicano.

## <a name="best-practices"></a>Procedure consigliate

Il servizio di trascrizione può gestire un numero elevato di trascrizioni inviate. È possibile eseguire una query sullo stato delle trascrizioni tramite un oggetto `GET` sulle [trascrizioni Get](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions). Chiamare la [trascrizione Delete](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) regolarmente dal servizio una volta recuperati i risultati. In alternativa `timeToLive` , impostare la proprietà su un valore ragionevole per garantire l'eventuale eliminazione dei risultati.

## <a name="sample-code"></a>Codice di esempio

Gli esempi completi sono disponibili nel [repository di esempio GitHub](https://aka.ms/csspeech/samples) all'interno della `samples/batch` sottodirectory.

Aggiornare il codice di esempio con le informazioni sulla sottoscrizione, l'area del servizio, l'URI di firma di accesso condiviso che punta al file audio da trascrivere e il percorso del modello nel caso in cui si desideri usare un modello personalizzato.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Il codice di esempio configura il client e Invia la richiesta di trascrizione. Esegue quindi il polling delle informazioni sullo stato e i dettagli di stampa sullo stato della trascrizione.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Per dettagli completi sulle chiamate precedenti, vedere il [documento di spavalderia](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). L'esempio completo illustrato qui è disponibile in [GitHub](https://aka.ms/csspeech/samples), nella sottodirectory `samples/batch`.

Prendere nota della configurazione asincrona per l'inserimento dell'audio e la ricezione dello stato della trascrizione. Viene creato un client HTTP .NET. È disponibile un `PostTranscriptions` metodo per inviare i dettagli del file audio e un `GetTranscriptions` metodo per la ricezione degli Stati. `PostTranscriptions` restituisce un handle e `GetTranscriptions` usa l'handle per crearne uno nuovo per ottenere lo stato della trascrizione.

Il codice di esempio corrente non specifica un modello personalizzato. Il servizio usa il modello di base per la trascrizione del file o dei file. Per specificare il modello, è possibile passare lo stesso metodo al riferimento del modello per il modello personalizzato.

> [!NOTE]
> Per le trascrizioni di base, non è necessario dichiarare l'ID per il modello di base.

## <a name="download-the-sample"></a>Scaricare l'esempio

L'esempio è disponibile nella directory `samples/batch` nel [repository di esempi GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sull'API di sintesi vocale V3](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
