---
title: Creare revisioni usando .NET - Content Moderator
titleSuffix: Azure Cognitive Services
description: Come creare revisioni usando Content Moderator SDK di Azure per .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 38e4d33d453bae4e6dbb5b87ae9a5023cc8aa671
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931703"
---
# <a name="create-human-reviews-net"></a>Creare recensioni umane (.NET)

Esamina l'archivio e visualizza il contenuto per i moderatori umani da valutare. Quando un utente completa una revisione, i risultati vengono inviati a un endpoint di callback specificato. Questa guida fornisce informazioni ed esempi di codice che consentono di iniziare a usare l' [SDK content moderator per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) per:

- Creare un set di revisioni per moderatori umani
- Ottenere lo stato delle revisioni esistenti per i moderatori umani

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) content moderator.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Verificare che la chiave API possa chiamare l'API di verifica per la creazione della revisione

Dopo aver completato i passaggi precedenti, potrebbero esserci due chiavi Content Moderator se la procedura è stata avviata dal portale di Azure.

Se si prevede di usare la chiave API fornita da Azure nell'esempio di SDK, seguire i passaggi indicati nella sezione [Usare la chiave di Azure con l'API di revisione](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) per consentire all'applicazione di chiamare l'API di revisione e creare revisioni.

Se si usa la chiave di prova gratuita generata dallo strumento di revisione, l'account dello strumento di revisione conosce già la chiave e di conseguenza non sono necessari passaggi aggiuntivi.

## <a name="create-your-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aggiungere un nuovo progetto **App console (.NET Framework)** alla soluzione.

   Nel codice di esempio assegnare al progetto il nome **CreateReviews**.

1. Selezionare il progetto come progetto di avvio singolo per la soluzione.

### <a name="install-required-packages"></a>Installare i pacchetti necessari

Installare i pacchetti NuGet seguenti:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aggiornare le istruzioni using del programma

Modificare le istruzioni using del programma.


```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Creare il client di Content Moderator

Aggiungere il codice seguente per creare un client di Content Moderator per la sottoscrizione. Aggiornare i campi `AzureEndpoint` e `CMSubscriptionKey` con i valori dell'URL dell'endpoint e della chiave di sottoscrizione. È possibile trovarli nella scheda **avvio rapido** della risorsa nel portale di Azure.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Creare una classe per associare le informazioni sul contenuto interno a un ID revisione

Aggiungere la classe seguente alla classe **Program**. Usare questa classe per associare l'ID di revisione all'ID contenuto interno per l'elemento.

```csharp
/// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }
```

### <a name="initialize-application-specific-settings"></a>Inizializzare le impostazioni specifiche dell'applicazione

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se questo limite viene superato, l'SDK genera un'eccezione con un codice di errore 429.
>
> Una chiave di livello gratuito prevede un limite di frequenza di richieste al secondo pari a uno.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Aggiungere le costanti seguenti alla classe **Program** in Program.cs

```csharp
/// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";
```

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Aggiungere le costanti e i campi statici seguenti alla classe **Program** in Program.cs

Aggiornare questi valori in modo che contengano informazioni specifiche per la sottoscrizione e i team.

> [!NOTE]
> Impostare la costante TeamName sul nome usato al momento della creazione della sottoscrizione dello [strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/). La costante TeamName può essere recuperata nella sezione **Credentials** (Credenziali) nel menu **Settings** (Impostazioni), ovvero l'icona a forma di ingranaggio.
>
> Il nome del team è il valore del campo **ID** nella sezione **API**.

```csharp
/// <summary>
/// The name of the team to assign the review to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "YOUR REVIEW TEAM ID";

/// <summary>
/// The optional name of the subteam to assign the review to.
/// </summary>
private const string Subteam = null;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team. 
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "YOUR API ENDPOINT";

/// <summary>
/// The media type for the item to review.
/// </summary>
/// <remarks>Valid values are "image", "text", and "video".</remarks>
private const string MediaType = "image";

/// <summary>
/// The URLs of the images to create review jobs for.
/// </summary>
private static readonly string[] ImageUrls = new string[] {
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
    // add more if you want
};

/// <summary>
/// The metadata key to initially add to each review item.
/// </summary>
private const string MetadataKey = "sc";

/// <summary>
/// The metadata value to initially add to each review item.
/// </summary>
private const string MetadataValue = "true";
```

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Aggiungere i campi statici seguenti alla classe **Program** in Program.cs

Usare questi campi per tenere traccia dello stato dell'applicazione.

```csharp
/// <summary>
/// A static reference to the text writer to use for logging.
/// </summary>
private static TextWriter writer;

/// <summary>
/// The cached review information, associating a local content ID
/// to the created review ID for each item.
/// </summary>
private static List<ReviewItem> reviewItems =
    new List<ReviewItem>();
