---
title: Come usare il servizio di traduzione vocale di batch
titleSuffix: Azure Cognitive Services
description: La trascrizione batch è ideale se si desidera trascrivere una grande quantità di audio in un archivio, ad esempio BLOB di Azure. Usando l'API REST dedicata è possibile puntare ai file audio con un URI di firma di accesso condiviso (SAS) e ricevere trascrizioni in modo asincrono.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: 5be3b4ce5d89a15009f2b9b31183400890dbefb5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918945"
---
# <a name="how-to-use-batch-transcription"></a>Come usare la trascrizione batch

La trascrizione batch è un set di operazioni API REST che consente di trascrivere una grande quantità di audio nell'archivio. È possibile puntare a file audio usando un URI tipico o un URI di firma di accesso condiviso e ricevere in modo asincrono i risultati della trascrizione. Con l'API v 3.0 è possibile trascrivere uno o più file audio oppure elaborare un intero contenitore di archiviazione.

È possibile usare le API REST per la trascrizione batch per chiamare i metodi seguenti:

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

Questa API non richiede endpoint personalizzati e non prevede requisiti di concorrenza.

I processi di trascrizione batch sono pianificati in base al massimo sforzo.
Non è possibile stimare il momento in cui un processo cambierà nello stato in esecuzione, ma dovrebbe verificarsi entro pochi minuti sotto il normale carico di sistema. Una volta nello stato di esecuzione, la trascrizione viene eseguita più velocemente della velocità di riproduzione del runtime audio.

## <a name="prerequisites"></a>Prerequisiti

