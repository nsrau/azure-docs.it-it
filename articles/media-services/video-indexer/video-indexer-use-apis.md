---
title: Usare l'API Video Indexer - Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come iniziare a usare l'API Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: ce04e19022a9902c228079e866c192985694134c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799252"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Esercitazione: Usare l'API Video Indexer

Video Indexer consolida diverse tecnologie di intelligenza artificiale audio e video offerte da Microsoft in un unico servizio integrato, semplificando così lo sviluppo. Le API sono progettate per consentire agli sviluppatori di concentrarsi sull'uso delle tecnologie di intelligenza artificiale per i contenuti multimediali senza doversi preoccupare della scalabilità, della portata globale, della disponibilità e dell'affidabilità della piattaforma cloud. È possibile usare l'API per caricare file, ottenere informazioni dettagliate sui video, ottenere gli URL dei widget delle informazioni dettagliate e del lettore per incorporarli nell'applicazione ed eseguire altre attività.

Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuita) o un'opzione a pagamento (in cui non si è limitati dalla quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2400 minuti di indicizzazione gratuita per gli utenti di API. Con l'opzione a pagamento, si crea un account di Video Indexer [collegato alla sottoscrizione di Azure e un account di Servizi multimediali di Azure](connect-to-azure.md). Il pagamento viene effettuato per i minuti di indicizzazione, nonché in base ai costi correlati all'account di Azure Media Services. 

Questo articolo illustra in che modo gli sviluppatori possono trarre vantaggio dall'[API Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Eseguire la sottoscrizione all'API

