---
title: Caricare e indicizzare video con Video Indexer di Azure | Microsoft Docs
description: Questo argomento illustra come usare le API per caricare e indicizzare i video con Video Indexer di Azure
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: c4a755d0c13516ce3cb0177cea2ea17e4a3abcbb
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390970"
---
# <a name="upload-and-index-your-videos"></a>Caricare e indicizzare i video  

Questo argomento illustra come usare l'API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) per caricare e indicizzare i video con Video Indexer di Azure. Illustra inoltre alcuni dei parametri che è possibile impostare sull'API per modificare il processo e l'output dell'API.

> [!Note]
> Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuita) o un'opzione a pagamento (in cui non si è limitati dalla quota). <br/>Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2400 minuti di indicizzazione gratuita per gli utenti di API. <br/>Con l'opzione a pagamento, si crea un account di Video Indexer [connesso alla sottoscrizione di Azure e a un account di Servizi multimediali di Azure](connect-to-azure.md). Il pagamento viene effettuato per i minuti di indicizzazione, nonché in base ai costi correlati all'account multimediale. 

## <a name="configurations-and-params"></a>Configurazioni e parametri

Questa sezione descrive alcuni dei parametri facoltativi e i casi in cui è utile impostarli.

### <a name="externalid"></a>externalID 

Questo parametro consente di specificare un ID che verrà associato al video. L'ID può essere applicato all'integrazione del sistema esterno di gestione di contenuti video (VCM). I video situati nel portale di Video Indexer si possono cercare usando l'ID esterno specificato.

### <a name="indexingpreset"></a>indexingPreset

Usare questo parametro se registrazioni non elaborate o esterne contengono rumore di fondo. Questo parametro si usa per configurare il processo di indicizzazione. È possibile specificare i valori seguenti:

- `Default`: indicizzare ed estrarre informazioni dettagliate usando audio e video
- `AudioOnly`: indicizzare ed estrarre informazioni dettagliate usando solo l'audio (ignorando il video)
- `DefaultWithNoiseReduction`: indicizzare ed estrarre informazioni dettagliate sia dall'audio che dal video, applicando gli algoritmi di riduzione del rumore al flusso audio

Il prezzo dipende dall'opzione di indicizzazione selezionata.  

### <a name="callbackurl"></a>callbackUrl

URL POST a cui inviare una notifica quando l'indicizzazione è stata completata. Video Indexer vi aggiunge due parametri della stringa di query: id e state. Ad esempio, se l'URL di callback è 'https://test.com/notifyme?projectName=MyProject', la notifica verrà inviata con i parametri aggiuntivi a 'https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed'.

È anche possibile aggiungere altri parametri all'URL prima di inviare la chiamata a Video Indexer. Questi parametri verranno inclusi nel callback. In un secondo momento, all'interno del codice, è possibile analizzare la stringa di query e ottenere tutti i parametri specificati nella stringa di query (i dati aggiunti in origine all'URL e le informazioni fornite da Video Indexer). 

### <a name="streamingpereset"></a>streamingPreset

Una volta caricato il video, facoltativamente Video Indexer lo codifica. Procede quindi con l'indicizzazione e l'analisi del video. Quando Video Indexer termina l'analisi, si riceve una notifica con l'ID del video.  

Quando si usano le API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) o [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?), uno dei parametri facoltativi è `streamingPreset`. Se si imposta `streamingPereset` su `Default`, `SingleBitrate` o `AdaptiveBitrate`, viene attivato il processo di codifica. Dopo il completamento dei processi di indicizzazione e codifica, il video viene pubblicato in modo che sia possibile eseguirne lo streaming. L'endpoint di streaming da cui si vuole trasmettere il video deve essere nello stato **In esecuzione**.

Per poter eseguire i processi di indicizzazione e codifica, l'[account di Servizi multimediali di Microsoft Azure connesso al proprio account di Video Indexer](connect-to-azure.md) richiede unità riservate. Per altre informazioni, vedere [Panoramica del ridimensionamento dell'elaborazione multimediale](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Poiché si tratta di processi a elevato utilizzo di calcolo, è consigliabile il tipo di unità S3. Il numero di unità riservate definisce il numero massimo di processi che è possibile eseguire in parallelo. In linea generale è consigliabile usare 10 unità riservate S3. 

Se si vuole solo indicizzare il video, ma non codificarlo, impostare `streamingPereset` su `NoStreaming`.

## <a name="code-sample"></a>Esempio di codice

Il frammento di codice C# seguente illustra l'uso di tutte le API di Video Indexer.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```



## <a name="next-steps"></a>Passaggi successivi

[Esaminare l'output di Video Indexer di Azure generato dall'API v2](video-indexer-output-json-v2.md)