```

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Creare un metodo per scrivere messaggi nel file di log

Aggiungere il metodo seguente alla classe **Program**.

```csharp
/// <summary>
/// Writes a message to the log file, and optionally to the console.
/// </summary>
/// <param name="message">The message.</param>
/// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
private static void WriteLine(string message = null, bool echo = false)
{
    writer.WriteLine(message ?? String.Empty);

    if (echo)
    {
        Console.WriteLine(message ?? String.Empty);
    }
}
```

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Creare un metodo per creare un set di revisioni

Per identificare immagini, testo o video in arrivo da esaminare, si dispone in genere di logica di business. In questo caso usare semplicemente un elenco fisso di immagini.

Aggiungere il metodo seguente alla classe **Program**.

```csharp
/// <summary>
/// Create the reviews using the fixed list of images.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
private static void CreateReviews(ContentModeratorClient client)
{
    WriteLine(null, true);
    WriteLine("Creating reviews for the following images:", true);

    // Create the structure to hold the request body information.
    List<CreateReviewBodyItem> requestInfo =
        new List<CreateReviewBodyItem>();

    // Create some standard metadata to add to each item.
    List<CreateReviewBodyItemMetadataItem> metadata =
        new List<CreateReviewBodyItemMetadataItem>(
        new CreateReviewBodyItemMetadataItem[] {
            new CreateReviewBodyItemMetadataItem(
                MetadataKey, MetadataValue)
        });

    // Populate the request body information and the initial cached review information.
    for (int i = 0; i < ImageUrls.Length; i++)
    {
        // Cache the local information with which to create the review.
        var itemInfo = new ReviewItem()
        {
            Type = MediaType,
            ContentId = i.ToString(),
            Url = ImageUrls[i],
            ReviewId = null
        };

        WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

        // Add the item informaton to the request information.
        requestInfo.Add(new CreateReviewBodyItem(
            itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
            CallbackEndpoint, metadata));

        // Cache the review creation information.
        reviewItems.Add(itemInfo);
    }

    var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
        "application/json", TeamName, requestInfo);

    // Update the local cache to associate the created review IDs with
    // the associated content.
    var reviewIds = reviewResponse.Result.Body;
    for (int i = 0; i < reviewIds.Count; i++)
    {
        Program.reviewItems[i].ReviewId = reviewIds[i];
    }

    WriteLine(JsonConvert.SerializeObject(
    reviewIds, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Creare un metodo per ottenere lo stato delle revisioni esistenti

Aggiungere il metodo seguente alla classe **Program**.

> [!Note]
> Impostare in pratica l'URL di callback `CallbackEndpoint` sull'URL che riceve i risultati della revisione manuale (tramite una richiesta HTTP POST). È possibile modificare questo metodo per verificare lo stato delle revisioni in sospeso.

```csharp
/// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }
```

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Aggiungere codice per creare un set di revisioni e verificarne lo stato

Aggiungere il codice seguente al metodo **Main**.

Questo codice simula molte operazioni eseguite nella definizione e nella gestione dell'elenco nonché nell'uso dell'elenco per filtrare le immagini. Le funzionalità di registrazione consentono di visualizzare gli oggetti risposta generati dalle chiamate SDK al servizio Content mModerator.

```csharp
using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
{
    writer = outputWriter;
    using (var client = Clients.NewClient())
    {
        CreateReviews(client);
        GetReviewDetails(client);

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
        Thread.Sleep(latencyDelay * 1000);

        GetReviewDetails(client);
    }

    writer = null;
    outputWriter.Flush();
    outputWriter.Close();
}

Console.WriteLine();
Console.WriteLine("Press any key to exit...");
Console.ReadKey();
```

## <a name="run-the-program-and-review-the-output"></a>Eseguire il programma ed esaminare l'output

Viene visualizzato l'output di esempio seguente:

```console
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
```

Accedere allo strumento di revisione di Content Moderator per visualizzare la revisione delle immagini in sospeso con l'etichetta **sc** impostata su **true**. Vengono anche visualizzati i tag **a** e **r** predefiniti e tutti i tag personalizzati definiti nello strumento di revisione.

Fare clic su **Next** (Avanti) per inviare.

![Revisione di immagini per moderatori umani](images/moderation-reviews-quickstart-dotnet.PNG)

Premere quindi un tasto qualsiasi per continuare.

```console
Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...
```

## <a name="check-out-the-following-output-in-the-log-file"></a>Controllare l'output seguente nel file di log

> [!NOTE]
> Nel file di output le stringhe "\{teamname}" e "\{callbackUrl}" riflettono i valori per i campi `TeamName` e `CallbackEndpoint`, rispettivamente.

Gli ID revisione e gli URL di contenuto dell'immagine sono diversi ogni volta che si esegue l'applicazione e quando una revisione è completata il campo `reviewerResultTags` riflette il modo in cui il revisore ha contrassegnato l'elemento.

```json
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
              "key": "a",
              "value": "False"
        },
        {
              "key": "r",
              "value": "True"
        },
        {
              "key": "sc",
              "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>L'URL di callback, se specificato, riceve la risposta

Verrà visualizzata una risposta simile all'esempio seguente:

```json
{
    "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
    "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
    "ModifiedBy": "yourusername",
    "CallBackType": "Review",
    "ContentId": "0",
    "ContentType": "Image",
    "Metadata": {
        "sc": "true"
        },
    "ReviewerResultTags": {
        "a": "False",
        "r": "False",
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Ottenere il [content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e scaricare la [soluzione di Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) per questa e altre guide introduttive content moderator per .NET e iniziare a usare l'integrazione.