---
title: 'Guida introduttiva: Controllare il contenuto delle immagini in C# - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Come controllare il contenuto delle immagini usando Content Moderator SDK per C#
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: 4973d78eac02aed42689bf5742155c375d5f78ae
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309298"
---
# <a name="quickstart-check-image-content-in-c"></a>Guida introduttiva: Controllare il contenuto delle immagini in C# 

Questo articolo offre informazioni ed esempi di codice per iniziare a usare [Content Moderator SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) per cercare contemporaneamente nelle immagini: 

- contenuto per adulti o audace
- testo estraibile
- visi umani

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-up-for-content-moderator-services"></a>Eseguire la registrazione per i servizi Content Moderator

Per usare i servizi Content Moderator tramite l'API REST o l'SDK, è necessaria una chiave API e l'area dell'account API. Per ottenerle entrambe, effettuare la sottoscrizione al servizio Content Moderator nel [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).

## <a name="create-your-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aggiungere un nuovo progetto **App console (.NET Framework)** alla soluzione.

   Nel codice di esempio denominare il progetto **ImageModeration**.

1. Selezionare questo progetto come progetto di avvio singolo per la soluzione.


### <a name="install-required-packages"></a>Installare i pacchetti necessari

Installare i pacchetti NuGet seguenti:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aggiornare le istruzioni using del programma

Aggiungere le istruzioni `using` seguenti.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Creare il client di Content Moderator

Aggiungere il codice seguente per creare un client di Content Moderator per la sottoscrizione.

> [!IMPORTANT]
> Aggiornare i campi **AzureRegion** e **CMSubscriptionKey** con i valori di identificatore di area e chiave di sottoscrizione.

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
    /// The region/location for your Content Moderator account, 
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

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

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>Inizializzare le impostazioni specifiche dell'applicazione

Aggiungere i campi statici seguenti alla classe **Program** in Program.cs.

```csharp
///<summary>
///The name of the file that contains the image URLs to evaluate.
///</summary>
///<remarks>You will need to create an input file and update 
///this path accordingly. Paths are relative to the execution directory.
///</remarks>
private static string ImageUrlFile = "ImageFiles.txt";

///<summary>
///The name of the file to contain the output from the evaluation.
///</summary>
///<remarks>Paths are relative to the execution directory.
///</remarks>
private static string OutputFile = "ModerationOutput.json";
```

Creare il file di input _ImageFiles.txt_ e aggiungere gli URL delle immagini da analizzare. Per generare l'output di esempio, questa guida introduttiva usa i due URL seguenti.
- https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
- https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png

## <a name="create-a-class-to-handle-results"></a>Creare una classe per gestire i risultati

Aggiungere la classe seguente alla classe **Program**. Per registrare i risultati della moderazione per ognuna delle immagini riviste, si userà un'istanza di questa classe.

```csharp
/// <summary>
/// Contains the image moderation results for an image, 
/// including text and face detection results.
/// </summary>
public class EvaluationData
{
    /// <summary>
    /// The URL of the evaluated image.
    /// </summary>
    public string ImageUrl;

    /// <summary>
    /// The image moderation results.
    /// </summary>
    public Evaluate ImageModeration;

    /// <summary>
    /// The text detection results.
    /// </summary>
    public OCR TextDetection;

    /// <summary>
    /// The face detection results;
    /// </summary>
    public FoundFaces FaceDetection;
}
```

## <a name="create-the-image-evaluation-method"></a>Creare il metodo di valutazione delle immagini

Aggiungere il metodo seguente alla classe **Program**. Questo metodo valuta una singola immagine e restituisce il risultato della valutazione.

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se questo limite viene superato, l'SDK genera un'eccezione con il codice di errore 429. Una chiave di livello gratuito prevede un limite di frequenza RPS pari a uno.

```csharp
/// <summary>
/// Evaluates an image using the Image Moderation APIs.
/// </summary>
/// <param name="client">The Content Moderator API wrapper to use.</param>
/// <param name="imageUrl">The URL of the image to evaluate.</param>
/// <returns>Aggregated image moderation results for the image.</returns>
/// <remarks>This method throttles calls to the API.
/// Your Content Moderator service key will have a requests per second (RPS)
/// rate limit, and the SDK will throw an exception with a 429 error code 
/// if you exceed that limit. A free tier key has a 1 RPS rate limit.
/// </remarks>
private static EvaluationData EvaluateImage(
  ContentModeratorClient client, string imageUrl)
{
    var url = new ImageUrl("URL", imageUrl.Trim());

    var imageData = new EvaluationData();

    imageData.ImageUrl = url.Value;

  // Evaluate for adult and racy content.
    imageData.ImageModeration =
        client.ImageModeration.EvaluateUrlInput("application/json", url, true);
    Thread.Sleep(1000);

    // Detect and extract text.
    imageData.TextDetection =
        client.ImageModeration.OCRUrlInput("eng", "application/json", url, true);
    Thread.Sleep(1000);

    // Detect faces.
    imageData.FaceDetection =
        client.ImageModeration.FindFacesUrlInput("application/json", url, true);
    Thread.Sleep(1000);

    return imageData;
}
```

Il metodo **EvaluateUrlInput** è un wrapper per l'API REST Moderazione immagini.
Il valore restituito contiene l'oggetto restituito dalla chiamata all'API.

Il metodo **OCRUrlInput** è un wrapper per l'API REST Image OCR.
Il valore restituito contiene l'oggetto restituito dalla chiamata all'API.

Il metodo **FindFacesUrlInput** è un wrapper per l'API REST Image Find Faces.
Il valore restituito contiene l'oggetto restituito dalla chiamata all'API.

## <a name="evaluate-the-images-in-your-code"></a>Valutare le immagini nel codice

Aggiungere il codice seguente al metodo **Main**.

```csharp
// Create an object to store the image moderation results.
List<EvaluationData> evaluationData = new List<EvaluationData>();

// Create an instance of the Content Moderator API wrapper.
using (var client = Clients.NewClient())
{
    // Read image URLs from the input file and evaluate each one.
    using (StreamReader inputReader = new StreamReader(ImageUrlFile))
    {
        while (!inputReader.EndOfStream)
        {
            string line = inputReader.ReadLine().Trim();
            if (line != String.Empty)
            {
                EvaluationData imageData = EvaluateImage(client, line);
                evaluationData.Add(imageData);
            }
        }
    }
}

// Save the moderation results to a file.
using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
{
    outputWriter.WriteLine(JsonConvert.SerializeObject(
        evaluationData, Formatting.Indented));

    outputWriter.Flush();
    outputWriter.Close();
}
```

## <a name="run-the-program-and-review-the-output"></a>Eseguire il programma ed esaminare l'output

Aprire il file _ModerationOutput.json_ per visualizzare il contenuto di output. Il contenuto dovrebbe essere simile a quello riportato di seguito. Si noti che per ogni immagine sono presenti sezioni `ImageModeration`, `FaceDetection` e `TextDetection` diverse, che corrispondono alle tre chiamate API del metodo **EvaluateImage**.

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps---get-the-source-code"></a>Passaggi successivi: ottenere il codice sorgente

Consultare questa e altre guide introduttive di Content Moderator .NET per usare [Content Moderator SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e la [soluzione Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) prima di iniziare a implementare l'integrazione.
