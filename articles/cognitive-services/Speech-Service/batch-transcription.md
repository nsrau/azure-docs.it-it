---
title: API di trascrizione di Azure Batch | Azure Microsoft Docs
description: Esempi
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 9dd7479ae95f74123d9b762e42ec95e8dbf25818
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346445"
---
# <a name="batch-transcription"></a>Trascrizione Batch

La trascrizione Batch è ideale per i casi d'uso in cui sono presenti grandi quantità di dati audio. Consente allo sviluppatore di selezionare file audio e ottenere le trascrizioni in modalità asincrona.

## <a name="batch-transcription-api"></a>API di trascrizione Batch

L'API di trascrizione Batch rende possibile lo scenario appena descritto. Offre la trascrizione asincrona della voce in testo scritto insieme ad altre funzionalità.

> [!NOTE]
> L'API di trascrizione Batch è ideale per i call center che in genere accumulano migliaia di ore di audio. Grazie all'approccio di tipo "attiva e dimentica" su cui si basa l'API, la trascrizione di volumi elevati di registrazioni audio risulta piuttosto semplice.

### <a name="supported-formats"></a>Formati supportati

L'API di trascrizione Batch aspira a diventare la scelta naturale per tutti gli scenari offline relativi ai call center e a offrire supporto per tutti i formati correlati. Formati attualmente supportati:

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
> L'API di trascrizione Batch usa un servizio REST per richiedere le trascrizioni, lo stato e i risultati associati. L'API può essere usata da qualsiasi linguaggio. Nella prossima sezione ne viene descritta la modalità d'uso.

## <a name="authorization-token"></a>Token di autorizzazione

Come per tutte le funzionalità del Servizio di riconoscimento vocale unificato, l'utente deve creare una chiave di sottoscrizione nel [portale di Azure](https://portal.azure.com). È inoltre necessario ottenere una chiave API dal portale del Servizio riconoscimento vocale. Per generare una chiave API seguire questa procedura:

1. Accedere a https://customspeech.ai.

2. Fare clic su Sottoscrizioni.

3. Fare clic sull'opzione `Generate API Key`.

    ![Visualizzazione caricamento](media/stt/Subscriptions.jpg)

4. Copiare e incollare la chiave nel codice client nell'esempio riportato di seguito.

> [!NOTE]
> Se si prevede di usare un modello personalizzato sarà necessario anche l'ID del modello. Si noti che non si tratta dell'ID di distribuzione o dell'endpoint che si trova nella visualizzazione Dettagli endpoint, ma dell'ID del modello che è possibile recuperare facendo clic sui dettagli del modello

## <a name="sample-code"></a>Codice di esempio

Usare l'API è piuttosto semplice. Il codice di esempio seguente deve essere personalizzato con una chiave di sottoscrizione e una chiave API, che a sua volta consente allo sviluppatore di ottenere un token di connessione, come mostra il frammento di codice seguente:

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

Dopo aver ottenuto il token lo sviluppatore deve specificare l'URI SAS selezionando il file audio che richiede la trascrizione. Il resto del codice semplicemente scorre lo stato e visualizza i risultati.

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> La chiave di sottoscrizione indicata nel frammento di codice precedente è la chiave della risorsa del Servizio di riconoscimento vocale (anteprima) creata nel portale di Azure. Le chiavi ottenute dalla risorsa del Servizio di riconoscimento vocale personalizzato non funzioneranno.


Si noti la configurazione asincrona per l'inserimento dell'audio e la ricezione dello stato della trascrizione. Il client creato è un client HTTP NET. Il metodo `PostTranscriptions` consente di inviare i dettagli del file audio e il metodo `GetTranscriptions` consente di ricevere i risultati. `PostTranscriptions` restituisce un handle e il metodo `GetTranscriptions` usa questo handle per crearne uno per ottenere lo stato della trascrizione.

Il codice di esempio attuale non specifica alcun modello personalizzato. Per la trascrizione dei file, il servizio userà i modelli di base. Se l'utente vuole specificare i modelli, è possibile passare sullo stesso metodo gli ID modello per il modello acustico e linguistico. 

Se non si vuole usare i modelli di base, è necessario passare gli ID modello per i modelli acustico e linguistico.

> [!NOTE]
> Per la trascrizione di base non è necessario che l'utente dichiari gli endpoint dei modelli di base. Se l'utente vuole usare modelli personalizzati dovrà specificare gli ID degli endpoint come nell'[esempio](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Se l'utente vuole usare una baseline acustica con un modello linguistico di base dovrà dichiarare solo l'ID endpoint del modello personalizzato. Il sistema determinerà internamente il modello di base (acustico o linguistico) partner e lo userà per soddisfare la richiesta di trascrizione.

### <a name="supported-storage"></a>Archiviazione supportata

Attualmente è supportata solo l'archiviazione BLOB di Azure.

## <a name="downloading-the-sample"></a>Download dell'esempio

L'esempio visualizzato qui è disponibile in [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> In genere una trascrizione audio richiede un intervallo di tempo uguale alla durata del file audio più 2-3 minuti.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
