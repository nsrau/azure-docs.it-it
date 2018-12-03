---
title: Usare l'API di trascrizione di Azure Batch
titlesuffix: Azure Cognitive Services
description: Esempi per la trascrizione di volumi elevati di contenuto audio.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 8a180dfada9da92e0b8ed69373a20602b3b0a177
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495584"
---
# <a name="why-use-batch-transcription"></a>Perché usare la trascrizione batch?

La trascrizione Batch è ideale se nello spazio di archiviazione sono presenti grandi quantità di dati audio. Usando l'API REST dedicato è possibile fare riferimento ai file audio tramite URI di firma di accesso condiviso e ricevere trascrizioni in modo asincrono.

## <a name="the-batch-transcription-api"></a>API di trascrizione batch

L'API di trascrizione batch offre la trascrizione asincrona della voce in testo scritto insieme ad altre funzionalità. Si tratta di un'API REST che espone metodi per eseguire le operazioni seguenti:

1. Creare richieste di elaborazione batch
1. Eseguire query sullo stato 
1. Scaricare le trascrizioni

> [!NOTE]
> L'API di trascrizione batch è ideale per i call center che in genere accumulano migliaia di ore di contenuto audio. L'API si basa su un approccio di tipo "attiva e dimentica" che facilita la trascrizione di volumi elevati di registrazioni audio.

### <a name="supported-formats"></a>Formati supportati

L'API di trascrizione batch supporta i formati seguenti:

| Format | Codec | Bitrate | Frequenza di campionamento |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bit | 8 o 16 kHz, mono, stereo |
| MP3 | PCM | 16 bit | 8 o 16 kHz, mono, stereo |
| OGG | OPUS | 16 bit | 8 o 16 kHz, mono, stereo |

> [!NOTE]
> L'API di trascrizione batch richiede una chiave S0 (livello di pagamento). Non funziona con una chiave (f0) gratuita.

Per i flussi audio stereo, l'API di trascrizione batch divide i canali sinistro e destro durante la trascrizione. I due file JSON con il risultato vengono creati ognuno da un singolo canale. I timestamp per espressione consentono allo sviluppatore di creare una trascrizione finale ordinata. L'esempio JSON seguente illustra l'output di un canale, incluse le proprietà per impostare il filtro per le espressioni volgari e il modello di punteggiatura.

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> L'API di trascrizione batch usa un servizio REST per richiedere le trascrizioni, il relativo stato e i risultati associati. È possibile usare l'API da qualsiasi linguaggio. La sezione seguente descrive come viene usata l'API.

### <a name="query-parameters"></a>Parametri di query

Questi parametri possono essere inclusi nella stringa di query della richiesta REST.

| Parametro | DESCRIZIONE | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `none` che disabilita i filtri del contenuto volgare, `masked` che sostituisce il contenuto volgare con gli asterischi, `removed` che rimuove tutto il contenuto volgare dal risultato, o `tags` che aggiunge tag "contenuti volgari". L'impostazione predefinita è `masked`. | Facoltativo |
| `PunctuationMode` | Specifica come gestire la punteggiatura nei risultati del riconoscimento. I valori accettati sono `none` che consente di disattivare la punteggiatura, `dictated` che implica la punteggiatura esplicita, `automatic` che permette al decodificatore di occuparsi della punteggiatura, o `dictatedandautomatic` che implica segni di punteggiatura dettata o automatica. | Facoltativo |


## <a name="authorization-token"></a>Token di autorizzazione

