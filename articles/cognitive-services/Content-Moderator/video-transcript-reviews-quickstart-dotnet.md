---
title: Creare revisioni di trascrizioni di video usando .NET - Content Moderator
titleSuffix: Azure Cognitive Services
description: Creare revisioni di trascrizioni di video usando Content Moderator SDK per .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: c7d68685a772bb777fff1dd56a25990b9d0406ef
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242815"
---
# <a name="create-video-transcript-reviews-using-net"></a>Creare revisioni di trascrizioni di video usando .NET

Questo articolo contiene informazioni ed esempi di codice per iniziare rapidamente a usare [Content Moderator SDK con C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) allo scopo di:

- Creare la revisione di un video per moderatori umani
- Aggiungere una trascrizione moderata alla revisione
- Pubblicare la revisione

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) content moderator se non è già stato fatto.
- Questo articolo presuppone che il video sia stato [moderato](video-moderation-api.md) e sia stata [creata la relativa revisione](video-reviews-quickstart-dotnet.md) nello strumento di revisione per il processo decisione delle persone. Si vuole a questo punto aggiungere trascrizioni del video moderate nello strumento di revisione.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Verificare che la chiave API possa chiamare l'API di revisione (Creazione del processo)

Dopo aver completato i passaggi precedenti, potrebbero esserci due chiavi Content Moderator se la procedura è stata avviata dal portale di Azure.

Se si prevede di usare la chiave API fornita da Azure nell'esempio di SDK, seguire i passaggi indicati nella sezione [Usare la chiave di Azure con l'API di revisione](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) per consentire all'applicazione di chiamare l'API di revisione e creare revisioni.

Se si usa la chiave di prova gratuita generata dallo strumento di revisione, l'account dello strumento di revisione conosce già la chiave e di conseguenza non sono necessari passaggi aggiuntivi.

## <a name="prepare-your-video-for-review"></a>Preparare il video per la revisione

Aggiungere la trascrizione alla revisione di un video. Il video deve essere pubblicato online. È necessario conoscere il relativo endpoint di streaming. L'endpoint di streaming consente al lettore video dello strumento di revisione di riprodurre il video.

![Anteprima dimostrativa del video](images/ams-video-demo-view.PNG)

- Copiare l'**URL** in questa pagina della [demo di Servizi multimediali di Microsoft Azure](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) per l'URL del manifesto.

## <a name="create-your-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aggiungere un nuovo progetto **App console (.NET Framework)** alla soluzione.

1. Denominare il progetto **VideoTranscriptReviews**.

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
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Aggiungere proprietà private

Aggiungere le proprietà private seguenti nello spazio dei nomi VideoTranscriptReviews, classe Program.

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

### <a name="create-content-moderator-client-object"></a>Creare l'oggetto client di Content Moderator

Aggiungere la definizione del metodo seguente nello spazio dei nomi VideoTranscriptReviews, classe Program.

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
1. Oggetto **IList @ no__t-1CreateVideoReviewsBodyItem >** . Ogni oggetto **CreateVideoReviewsBodyItem** rappresenta la revisione di un video. Questo Avvio rapido crea una revisione alla volta.

L'oggetto **CreateVideoReviewsBodyItem** include diverse proprietà. Impostare almeno le proprietà seguenti:
- **Content**. URL del video da rivedere.
- **ContentId**. ID da assegnare alla revisione del video.
- **Status**. Impostare il valore su "Unpublished". Se non si imposta questa proprietà, il valore predefinito è "Pending", vale a dire che la revisione del video è pubblicata e la revisione umana è in sospeso. Dopo la pubblicazione della revisione di un video, non è più possibile aggiungervi fotogrammi video, una trascrizione o il risultato della moderazione di una trascrizione.

> [!NOTE]
> **CreateVideoReviews** restituisce un > IList @ no__t-1string. Ognuna di queste stringhe contiene un ID per la revisione di un video. Questi ID sono GUID e non sono uguali al valore della proprietà **ContentId**.

Aggiungere la definizione del metodo seguente nello spazio dei nomi VideoReviews, classe Program.

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
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se si supera il limite, l'SDK genera un'eccezione con il codice di errore 429.
>
> Una chiave di livello gratuito prevede un unico limite di frequenza RPS.

