---
title: Caricare e indicizzare video con Video Indexer
titleSuffix: Azure Media Services
description: Questo argomento illustra come usare le API per caricare e indicizzare i video con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/03/2019
ms.author: juliako
ms.openlocfilehash: a1fd37b65c3449e7000db6189c8c71def1f96b0a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790048"
---
# <a name="upload-and-index-your-videos"></a>Caricare e indicizzare i video  

Durante il caricamento di video con l'API Video Indexer, sono disponibili le opzioni di caricamento seguenti: 

* caricare il video da un URL (scelta consigliata),
* inviare il file video come matrice di byte nel corpo della richiesta,
* Usare l'asset di servizi multimediali di Azure esistente fornendo l' [ID asset](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (supportato solo negli account a pagamento).

Questo articolo illustra come usare l'API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) per caricare e indicizzare i video in base a un URL. L'esempio di codice nell'articolo include il codice impostato come commento che mostra come caricare la matrice di byte. <br/>L'articolo illustra anche alcuni parametri che è possibile impostare per l'API per modificare il processo e l'output dell'API.

Una volta caricato il video, facoltativamente Video Indexer lo codifica (illustrato in questo articolo). Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuita) o un'opzione a pagamento (in cui non si è limitati dalla quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2400 minuti di indicizzazione gratuita per gli utenti di API. Con l'opzione a pagamento, si crea un account di Video Indexer [collegato alla sottoscrizione di Azure e un account di Servizi multimediali di Azure](connect-to-azure.md). Il pagamento viene effettuato per i minuti di indicizzazione, nonché in base ai costi correlati all'account multimediale. 

## <a name="uploading-considerations-and-limitations"></a>Caricamento di considerazioni e limitazioni
 
- Il nome del video non può contenere più di 80 caratteri.
- Quando si carica il video in base all'URL (scelta consigliata), l'endpoint deve essere protetto con TLS 1,2 (o versione successiva).
- Le dimensioni di caricamento con l'opzione URL sono limitate a 30 GB.
- La lunghezza dell'URL della richiesta è limitata a 6144 caratteri, in cui la lunghezza dell'URL della stringa di query è limitata a 4096 caratteri.
- Le dimensioni di caricamento con l'opzione di matrice di byte sono limitate a 2 GB.
- L'opzione della matrice di byte scade dopo 30 minuti.
- L'URL specificato in `videoURL` Param deve essere codificato.
- L'indicizzazione degli asset di servizi multimediali ha la stessa limitazione dell'indicizzazione dall'URL.
- Video Indexer ha un limite di durata massima di 4 ore per un singolo file.

> [!Tip]
> È consigliabile usare .NET framework versione 4.6.2. o versione successiva perché le versioni precedenti di .NET Framework non usano per impostazione predefinita TLS 1.2.
>
> Se è necessario usare versioni di .NET Framework precedenti, aggiungere una riga al codice prima di effettuare la chiamata API REST:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Configurazioni e parametri

Questa sezione descrive alcuni dei parametri facoltativi e i casi in cui è utile impostarli.

### <a name="externalid"></a>externalID 

Questo parametro consente di specificare un ID che verrà associato al video. L'ID può essere applicato all'integrazione del sistema esterno di gestione di contenuti video (VCM). I video situati nel portale di Video Indexer si possono cercare usando l'ID esterno specificato.

### <a name="callbackurl"></a>callbackUrl

Un URL che viene usato per notificare al cliente (con una richiesta POST) gli eventi seguenti:

- Modifica stato indicizzazione: 
    - Proprietà:    
    
        |name|Description|
        |---|---|
        |id|ID video|
        |state|Lo stato del video|  
    - Esempio: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Persona identificata nel video:
  - properties
    
      |name|Description|
      |---|---|
      |id| ID video|
      |faceId|L'ID viso che appare nell'indice video|
      |knownPersonId|L'ID utente univoco all'interno di un modello di viso|
      |personName|Il nome della persona|
        
    - Esempio: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Note

- Video Indexer restituisce tutti i parametri esistenti forniti nell'URL originale.
- L'URL specificato deve essere codificato.

### <a name="indexingpreset"></a>indexingPreset

Usare questo parametro se registrazioni non elaborate o esterne contengono rumore di fondo. Questo parametro si usa per configurare il processo di indicizzazione. È possibile specificare i valori seguenti:

- `AudioOnly`: indicizzare ed estrarre informazioni dettagliate usando solo l'audio (ignorando il video)
- `Default`: indicizzare ed estrarre informazioni dettagliate usando audio e video
- `DefaultWithNoiseReduction`: indicizzare ed estrarre informazioni dettagliate sia dall'audio che dal video, applicando gli algoritmi di riduzione del rumore al flusso audio
- `VideoOnly`-indicizzare ed estrarre informazioni dettagliate solo con video 