Come per tutte le funzionalità del servizio Voce, si crea una chiave di sottoscrizione dal [portale di Azure](https://portal.azure.com) seguendo la [guida introduttiva](get-started.md). Se si prevede di ottenere trascrizioni dai modelli di base, la creazione di una chiave è l'unica operazione da eseguire. 

Se si prevede di personalizzare e usare un modello personalizzato, aggiungere la chiave di sottoscrizione nel portale del Servizio di riconoscimento vocale personalizzato eseguendo queste operazioni:

1. Accedere a [Riconoscimento vocale personalizzato](https://customspeech.ai).

2. In alto a destra selezionare **Subscriptions** (Sottoscrizioni).

3. Selezionare **Connect existing subscription** (Connetti sottoscrizione esistente).

4. Nella finestra popup aggiungere la chiave di sottoscrizione e un alias.

    ![Finestra per l'aggiunta di una sottoscrizione](media/stt/Subscriptions.jpg)

5. Copiare e incollare la chiave nel codice client nell'esempio riportato di seguito.

> [!NOTE]
> Se si prevede di usare un modello personalizzato sarà necessario anche l'ID del modello. Questo non è l'ID dell'endpoint che si trova sulla visualizzazione dei dettagli degli endpoint. Si tratta dell'ID modello che è possibile recuperare quando si selezionano i dettagli di tale modello.

## <a name="sample-code"></a>Codice di esempio

Personalizzare il codice di esempio seguente con una chiave di sottoscrizione e una chiave API. Questa azione consente di ottenere un token di connessione.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
         
            return new CrisClient(client);
        }
```

Dopo avere ottenuto il token, specificare l'URI di firma di accesso condiviso che punta al file di cui eseguire la trascrizione. La parte restante del codice esegue l'iterazione attraverso i diversi valori di stato e visualizza i risultati. Prima di tutto, si impostano la chiave, l'area, i modelli da usare e l'amministratore del servizio, come illustrato nel frammento di codice seguente. Successivamente, si crea un'istanza del client e della richiesta POST. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Dopo aver creato la richiesta, è possibile eseguire una query e scaricare i risultati della trascrizione, come illustrato nel frammento di codice seguente:

```cs
  
            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }
                    
                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;
                    
                    }
                }
            }
        }
```

Per informazioni dettagliate sulle chiamate precedenti, vedere il [documento di Swagger](https://westus.cris.ai/swagger/ui/index). L'esempio completo illustrato qui è disponibile in [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Nell'esempio precedente, la chiave di sottoscrizione proviene dalla risorsa del servizio Voce creata nel portale di Azure. Le chiavi ottenute dalla risorsa del Servizio di riconoscimento vocale personalizzato non funzionano.

Prendere nota della configurazione asincrona per l'inserimento dell'audio e la ricezione dello stato della trascrizione. Viene creato un client HTTP .NET. Il metodo `PostTranscriptions` consente di inviare i dettagli del file audio e il metodo `GetTranscriptions` consente di ricevere i risultati. `PostTranscriptions` restituisce un handle e `GetTranscriptions` usa l'handle per crearne uno nuovo per ottenere lo stato della trascrizione.

Il codice di esempio corrente non specifica un modello personalizzato. Per la trascrizione dei file, il servizio userà i modelli di base. Per specificare i modelli, è possibile passare nello stesso metodo gli ID per il modello acustico e per quello linguistico. 

Se non si vogliono usare i modelli di base, passare gli ID per il modello acustico e per quello linguistico.

> [!NOTE]
> Per le trascrizioni di base non è necessario dichiarare gli endpoint dei modelli di base. Per usare modelli personalizzati si dovranno specificare gli ID degli endpoint come nell'[esempio](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Per usare un modello acustico di base con un modello linguistico di base si dovrà dichiarare solo l'ID endpoint del modello personalizzato. Microsoft rileva il modello di base partner (acustico o linguistico) e lo usa per soddisfare la richiesta di trascrizione.

### <a name="supported-storage"></a>Archiviazione supportata

Attualmente è supportata solo l'archiviazione BLOB di Azure.

## <a name="download-the-sample"></a>Scaricare l'esempio

È possibile trovare l'esempio in questo articolo su [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Una trascrizione audio richiede in genere un intervallo di tempo uguale alla durata del file audio, più due o tre minuti.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