1. Accedere al [portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/).
    
    ![Accesso](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * È necessario usare lo stesso provider usato per l'iscrizione a Video Indexer.
   > * Gli account Google e Microsoft (Outlook/Live) personali possono essere usati solo per gli account di valutazione. Gli account connessi ad Azure richiedono Azure AD.
   > * Può essere presente un solo account attivo per ogni indirizzo di posta elettronica. Se un utente tenta di effettuare l'accesso con user@gmail.com per LinkedIn e successivamente con user@gmail.com per Google, verrà visualizzata una pagina di errore che indica che l'utente già esiste.

2. Eseguire la sottoscrizione.

    Selezionare la scheda [Prodotti](https://api-portal.videoindexer.ai/products). Selezionare quindi Autorizzazione ed eseguire la sottoscrizione. 
    
    ![Iscrizione](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > I nuovi utenti sono automaticamente sottoscritti per l'autorizzazione.
    
    Una volta effettuata la sottoscrizione, sarà possibile visualizzare la sottoscrizione e le chiavi primaria e secondaria. Le chiavi devono essere protette. Le chiavi devono essere usate solo dal codice server. Non devono essere disponibili sul lato client (JS, HTML e così via).

    ![Iscrizione](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> L'utente di Video Indexer può usare una chiave di sottoscrizione singola per connetterti a più account Video Indexer. È quindi possibile collegare questi account Video Indexer a diversi account Servizi multimediali.

## <a name="obtain-access-token-using-the-authorization-api"></a>Ottenere i token di accesso usando l'API di autorizzazione

Una volta eseguita la sottoscrizione per l'API di autorizzazione, sarà possibile ottenere i token di accesso. Questi token di accesso vengono usati per eseguire l'autenticazione con l'API delle operazioni. 

Ogni chiamata all'API delle operazioni deve essere associata a un token di accesso, che corrisponde all'ambito di autorizzazione della chiamata.

- Livello utente: i token di accesso a livello di utente consentono di eseguire operazioni a livello di **utente**, ad esempio ottenere gli account associati.
- Livello di account: i token di accesso a livello di account consentono di eseguire operazioni a livello di **account** o a livello di **video**, ad esempio caricare video, elencare tutti i video, ottenere informazioni dettagliate sui video e così via.
- Livello di video: i token di accesso a livello di video consentono di eseguire operazioni su uno specifico **video**, ad esempio ottenere informazioni dettagliate sul video, scaricare i sottotitoli, ottenere i widget e così via. 

È possibile definire se i token devono essere di sola lettura o se devono consentire la modifica specificando **allowEdit=true/false**.

Per la maggior parte degli scenari da server a server, in genere si userà lo stesso token di **account**, perché consente di eseguire sia le operazioni a livello di **account** che le operazioni a livello di **video**. Se tuttavia si prevede di effettuare chiamate lato client a Video Indexer (ad esempio, da javascript), potrebbe essere preferibile usare un token di accesso **video**, in modo da impedire ai client di ottenere l'accesso all'intero account. È sempre per questo motivo che, quando si incorpora codice client di Video Indexer nel client, ad esempio usando **Get Insights Widget** (Ottieni widget informazioni dettagliate) o **Get Player Widget** (Ottieni widget lettore), è necessario fornire un token di accesso **video**.

Per semplificare le operazioni, è possibile usare l'API **Autorizzazione** > **GetAccounts** per ottenere gli account senza prima ottenere un token utente. È anche possibile richiedere di ottenere gli account con token validi, in modo da evitare una chiamata aggiuntiva per ottenere un token di account.

I token di accesso scadono dopo un'ora. Assicurarsi che il token di accesso sia valido prima di usare l'API delle operazioni. Se scade, chiamare di nuovo l'API di autorizzazione per ottenere un nuovo token di accesso.
 
A questo punto, si è pronti per avviare l'integrazione con l'API. Leggere la [descrizione dettagliata di ogni API REST di Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>ID account 

Il parametro Account ID è obbligatorio in tutte le chiamate alle API delle operazioni. L'ID account è un GUID che può essere ottenuto in uno dei modi seguenti:

* Usare il **sito Web di Video Indexer** per ottenere l'ID account:

    1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
    2. Passare alla pagina **Impostazioni**.
    3. Copiare l'ID account.

        ![ID account](./media/video-indexer-use-apis/account-id.png)

* Usare il **portale per sviluppatori di Video Indexer** per ottenere l'ID account a livello di codice.

    Usare l'API [Get accounts](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) (Ottieni account).
    
    > [!TIP]
    > È possibile generare i token di accesso per gli account definendo `generateAccessTokens=true`.
    
* Ottenere l'ID account dall'URL di una pagina del lettore nel proprio account.

    Quando si guarda un video, l'ID viene visualizzato dopo la sezione `accounts` e prima della sezione `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Consigli

In questa sezione sono elencati alcuni suggerimenti per l'uso dell'API Video Indexer.

- Se si prevede di caricare un video, è consigliabile posizionare il file in un percorso di rete pubblico (ad esempio, OneDrive). Ottenere il collegamento del video e specificare l'URL come il parametro per il caricamento del file. 

    L'URL fornito a Video Indexer deve puntare a un file multimediale (audio o video). Alcuni dei collegamenti generati da OneDrive sono relativi a una pagina HTML che contiene il file. Un modo semplice per verificare l'URL è quello di incollarlo in un browser: se viene avviato il download del file, è probabile che l'URL sia valido. Se il browser esegue il rendering di una visualizzazione, probabilmente il collegamento non è relativo a un file, ma a una pagina HTML.
    
- Quando si chiama l'API che consente di ottenere informazioni dettagliate sui video per il video specificato, viene visualizzato un output JSON dettagliato come contenuto della risposta. [Consultare informazioni dettagliate sul codice JSON restituito in questo argomento](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Esempio di codice

Il frammento di codice C# seguente illustra l'uso di tutte le API di Video Indexer.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token            
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Vedere anche 

- [Panoramica di Video Indexer](video-indexer-overview.md)
- [Aree](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Passaggi successivi

[Esaminare i dettagli dell'output JSON](video-indexer-output-json-v2.md).