Il prezzo dipende dall'opzione di indicizzazione selezionata.  

### <a name="priority"></a>priority

I video vengono indicizzati da Video Indexer in base alle rispettive priorità. Usare il parametro **priority** per specificare la priorità dell'indice. Sono validi i valori seguenti: **Basso**, **Normale** (predefinito) e **Alto**.

Il parametro **priority** è supportato solo per gli account a pagamento.

### <a name="streamingpreset"></a>streamingPreset

Una volta caricato il video, facoltativamente Video Indexer lo codifica. Procede quindi con l'indicizzazione e l'analisi del video. Quando Video Indexer termina l'analisi, si riceve una notifica con l'ID del video.  

Quando si usano le API [Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) o [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?), uno dei parametri facoltativi è `streamingPreset`. Se si imposta `streamingPreset` su `Default`, `SingleBitrate` o `AdaptiveBitrate`, viene attivato il processo di codifica. Dopo il completamento dei processi di indicizzazione e codifica, il video viene pubblicato in modo che sia possibile eseguirne lo streaming. L'endpoint di streaming da cui si vuole trasmettere il video deve essere nello stato **In esecuzione**.

Per poter eseguire i processi di indicizzazione e codifica, l'[account di Servizi multimediali di Microsoft Azure connesso al proprio account di Video Indexer](connect-to-azure.md) richiede unità riservate. Per altre informazioni, vedere [Panoramica del ridimensionamento dell'elaborazione multimediale](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Poiché si tratta di processi a elevato utilizzo di calcolo, è consigliabile il tipo di unità S3. Il numero di unità riservate definisce il numero massimo di processi che è possibile eseguire in parallelo. In linea generale è consigliabile usare 10 unità riservate S3. 

Se si vuole solo indicizzare il video, ma non codificarlo, impostare `streamingPreset` su `NoStreaming`.

### <a name="videourl"></a>videoUrl

URL del file audio/video da indicizzare. L'URL deve puntare a un file multimediale; le pagine HTML non sono supportate. Il file può essere protetto da un token di accesso fornito come parte dell'URI e l'endpoint che gestisce il file deve essere protetto con il protocollo TLS 1.2 o versione successiva. L'URL deve essere codificato. 

Se `videoUrl` non viene specificato, Video Indexer richiede che il file venga passato come contenuto di un corpo multipart/form.

## <a name="code-sample"></a>Esempio di codice

Il frammento di codice C# seguente illustra l'uso di tutte le API di Video Indexer.

### <a name="instructions-for-running-this-code-sample"></a>Istruzioni per l'esecuzione di questo esempio di codice

Dopo aver copiato questo codice nella piattaforma di sviluppo, sarà necessario specificare due parametri: chiave di autenticazione e URL video di gestione API.

* Chiave API: la chiave API è la chiave di sottoscrizione di gestione API personale, che consente di ottenere un token di accesso per eseguire operazioni sull'account Video Indexer. 

    Per ottenere la chiave API, passare a questo flusso:

    * Passa a https://api-portal.videoindexer.ai/
    * Login
    * Passa a **prodotti** -> **autorizzazione** -> **sottoscrizione di autorizzazione**
    * Copiare la **chiave primaria**
* URL video: URL del file video/audio da indicizzare. L'URL deve puntare a un file multimediale; le pagine HTML non sono supportate. Il file può essere protetto da un token di accesso fornito come parte dell'URI e l'endpoint che gestisce il file deve essere protetto con il protocollo TLS 1.2 o versione successiva. L'URL deve essere codificato.

Il risultato dell'esecuzione dell'esempio di codice include un URL widget Insight e un URL Widget Player che consentiranno di esaminare le informazioni dettagliate e i video caricati rispettivamente. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

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
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

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

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

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

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
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
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

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
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

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
## <a name="common-errors"></a>Errori comuni

L'operazione di caricamento può restituire i codici di stato elencati nella tabella seguente.

|Codice di stato|ErrorType (nel corpo della risposta)|Description|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|Lo stesso video è già in fase di elaborazione nell'account specificato.|
|400|VIDEO_ALREADY_FAILED|Lo stesso video ha restituito un errore di elaborazione nell'account specificato meno di 2 ore prima. I client API devono attendere almeno 2 ore prima di caricare nuovamente un video.|

## <a name="next-steps"></a>Passaggi successivi

[Esaminare l'output del Video Indexer di Azure prodotto dall'API](video-indexer-output-json-v2.md)
