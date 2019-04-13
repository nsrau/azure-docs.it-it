---
title: Creare revisioni di video usando .NET - Content Moderator
titlesuffix: Azure Cognitive Services
description: Questo articolo include informazioni ed esempi di codice per iniziare in poco tempo a usare Content Moderator SDK con C# per creare revisioni di video.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: e4dd7299907168bb50ac8ebdf90b381c0bac01f2
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527371"
---
# <a name="create-video-reviews-using-net"></a>Creare revisioni di video usando .NET

Questo articolo contiene informazioni ed esempi di codice per iniziare rapidamente a usare [Content Moderator SDK con C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) allo scopo di:

- Creare la revisione di un video per moderatori umani
- Aggiungere fotogrammi a una revisione
- Ottenere i fotogrammi per la revisione
- Ottenere lo stato e i dettagli della revisione
- Pubblicare la revisione

## <a name="prerequisites"></a>Prerequisiti

- Accedi o crea un account su Content Moderator [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) sito.
- In questo articolo si presuppone che sia stato [moderato il video (vedere la guida introduttiva)](video-moderation-api.md) e che siano disponibili i dati di risposta, indispensabili per creare revisioni basate su fotogrammi per i moderatori umani.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Verificare che la chiave API possa chiamare l'API di verifica per la creazione della revisione

Dopo aver completato i passaggi precedenti, si potrebbero avere due chiavi di Content Moderator se la procedura è stata avviata dal portale di Azure.

Se si prevede di usare la chiave API fornita da Azure nell'esempio di SDK, seguire i passaggi indicati nella sezione [Usare la chiave di Azure con l'API di revisione](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) per consentire all'applicazione di chiamare l'API di revisione e creare revisioni.

Se si usa la chiave di prova gratuita generata dallo strumento di revisione, l'account dello strumento di revisione conosce già la chiave e di conseguenza non sono necessari passaggi aggiuntivi.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Preparare il video e i fotogrammi video per la revisione

Il video e i fotogrammi video di esempio da rivedere devono essere pubblicati online poiché sono necessari i relativi URL.

> [!NOTE]
> Il programma usa screenshot salvati manualmente dal video con punteggi di contenuti spinti/per adulti casuali per illustrare l'uso dell'API di revisione. In una situazione reale viene usato l'[output della moderazione video](video-moderation-api.md#run-the-program-and-review-the-output) per creare immagini e assegnare punteggi. 

Per il video, è necessario un endpoint di streaming in modo che lo strumento di revisione riproduca il video nella visualizzazione lettore.

![Anteprima del video dimostrativo](images/ams-video-demo-view.PNG)

- Copiare l'**URL** in questa pagina della [demo di Servizi multimediali di Azure](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) per l'URL del manifesto.

Per i fotogrammi video (immagini), usare le immagini seguenti:

