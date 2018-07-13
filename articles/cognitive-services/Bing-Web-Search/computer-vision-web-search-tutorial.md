---
title: Esercitazione sull'app Ricerca visiva per dispositivi mobili
description: Applicazione C# open source che implementa la ricerca visiva tramite l'API Visione artificiale e l'API Ricerca Web Bing di Servizi cognitivi e il framework multipiattaforma Xamarin.Forms.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374249"
---
# <a name="visual-search-mobile-app-tutorial"></a>Esercitazione sull'app Ricerca visiva per dispositivi mobili

## <a name="introduction"></a>Introduzione  
Questa esercitazione esamina gli endpoint delle API [Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/) e [Ricerca Web Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) e descrive come possono essere usati per compilare un'applicazione di base di ricerca visiva con [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/).  In questa esercitazione vengono trattati gli argomenti seguenti: 

> [!div class="checklist"]
> * Impostazione di Visual Studio per sviluppare applicazioni Xamarin.Forms
> * Uso di [Xamarin Media Plugin](https://github.com/jamesmontemagno/MediaPlugin) per acquisire e importare dati di immagine
> * Analisi del testo da un'immagine tramite l'API Visione artificiale
> * Invio di richieste di ricerca all'API Ricerca Web Bing
> * Analisi delle risposte JSON da entrambe le API con [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (con deserializzazione LINQ e degli oggetti del modello)
> * Creazione di un'interfaccia utente Xamarin.Forms multipiattaforma per la ricerca visiva 

## <a name="prerequisites"></a>Prerequisiti

Questo esempio è stato sviluppato usando Xamarin.Forms con [Visual Studio 2017](https://www.visualstudio.com/downloads/). È possibile usare anche Visual Studio 2017 Community Edition. Per altre informazioni sull'uso di Xamarin con Visual Studio, vedere la [documentazione di Xamarin](https://developer.xamarin.com/guides/cross-platform/getting_started/).

Questo esempio usa i pacchetti NuGet seguenti:

> [!div class="checklist"]
> * [Xamarin Media Plugin](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

L'applicazione usa le API Servizi cognitivi seguenti:

> [!div class="checklist"]
> * [API Ricerca Web Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [API Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

Per ottenere le chiavi della versione di valutazione di 30 giorni per queste API, vedere [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) (Prova Servizi cognitivi). Per altre informazioni su come ottenere le chiavi per un uso commerciale, vedere [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/).

## <a name="setup-for-development"></a>Installazione per lo sviluppo  

### <a name="installing-xamarin-on-windows"></a>Installazione di Xamarin in Windows
In qualsiasi edizione di Visual Studio 2017 installata, aprire il Programma di installazione di Visual Studio, selezionare il menu hamburger associato all'installazione di Visual Studio e scegliere **Modify** (Modifica).  

![Programma di installazione di Visual Studio](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

Scorrere verso il basso fino alla sezione relativa a dispositivi mobili e giochi e abilitare l'opzione **Mobile Development with .NET** (Sviluppo di applicazioni per dispositivi mobili con .NET), se non è già abilitata.

![Programma di installazione di Visual Studio con Xamarin.Forms selezionato](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

Fare clic su **Modify** (Modifica) nell'angolo inferiore destro della finestra e attendere l'installazione di Xamarin.

### <a name="installing-xamarin-on-macos"></a>Installazione di Xamarin in macOS
In Visual Studio per Mac Xamarin è già presente. Non è necessaria alcuna installazione.

## <a name="building-and-running-the-app"></a>Compilazione ed esecuzione dell'app

Un file di soluzione di Visual Studio *(con estensione sln)* per l'applicazione di ricerca visiva può essere scaricato dalla pagina [Visual Search App with Cognitive Services](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/) (App Ricerca visiva con Servizi cognitivi). È possibile scaricare l'archivio ZIP mediante un Web browser, clonarlo nella workstation in uso da GitHub o scaricarlo usando Visual Studio.

Per iniziare a usare l'esempio, aprire `VisualSearchApp.sln` in Visual Studio.  L'inizializzazione dei componenti necessari potrebbe richiedere qualche istante.

L'applicazione richiede due librerie di terze parti: **Json.NET** e **Xamarin Media Plugin**. È possibile installare tali librerie direttamente in Visual Studio con Gestione pacchetti NuGet. Scegliere  **Strumenti > Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione** oppure fare doppio clic con il pulsante destro del mouse sulla soluzione in Esplora soluzioni e quindi scegliere **Gestisci pacchetti NuGet** nel menu di scelta rapida.

Nella finestra NuGet individuare e installare **Xamarin Media Plugin** (Xam.Plugin.Media) versione 2.6.2 e **Json.NET** (Newtonsoft.Json) versione 10.0.3. Verificare di selezionare tutti i progetti durante l'installazione.

Per compilare l'applicazione per tutte le piattaforme disponibili, premere **CTRL + MAIUSC + B** oppure fare clic su **Compila** sul menu della barra multifunzione e scegliere **Compila soluzione**.  Per compilare e testare il codice per iOS da un sistema di Windows, vedere [questa guida](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/).

Prima di eseguire l'applicazione, è necessario selezionare una configurazione, una piattaforma e un progetto di destinazione.  Le applicazioni Xamarin.Forms vengono compilate in codice nativo per Windows, Android e iOS. Questa esercitazione include le schermate della versione di Windows dell'esempio, ma tutte le versioni sono funzionalmente equivalenti. Le impostazioni di distribuzione usate in questa guida vengono visualizzate qui.  

![Visual Studio configurato per la compilazione per un dispositivo Android](./media/computer-vision-web-search-tutorial/configuration-selection.png)

Dopo che il processo di compilazione è stato completato e dopo che è stata selezionata una piattaforma di destinazione, fare clic sul pulsante **Avvia** sulla barra degli strumenti o premere **F5**. Visual Studio distribuisce la soluzione per la piattaforma di destinazione e la avvia.

Viene visualizzata la pagina per aggiungere le chiavi. Questa pagina è definita in `AddKeysPage.xaml`.  

![Immagine della pagina per l'aggiunta delle chiavi](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

Incollare qui le chiavi per le API Visione artificiale e Ricerca Web Bing. L'API Visione artificiale richiede anche di scegliere il server in cui è ospitato l'endpoint.

> [!TIP]
> Per ignorare questa pagina, immettere queste informazioni nelle posizioni appropriate in `App.xaml.cs`. 

L'applicazione convalida le chiavi eseguendo una query di test, quindi viene visualizzata la pagina di selezione OCR (definita in `OcrSelectPage.xaml`).
   
![Immagine della pagina di selezione OCR](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

Nella parte superiore della schermata è possibile scegliere se il testo da riconoscere è stampato o scritto a mano.

> [!TIP]
> Mantenere l'elemento di cui si sta tentando di riconoscere il testo il più uniforme possibile e assicurarsi che sia illuminato in modo uniforme senza alcun riflesso. Abbiamo scoperto che talvolta la funzionalità OCR per testi scritti a mano funziona meglio per i tipi di carattere di script o altro testo personalizzato.

Fare clic si **Take Photo** (Scatta foto) o **Import Photo** (Importa foto) per scattare una foto tramite la fotocamera del dispositivo o scegliere una foto archiviata nel dispositivo.

Dopo che una foto è stata scattata o scelta, l'immagine viene passato all'API Visione artificiale. Nella pagina della parole trovate (definita in `OcrResultsPage.xaml`) vengono visualizzate tutte le parole riconosciute nell'immagine.

![Immagine della pagina di risultati OCR](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> L'immagine usata per questi risultati si trova nel repository di origine come `SamplePhotos\TestImage.jpg`.

Quando si fa clic su un elemento nella pagina delle parole trovate, viene visualizzata la pagina dei risultati Web (`WebResultsPage.xaml`) in cui sono presenti i risultati Bing principali relativi alla ricerca.

![Immagine della pagina dei risultati Web](./media/computer-vision-web-search-tutorial/web-results-page.png)  
Scegliere infine un elemento nella pagina dei risultati Web per aprire il collegamento in una visualizzazione Web incorporata. In alternativa, tornare alla pagina per scegliere un risultato diverso.

![Immagine della pagina di visualizzazione Web](./media/computer-vision-web-search-tutorial/web-view-page.png)  

È possibile interagire con la pagina in modo analogo a qualsiasi Web browser oppure tornare alla pagina dei risultati Web. 

## <a name="review-and-learn"></a>Esame e apprendimento

Dopo aver iniziato a usare l'app Ricerca visiva, esaminiamo come usare le due API Servizi cognitivi Microsoft.  Sia che lo si usi come punto di partenza per la propria applicazione o semplicemente come una guida dettagliata per le API, questo esempio è utile per esaminare l'applicazione schermata per schermata per capire esattamente come funziona.

### <a name="add-keys-page"></a>Pagina di aggiunta delle chiavi
L'interfaccia utente per la pagina di aggiunta delle chiavi è definita in `AddKeysPage.xaml` e la logica principale è definita in `AddKeysPage.xaml.cs`. Poiché le chiavi vengono convalidate tramite una richiesta di test, il momento è adatto per stabilire come usare gli endpoint di Servizi cognitivi in C#.  

Le operazioni di base di questa interazione sono le seguenti: 

1. Creare un'istanza degli oggetti *HttpResponseMessage* e *HttpClient* in *System.Net.Http*
2. Collegare le intestazioni desiderate (definite nel riferimento all'API dell'endpoint) per l'oggetto *HttpClient*
3. Inviare una richiesta GET o POST con i propri dati, aggiungendo tutti i parametri necessari all'URI dell'endpoint
4. Verificare che la risposta sia corretta
5. Passare la risposta per un'ulteriore elaborazione

La funzione che controlla la validità della chiave dell'API di ricerca Bing è `CheckBingSearchKey()`, illustrata di seguito.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

Una funzione simile, `CheckComputerVisionKey()`, viene usata per convalidare la chiave di Visione artificiale.

### <a name="ocr-select-page"></a>Pagina di selezione OCR

La pagina di selezione OCR (`OcrSelectPage.xaml`) ha due ruoli importanti. La pagina determina in primo luogo il tipo di riconoscimento ottico dei caratteri eseguito sulla foto di destinazione e in secondo luogo consente di acquisire o aprire l'immagine da elaborare.

La seconda attività è in genere un'operazione complessa in un'applicazione multipiattaforma, perché ogni piattaforma richiede un codice diverso. Xamarin Media Plugin consente di eseguire questa attività in poche righe di codice.

La funzione seguente include un esempio di uso di Xamarin Media Plugin per l'acquisizione di foto.  Le operazioni presenti sono le seguenti:

1. Verificare che nel dispositivo corrente sia disponibile una fotocamera
2. Creare un'istanza di un oggetto `StoreCameraMediaOptions` per specificare dove salvare l'immagine acquisita
3. Acquisire un'immagine e ottenere un oggetto `MediaFile` contenente i dati dell'immagine
4. Decomprimere l'oggetto `MediaFile` in una matrice di byte
5. Restituire la matrice di byte per un'ulteriore elaborazione

Di seguito viene indicata la funzione `TakePhoto()` che usa Xamarin Media Plugin per l'acquisizione di foto.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
L'utilità di importazione foto funziona in modo analogo. Entrambe le funzionalità sono disponibili in `OcrSelectPage.xaml.cs`. 
 
> [!NOTE]
> L'endpoint della funzione OCR per testo scritto a mano può gestire solo foto con dimensioni inferiori a 4 MB. Per evitare problemi relativi alle dimensioni dei file, è possibile ridurre la foto al 50% delle dimensioni originali impostando l'opzione `PhotoSize = PhotoSize.Medium` nell'oggetto `StoreCameraMediaOptions`.  Se il dispositivo richiede foto di qualità eccezionalmente alta e si ricevono errori, è possibile provare a usare `PhotoSize = PhotoSize.Small`.

Di seguito vinee indicata la funzione dell'utilità che consente di convertire un oggetto *MediaFile* in una matrice di byte.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>Pagina dei risultati OCR

Nella pagina dei risultati OCR viene visualizzato il testo estratto dall'immagine usando il [metodo SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm) di **Json.NET**.  I due endpoint OCR funzionano in modo leggermente diverso, pertanto è importante descriverli entrambi.  

Poiché l'API Visione artificiale viene ospitata solo in alcuni percorsi server, il relativo endpoint deve essere creato in modo dinamico in fase di esecuzione. La funzione `SetOcrLocation` (parte della classe *AppConstants* statica disponibile in `AppConstants.cs`) imposta il percorso dell'endpoint dell'URI. Tale percorso corrisponde alla selezione dell'utente nella pagina di aggiunta delle chiavi oppure al valore impostato in `App.xaml.cs`. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

Esaminiamo queste richieste dell'API. L'API Visione artificiale per il riconoscimento ottico dei caratteri è in grado di analizzare il testo da una lingua non determinato, ma è possibile indicare di eseguire la ricerca di testo in lingua inglese per migliorare i risultati. L'API è anche in grado di rilevare automaticamente l'orientamento del testo. L'uso di un orientamento predefinito può migliorare i risultati dell'analisi, ma il rilevamento dell'orientamento può essere utile in un'applicazione per dispositivi mobili.

Per altre informazioni sui parametri disponibili con questo endpoint, vedere [Handwritten Optical Character Recognition API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc) (Riferimento all'API di riconoscimento ottico dei caratteri per testo stampato).  

L'API di riconoscimento ottico dei caratteri per testo scritto a mano è ancora in fase di anteprima e attualmente funziona unicamente con testo in lingua inglese.  Per questo motivo, l'unico parametro è attualmente un flag che indica se analizzare o meno il testo scritto a mano. L'API di riconoscimento ottico dei caratteri per testo scritto a mano può analizzare sia testo stampato che scritto a mano, ma `handwriting=false` restituisce risultati migliori su testo stampato. 

Poiché l'applicazione è in inglese, sarebbe stato possibile usare solo la funzione OCR per testo scritto a mano per l'esempio e impostare il flag `handwriting` in base a ciò che l'utente desidera riconoscere.  Entrambi gli endpoint sono stati usati a scopo illustrativo.  

Per altre informazioni sui parametri disponibili con questo endpoint, vedere [Handwritten Optical Character Recognition API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200) (Riferimento all'API di riconoscimento ottico dei caratteri per testo scritto a mano).

A questo punto esaminiamo le funzioni che chiamano le API.

`FetchPrintedWordList()` usa l'endpoint OCR per testo stampato per analizzare testo stampato nelle immagini. La richiesta HTTP segue una struttura simile alla chiamata usata nella pagina di aggiunta delle chiavi per convalidare la chiave, ma è necessario inviare una foto. Una foto è troppo grande per essere passata in una stringa di query, pertanto è necessario usare una richiesta HTTP POST anziché una richiesta GET. È necessario codificare la foto (che è presente in memoria come matrice di byte) in un oggetto `ByteArrayContent` e aggiungere un'intestazione a tale oggetto definendo il tipo di dati di cui è in corso l'invio. 

I tipi di contenuto accettabili sono disponibili in [Computer Vision API reference](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200) (Riferimento all'API Visione artificiale).  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> Si noti come viene usato il [metodo SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm) di **Json.NET** per estrarre testo dall'oggetto della risposta. `SelectToken` è ideale quando è necessaria solo una parte specifica della risposta JSON, che può essere passata alla funzione successiva. In altre parti del codice le risposte JSON vengono deserializzate in oggetti del modello definiti in `ModelObjects.cs`.

La differenza principale tra la richiesta OCR per testo stampato e scritto a mano consiste nel fatto che il servizio OCR per testo stampato restituisce il testo riconosciuto come parte della risposta HTTP, mentre il servizio OCR per il testo scritto a mano ha bisogno di un'ulteriore richiesta per ottenere le informazioni. La richiesta OCR per testo scritto a mano iniziale restituisce uno stato HTTP 202, che indica solo l'avvio dell'elaborazione. Questa risposta contiene un endpoint che il cliente deve controllare per ottenere la risposta completata quando è disponibile. Per vedere il funzionamento complessivo, vedere `FetchHandwrittenWordList()`.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` è la seconda parte del processo OCR per la scrittura manuale. Esegue il test dell'URI estratto dai metadati della risposta iniziale fino a quando non viene ottenuto un risultato o la funzione raggiunge il timeout.  È importante che questa funzione venga chiamata in modo asincrono nel proprio thread. In caso contrario, questo metodo blocca l'interfaccia utente fino a quando l'elaborazione non è stata completata.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Pagina dei risultati Web
Dopo che l'utente seleziona un termine di ricerca visualizzato nella pagina dei risultati OCR, è possibile spostarsi nella pagina dei risultati Web.  In tale pagina viene creata una richiesta all'[API Ricerca Web Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/), inviata in seguito all'endpoint del servizio, e viene deserializzata la risposta JSON tramite il metodo [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) di **Json.NET**.  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

L'API Ricerca Web Bing funziona meglio quando si specifica il maggior numero di informazioni possibili su ciò che potrebbe essere necessario all'utente. In particolare, è quasi sempre consigliabile usare i parametri `mkt` e `setLang` (qui impostati per la lingua inglese - Stati Uniti) per identificare posizione e la lingua preferita dell'utente.

> [!NOTE]
> La query di Ricerca Web Bing è semplice per garantire che il codice sorgente sia di facile comprensione.  In un'applicazione reale è necessario aggiungere le intestazioni seguenti alla richiesta HTTP per migliorare i risultati. 
> * User-Agent  
> * X-MSEdge-ClientID  
> * X-Search-ClientIP  
> * X-Search-Location  
>
> Altre informazioni su questi valori di intestazione sono disponibili in [Bing Web Search API Reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers) (Riferimento all'API Ricerca Web Bing)

## <a name="next-steps"></a>Passaggi successivi
In Servizi cognitivi Microsoft sono disponibili numerosi servizi aggiuntivi che è possibile integrare facilmente in questa applicazione.  Ad esempio, è possibile:

* Aggiungere [Ricerca entità Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) per migliorare i risultati della ricerca Web
* Andare a [Ricerca personalizzata Bing](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/) al posto di Ricerca Web Bing
* Usare la funzionalità avanzata [Ricerca immagini Bing](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) per ottenere altre informazioni sull'immagine acquisita e trovare immagini simili sul Web
* Usare [Controllo ortografico Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) per migliorare la qualità del testo analizzato
* Integrare [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) per visualizzare il testo estratto in lingue diverse
* Combinare e mettere in corrispondenza altri servizi nel [portale di Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/) per un'esperienza utente ottimale.