Come per tutte le funzionalità del servizio Voce, si crea una chiave di sottoscrizione dal [portale di Azure](https://portal.azure.com) seguendo la [guida introduttiva](get-started.md).

>[!NOTE]
> Per usare la trascrizione batch, è necessaria una sottoscrizione standard (S0) per il servizio di riconoscimento vocale. Le chiavi di sottoscrizione gratuite (F0) non funzionano. Per altre informazioni, vedere [prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Se si prevede di personalizzare i modelli, attenersi alla procedura descritta in [personalizzazione acustica](how-to-customize-acoustic-models.md) e [personalizzazione della lingua](how-to-customize-language-model.md). Per usare i modelli creati nella trascrizione batch, è necessario il percorso del modello. È possibile recuperare il percorso del modello quando si esaminano i dettagli del modello ( `self` proprietà). Un endpoint personalizzato distribuito *non è necessario* per il servizio di trascrizione batch.

## <a name="batch-transcription-api"></a>API di trascrizione Batch

L'API trascrizione batch supporta i formati seguenti:

| Formato | Codec | BITS per campione | Frequenza di campionamento             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bit  | 8 kHz o 16 kHz, mono o stereo |
| MP3    | PCM   | 16 bit  | 8 kHz o 16 kHz, mono o stereo |
| OGG    | OPUS  | 16 bit  | 8 kHz o 16 kHz, mono o stereo |

Per i flussi audio stereo, i canali sinistro e destro vengono suddivisi durante la trascrizione. È in corso la creazione di un file di risultati JSON per ogni canale.
Per creare una trascrizione finale ordinata, usare i timestamp generati per espressione.

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

Il codice JSON seguente specifica un modello con training personalizzato da usare in una trascrizione batch:

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
      Facoltativo. il valore predefinito è `Masked` . Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `None` la disabilitazione del filtro per la volgarità, `Masked` la sostituzione della volgarità con gli asterischi, `Removed` la rimozione di tutti i messaggi profani dal risultato o l' `Tags` aggiunta di tag di "volgarità".
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Facoltativo. il valore predefinito è `DictatedAndAutomatic` . Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori accettati sono `None` la disabilitazione della punteggiatura, `Dictated` per implicare la punteggiatura esplicita (pronunciata), `Automatic` per consentire al decodificatore di gestire la punteggiatura o `DictatedAndAutomatic` di usare la punteggiatura dettata e automatica.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Facoltativo, `false` per impostazione predefinita. Specifica se i timestamp a livello di parola devono essere aggiunti all'output.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Facoltativo, `false` per impostazione predefinita. Specifica che l'analisi della fase di esecuzione deve essere eseguita nell'input, che dovrebbe essere canale mono contenente due voci. Nota: `wordLevelTimestampsEnabled` è necessario che sia impostato su `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Facoltativo `0` e `1` trascritto per impostazione predefinita. Matrice di numeri di canale da elaborare. Qui è possibile specificare un subset dei canali disponibili nel file audio da elaborare, ad esempio `0` solo.
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Facoltativo, nessuna eliminazione per impostazione predefinita. Durata per eliminare automaticamente le trascrizioni dopo aver completato la trascrizione. `timeToLive`È utile nelle trascrizioni di elaborazione di massa per assicurarsi che vengano eliminate, ad esempio `PT12H` per 12 ore.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL facoltativo con firma di accesso condiviso del [servizio](../../storage/common/storage-sas-overview.md) per un contenitore scrivibile in Azure. Il risultato viene archiviato in questo contenitore. Quando non è specificato, Microsoft archivia i risultati in un contenitore di archiviazione gestito da Microsoft. Quando la trascrizione viene eliminata chiamando la [trascrizione Delete](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription), verranno eliminati anche i dati del risultato.
:::row-end:::

### <a name="storage"></a>Archiviazione

La trascrizione batch può leggere l'audio da un URI Internet visibile pubblicamente ed è in grado di leggere audio o scrivere trascrizioni usando un URI SAS con [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

## <a name="batch-transcription-result"></a>Risultato trascrizione batch

Per ogni input audio, viene creato un file di risultati della trascrizione.
L'operazione [Get trascrizioni files](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) restituisce un elenco di file di risultati per la trascrizione. Per trovare il file di trascrizione per un file di input specifico, filtrare tutti i file restituiti con `kind`  ==  `Transcription` e `name`  ==  `{originalInputName.suffix}.json` .

Il formato di ogni file di risultati della trascrizione è il seguente:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "speaker": 1,                 // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
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

Il risultato contiene i campi seguenti:

:::row:::
   :::column span="1":::
      **Campo**
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

L'output della trascrizione con la registrazione abilitata contiene una `Speaker` voce per ogni frase trascritta. Se la non viene usata, la `Speaker` proprietà non è presente nell'output JSON. Per la tua operazione sono supportate due voci, quindi i relatori sono identificati come `1` o `2` .

Per richiedere la messa in pagina, aggiungere la `diarizationEnabled` proprietà su `true` come la richiesta http Mostra di seguito.

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

Il servizio di trascrizione batch può gestire un numero elevato di trascrizioni inviate. È possibile eseguire una query sullo stato delle trascrizioni con le [trascrizioni Get](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions).
Chiamare la [trascrizione Delete](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) regolarmente dal servizio una volta recuperati i risultati. In alternativa `timeToLive` , impostare la proprietà per garantire l'eventuale eliminazione dei risultati.

## <a name="sample-code"></a>Codice di esempio

Gli esempi completi sono disponibili nel [repository di esempio GitHub](https://aka.ms/csspeech/samples) all'interno della `samples/batch` sottodirectory.

Aggiornare il codice di esempio con le informazioni sulla sottoscrizione, l'area del servizio, l'URI che punta al file audio da trascrivere e il percorso del modello se si usa un modello personalizzato.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Il codice di esempio configura il client e Invia la richiesta di trascrizione. Esegue quindi il polling delle informazioni sullo stato e i dettagli di stampa sullo stato della trascrizione.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Per dettagli completi sulle chiamate precedenti, vedere il [documento di spavalderia](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). L'esempio completo illustrato qui è disponibile in [GitHub](https://aka.ms/csspeech/samples), nella sottodirectory `samples/batch`.

In questo esempio viene utilizzata una configurazione asincrona per pubblicare audio e ricevere lo stato della trascrizione.
Il `PostTranscriptions` metodo invia i dettagli del file audio e il `GetTranscriptions` metodo riceve gli Stati.
`PostTranscriptions` restituisce un handle e `GetTranscriptions` usa l'handle per crearne uno nuovo per ottenere lo stato della trascrizione.

Questo codice di esempio non specifica un modello personalizzato. Il servizio usa il modello di base per la trascrizione del file o dei file. Per specificare il modello, è possibile passare lo stesso metodo al riferimento del modello per il modello personalizzato.

> [!NOTE]
> Per le trascrizioni di base, non è necessario dichiarare l'ID per il modello di base.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sull'API di sintesi vocale V3](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