![Anteprima fotogramma video 1](images/ams-video-frame-thumbnails-1.PNG) | ![Anteprima fotogramma video 2](images/ams-video-frame-thumbnails-2.PNG) | ![Anteprima fotogramma video 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Fotogramma 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Fotogramma 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Fotogramma 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aggiungere un nuovo progetto **App console (.NET Framework)** alla soluzione.

1. Assegnare al progetto il nome **VideoReviews**.

1. Selezionare questo progetto come progetto di avvio singolo per la soluzione.

### <a name="install-required-packages"></a>Installare i pacchetti necessari

Installare i pacchetti NuGet seguenti per il progetto TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aggiornare le istruzioni using del programma

Modificare le istruzioni using del programma come indicato di seguito.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Aggiungere proprietà private

Aggiungere le proprietà private seguenti allo spazio dei nomi VideoReviews, classe Program.

Dove indicato, sostituire i valori di esempio per queste proprietà.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Replace this example location with the location for your Content Moderator account.
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

        // NOTE: Replace this example key with a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Creare l'oggetto Client di Content Moderator

Aggiungere la definizione del metodo seguente allo spazio dei nomi VideoReviews, classe Program.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureBaseURL
    };
}
```

## <a name="create-a-video-review"></a>Creare la revisione di un video

Creare la revisione di un video con **ContentModeratorClient.Reviews.CreateVideoReviews**. Per altre informazioni, vedere le [informazioni di riferimento sulle API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

L'oggetto **CreateVideoReviews** ha i parametri obbligatori seguenti:
1. Stringa contenente un tipo MIME, che deve essere "application/json". 
1. Nome del team di Content Moderator.
1. Un' **IList\<CreateVideoReviewsBodyItem >** oggetto. Ogni oggetto **CreateVideoReviewsBodyItem** rappresenta la revisione di un video. Questo Avvio rapido crea una revisione alla volta.

L'oggetto **CreateVideoReviewsBodyItem** include diverse proprietà. Impostare almeno le proprietà seguenti:
- **Content**. URL del video da rivedere.
- **ContentId**. ID da assegnare alla revisione del video.
- **Status**. Impostare il valore su "Unpublished". Se non si imposta questa proprietà, il valore predefinito è "Pending", vale a dire che la revisione del video è pubblicata e la revisione umana è in sospeso. Dopo la pubblicazione della revisione di un video, non è più possibile aggiungervi fotogrammi video, una trascrizione o il risultato della moderazione di una trascrizione.

> [!NOTE]
> L'oggetto **CreateVideoReviews** restituisce un oggetto IList<string>. Ognuna di queste stringhe contiene un ID per la revisione di un video. Questi ID sono GUID e non sono uguali al valore della proprietà **ContentId**. 

Aggiungere la definizione del metodo seguente allo spazio dei nomi VideoReviews, classe Program.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se questo limite viene superato, l'SDK genera un'eccezione con un codice di errore 429.
>
> Una chiave di livello gratuito prevede un unico limite di frequenza RPS.

## <a name="add-video-frames-to-the-video-review"></a>Aggiungere fotogrammi video alla revisione di un video

È possibile aggiungere fotogrammi video alla revisione di un video con **ContentModeratorClient.Reviews.AddVideoFrameUrl** (se i fotogrammi video sono ospitati online) o con **ContentModeratorClient.Reviews.AddVideoFrameStream** (se i fotogrammi video sono ospitati in locale). In questa guida introduttiva si presuppone che i fotogrammi video siano ospitati online, pertanto viene usato **AddVideoFrameUrl**. Per altre informazioni, vedere le [informazioni di riferimento sull'API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** ha i parametri obbligatori seguenti:
1. Stringa contenente un tipo MIME, che deve essere "application/json".
1. Nome del team di Content Moderator.
1. ID della revisione del video restituito da **CreateVideoReviews**.
1. Un' **IList\<VideoFrameBodyItem >** oggetto. Ogni oggetto **VideoFrameBodyItem** rappresenta un fotogramma video.

**VideoFrameBodyItem** ha le proprietà seguenti:
- **Timestamp**. Stringa contenente, in secondi, l'ora nel video in cui è stato estratto il fotogramma video.
- **FrameImage**. URL del fotogramma video.
- **Metadata**. Un oggetto IList\<VideoFrameBodyItemMetadataItem >. **VideoFrameBodyItemMetadataItem** è semplicemente una coppia chiave/valore. Le chiavi valide includono:
- **reviewRecommended**. True se è consigliata una revisione umana del fotogramma video.
- **adultScore**. Valore compreso tra 0 e 1 che classifica il livello di gravità del contenuto per adulti nel fotogramma video.
- **a**. True se il video contiene contenuto per adulti.
- **racyScore**. Valore compreso tra 0 e 1 che classifica il livello di gravità del contenuto spinto nel fotogramma video.
- **r**. True se il fotogramma video contiene contenuto spinto.
- **ReviewerResultTags**. Un oggetto IList\<VideoFrameBodyItemReviewerResultTagsItem >. **VideoFrameBodyItemReviewerResultTagsItem** è semplicemente una coppia chiave/valore. Un'applicazione può usare questi tag per organizzare i fotogrammi video.

> [!NOTE]
> In questa guida introduttiva vengono generati valori casuali per le proprietà **adultScore** e **racyScore**. In un'applicazione di produzione è possibile ottenere questi valori dal [servizio di moderazione video](video-moderation-api.md), distribuito come servizio multimediale di Azure.

Aggiungere le definizioni del metodo seguenti allo spazio dei nomi VideoReviews, classe Program.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Ottenere fotogrammi video per la revisione di un video

È possibile ottenere i fotogrammi video per la revisione di un video con **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** ha i parametri obbligatori seguenti:
1. Nome del team di Content Moderator.
1. ID della revisione del video restituito da **CreateVideoReviews**.
1. Indice in base zero del primo fotogramma video da ottenere.
1. Numero di fotogrammi video da ottenere.

Aggiungere la definizione del metodo seguente allo spazio dei nomi VideoReviews, classe Program.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Ottenere informazioni per la revisione di un video

È possibile ottenere informazioni per la revisione di un video con **ContentModeratorClient.Reviews.GetReview**. **GetReview** ha i parametri obbligatori seguenti:
1. Nome del team di Content Moderator.
1. ID della revisione del video restituito da **CreateVideoReviews**.

Aggiungere la definizione del metodo seguente allo spazio dei nomi VideoReviews, classe Program.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Pubblicare la revisione di un video

È possibile pubblicare la revisione di un video con **ContentModeratorClient.Reviews.PublishVideoReview**. L'oggetto **PublishVideoReview** ha i parametri obbligatori seguenti:
1. Nome del team di Content Moderator.
1. ID della revisione del video restituito da **CreateVideoReviews**.

Aggiungere la definizione del metodo seguente nello spazio dei nomi VideoReviews, classe Program.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Riassumendo

Aggiungere la definizione del metodo **Main** allo spazio dei nomi VideoReviews, classe Program. Infine, chiudere la classe Program e lo spazio dei nomi VideoReviews.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Eseguire il programma ed esaminare l'output
Quando si esegue l'applicazione, viene visualizzato un output nelle righe seguenti:

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Verificare la revisione del video

Al termine del processo, è possibile visualizzare la revisione del video con l'account dello strumento di revisione di Content Moderator nella schermata **Review** (Revisione)>**Video**.

![Revisione di video per moderatori umani](images/ams-video-review.PNG)

## <a name="next-steps"></a>Passaggi successivi

Per questa e altre guide introduttive Content Moderator per .NET, recuperare [Content Moderator SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e la [soluzione Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

Altre informazioni su come aggiungere una [moderazione di una trascrizione](video-transcript-moderation-review-tutorial-dotnet.md) alla revisione del video. 

Fare riferimento all'esercitazione dettagliata su come sviluppare una [soluzione completa di moderazione video](video-transcript-moderation-review-tutorial-dotnet.md).
