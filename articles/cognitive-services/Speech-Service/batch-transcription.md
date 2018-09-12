---
title: API di trascrizione Azure Batch
description: Esempi
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: b6fb39ef5941157cfe0d18324deeb9d836d7ab09
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377622"
---
# <a name="batch-transcription"></a>Trascrizione Batch

La trascrizione Batch è ideale se sono presenti grandi quantità di dati audio. È possibile fare riferimento a file audio e ottenere le trascrizioni in modalità asincrona.

## <a name="batch-transcription-api"></a>API di trascrizione Batch

L'API di trascrizione batch offre la trascrizione asincrona della voce in testo scritto insieme ad altre funzionalità.

> [!NOTE]
> L'API di trascrizione Batch è ideale per i call center che in genere accumulano migliaia di ore di audio. L'API di basa su un approccio di tipo "attiva e dimentica" che facilita la trascrizione di volumi elevati di registrazioni audio.

### <a name="supported-formats"></a>Formati supportati

L'API di trascrizione Batch supporta i formati seguenti:

NOME| Canale  |
----|----------|
mp3 |   Mono   |   
mp3 |  Stereo  | 
wav |   Mono   |
wav |  Stereo  |

Per i flussi audio stereo, la trascrizione Batch divide i canali sinistro e destro durante la trascrizione. I due file JSON con il risultato vengono creati ognuno da un singolo canale. I timestamp per espressione consentono allo sviluppatore di creare una trascrizione finale ordinata. Nell'esempio JSON seguente viene descritto l'output di un canale.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> L'API di trascrizione Batch usa un servizio REST per richiedere le trascrizioni, lo stato e i risultati associati. È possibile usare l'API da qualsiasi linguaggio. Nella prossima sezione ne viene descritta la modalità d'uso.

## <a name="authorization-token"></a>Token di autorizzazione

Come per tutte le funzionalità del Servizio di riconoscimento vocale unificato, è necessario creare una chiave di sottoscrizione nel [portale di Azure](https://portal.azure.com). Inoltre, viene acquisita una chiave API dal portale di riconoscimento vocale: 

1. Accedere a [Riconoscimento vocale personalizzato](https://customspeech.ai).

2. Selezionare **Sottoscrizioni**.

3. Selezionare **Genera chiave API**.

    ![Screenshot della pagina di sottoscrizione di Riconoscimento vocale personalizzato](media/stt/Subscriptions.jpg)

4. Copiare e incollare la chiave nel codice client nell'esempio riportato di seguito.

> [!NOTE]
> Se si prevede di usare un modello personalizzato sarà necessario anche l'ID del modello. Si noti che questo non è l'ID di distribuzione o dell'endpoint presente dei dettagli dell'Endpoint. Si tratta dell'ID modello che è possibile recuperare quando si selezionano i dettagli di tale modello.

## <a name="sample-code"></a>Codice di esempio

Personalizzare il codice di esempio seguente con una chiave di sottoscrizione e una chiave API. In questo modo è possibile ottenere un token di connessione.

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

Dopo aver ottenuto il token, è necessario specificare l'URI SAS selezionando il file audio che richiede la trascrizione. Il resto del codice esegue l'iterazione con lo stato e visualizza i risultati.

```cs
   static async Task TranscribeAsync()
        { 
            private const string SubscriptionKey = "<your Speech[Preview] subscription key>";
            private const string HostName = "cris.ai";
            private const int Port = 443;
    
            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);

            // get the transcription Id from the location URI
            var createdTranscriptions = new List<Guid>();
            createdTranscriptions.Add(new Guid(transcriptionLocation.ToString().Split('/').LastOrDefault()))

            while (true)
            {
                // get all transcriptions for the user
                transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);
                completed = 0; running = 0; notStarted = 0;

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
                                // not creted form here, continue
                                continue;
                            }
                            
                            completed++;
                            
                            // if the transcription was successfull, check the results
                            if (transcription.Status == "Succeeded")
                            {
                                var resultsUri = transcription.ResultsUrls["channel_0"];
                                WebClient webClient = new WebClient();
                                var filename = Path.GetTempFileName();
                                webClient.DownloadFile(resultsUri, filename);
                                var results = File.ReadAllText(filename);
                                Console.WriteLine("Transcription succedded. Results: ");
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

                Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.WriteLine("Press any key...");
        }
```

> [!NOTE]
> Nell'esempio precedente, la chiave di sottoscrizione è la chiave della risorsa del Servizio di riconoscimento vocale (anteprima) creata nel portale di Azure. Le chiavi ottenute dalla risorsa del Servizio di riconoscimento vocale personalizzato non funzioneranno.

Si noti la configurazione asincrona per l'inserimento dell'audio e la ricezione dello stato della trascrizione. Il client creato è un client HTTP NET. Il metodo `PostTranscriptions` consente di inviare i dettagli del file audio e il metodo `GetTranscriptions` consente di ricevere i risultati. `PostTranscriptions` restituisce un handle e `GetTranscriptions` usa questo handle per crearne uno per ottenere lo stato della trascrizione.

Il codice di esempio attuale non specifica alcun modello personalizzato. Per la trascrizione dei file, il servizio userà i modelli di base. Per specificare i modelli, è possibile passare sullo stesso metodo gli ID modello per il modello acustico e linguistico. 

Se non si vuole usare la baseline, è necessario passare gli ID modello per i modelli acustico e linguistico.

> [!NOTE]
> Per la trascrizione di base non è necessario dichiarare gli endpoint dei modelli di base. Per usare modelli personalizzati si dovranno specificare gli ID degli endpoint come nell'[esempio](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Per usare una baseline acustica con un modello linguistico di base si dovrà dichiarare solo l'ID endpoint del modello personalizzato. Microsoft rileva il modello di base (acustico o linguistico) partner e lo userà per soddisfare la richiesta di trascrizione.

### <a name="supported-storage"></a>Archiviazione supportata

Attualmente è supportata solo l'archiviazione BLOB di Azure.

## <a name="downloading-the-sample"></a>Download dell'esempio

L'esempio visualizzato qui è disponibile in [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> In genere una trascrizione audio richiede un intervallo di tempo uguale alla durata del file audio più 2-3 minuti.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