## <a name="add-transcript-to-video-review"></a>Aggiungere la trascrizione alla revisione di un video

Per aggiungere la trascrizione alla revisione di un video, si usa l'oggetto **ContentModeratorClient.Reviews.AddVideoTranscript**. L'oggetto **AddVideoTranscript** ha i parametri obbligatori seguenti:
1. ID del team di Content Moderator.
1. ID della revisione del video restituito da **CreateVideoReviews**.
1. Oggetto **Stream** che contiene la trascrizione.

La trascrizione deve essere nel formato WebVTT. Per altre informazioni, vedere [WebVTT: formato Web Video Text Tracks](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Il programma usa una trascrizione di esempio in formato VTT. In una soluzione reale, si usa il servizio Azure Media Indexer per [generare una trascrizione](https://docs.microsoft.com/azure/media-services/media-services-index-content) da un video.

Aggiungere la definizione del metodo seguente nello spazio dei nomi VideoTranscriptReviews, classe Program.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Aggiungere il risultato della moderazione di una trascrizione alla revisione del video

Oltre ad aggiungere una trascrizione a una revisione del video, si aggiunge il risultato della moderazione di tale trascrizione. Si usa a tale scopo **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Per altre informazioni, vedere le [informazioni di riferimento sulle API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

L'oggetto **AddVideoTranscriptModerationResult** ha i parametri obbligatori seguenti:
1. Stringa contenente un tipo MIME, che deve essere "application/json". 
1. Nome del team di Content Moderator.
1. ID della revisione del video restituito da **CreateVideoReviews**.
1. Un oggetto IList @ no__t-0TranscriptModerationBodyItem >. Un oggetto **TranscriptModerationBodyItem** include le proprietà seguenti:
1. **Terms**. Un oggetto IList @ no__t-0TranscriptModerationBodyItemTermsItem >. Un oggetto **TranscriptModerationBodyItemTermsItem** include le proprietà seguenti:
1. **Index**. Indice in base zero del termine.
1. **Term**. Stringa contenente il termine.
1. **Timestamp**. Stringa che contiene il tempo, in secondi, nella trascrizione in corrispondenza del quale vengono trovati i termini.

La trascrizione deve essere nel formato WebVTT. Per altre informazioni, vedere [WebVTT: formato Web Video Text Tracks](https://www.w3.org/TR/webvtt1/).

Aggiungere la definizione del metodo seguente nello spazio dei nomi VideoTranscriptReviews, classe Program. Questo metodo invia una trascrizione al metodo **ContentModeratorClient.TextModeration.ScreenText**. Converte inoltre il risultato in un oggetto IList @ no__t-0TranscriptModerationBodyItem > e viene inviato a **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

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

Aggiungere la definizione del metodo **Main** nello spazio dei nomi VideoTranscriptReviews, classe Program. Chiudere quindi la classe Program e lo spazio dei nomi VideoTranscriptReviews.

> [!NOTE]
> Il programma usa una trascrizione di esempio in formato VTT. In una soluzione reale, si usa il servizio Azure Media Indexer per [generare una trascrizione](https://docs.microsoft.com/azure/media-services/media-services-index-content) da un video.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Passare alla revisione della trascrizione del video

Passare alla revisione della trascrizione del video nello strumento di revisione Content Moderator in **Revisione**>**Video**>**Trascrizione**.

Vengono visualizzate le caratteristiche seguenti:
- Le due righe di trascrizione aggiunte
- Il termine volgare individuato ed evidenziato dal servizio di moderazione del testo
- La selezione di un testo di trascrizione avvia il video da tale timestamp

![Revisione della trascrizione di un video per moderatori umani](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Passaggi successivi

Per questa e altre guide introduttive Content Moderator per .NET, recuperare [Content Moderator SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e la [soluzione Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

Informazioni su come generare [revisioni di video](video-reviews-quickstart-dotnet.md) nello strumento di revisione.

Fare riferimento all'esercitazione dettagliata su come sviluppare una [soluzione completa di moderazione video](video-transcript-moderation-review-tutorial-dotnet.md).
