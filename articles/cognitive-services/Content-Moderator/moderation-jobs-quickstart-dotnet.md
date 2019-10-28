---
title: Usare i processi di moderazione con .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare Content Moderator .NET SDK per avviare i processi di moderazione dei contenuti end-to-end per il contenuto di immagini o testo in Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: bdc9e8cbea77b504cfdc2c3b1c83c74ea4ab8ed1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935968"
---
# <a name="define-and-use-moderation-jobs-net"></a>Definire e usare processi di moderazione (.NET)

Un processo di moderazione funge da wrapper per la funzionalità di moderazione del contenuto, flussi di lavoro e revisioni. Questa guida fornisce informazioni ed esempi di codice che consentono di iniziare a usare l' [SDK content moderator per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) per:

- Avviare un processo di moderazione per eseguire analisi e creare revisioni per moderatori umani
- Ottenere lo stato della revisione in sospeso
- Monitorare e ottenere lo stato finale della revisione
- Inviare i risultati della verifica all'URL callback

## <a name="prerequisites"></a>Prerequisiti

- Accedere o creare un account nel sito [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) content moderator.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Verificare che la chiave API possa chiamare l'API di verifica per la creazione della revisione

Dopo aver completato i passaggi precedenti, potrebbero esserci due chiavi Content Moderator se la procedura è stata avviata dal portale di Azure.

Se si prevede di usare la chiave API fornita da Azure nell'esempio di SDK, seguire i passaggi indicati nella sezione [Usare la chiave di Azure con l'API di revisione](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) per consentire all'applicazione di chiamare l'API di revisione e creare revisioni.

Se si usa la chiave di prova gratuita generata dallo strumento di revisione, l'account dello strumento di revisione conosce già la chiave e di conseguenza non sono necessari passaggi aggiuntivi.

## <a name="define-a-custom-moderation-workflow"></a>Definire un flusso di lavoro di moderazione personalizzato

Un processo di moderazione analizza il contenuto usando le API e usa un **flusso di lavoro** per determinare se creare o meno revisioni.
Anche se lo strumento di revisione contiene un flusso di lavoro predefinito, per questa guida introduttiva verrà [definito un flusso di lavoro personalizzato](Review-Tool-User-Guide/Workflows.md).

Usare il nome del flusso di lavoro nel codice che avvia il processo di moderazione.

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

Aggiungere il codice seguente per creare un client di Content Moderator per la sottoscrizione.

> [!IMPORTANT]
> Aggiornare i campi **AzureEndpoint** e **CMSubscriptionKey** con i valori dell'URL dell'endpoint e della chiave di sottoscrizione.

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

### <a name="initialize-application-specific-settings"></a>Inizializzare le impostazioni specifiche dell'applicazione

Aggiungere le costanti e i campi statici seguenti alla classe **Program** in Program.cs.

> [!NOTE]
> Impostare la costante TeamName sul nome usato al momento della creazione della sottoscrizione di Content Moderator. È possibile recuperare il valore di TeamName dal [sito Web di Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> Una volta effettuato l'accesso, selezionare **Credentials** (Credenziali) dal menu **Settings** (Impostazioni) (ingranaggio).
>
> Il nome del team è il valore del campo **ID** nella sezione **API**.

```csharp
/// <summary>
/// The moderation job will use this workflow that you defined earlier.
/// See the quickstart article to learn how to setup custom workflows.
/// </summary>
private const string WorkflowName = "OCR";

/// <summary>
/// The name of the team to assign the job to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "***";

/// <summary>
/// The URL of the image to create a review job for.
/// </summary>
private const string ImageUrl =
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

/// <summary>
/// The name of the log file to create.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Aggiungere il codice per eseguire la moderazione automatica, creare una revisione e ottenere i dettagli del processo

> [!Note]
> In pratica, si imposta il valore di **CallbackEndpoint** dell'URL callback sull'URL che riceve i risultati della revisione manuale (tramite una richiesta HTTP POST).

Iniziare aggiungendo il codice seguente al metodo **Main**.

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
        // See the quickstart article to learn more.
        var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

        // Record the job ID.
        var jobId = jobResult.Result.Body.JobIdProperty;

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
            jobResult.Result.Body, Formatting.Indented));

        Thread.Sleep(2000);
        writer.WriteLine();

        writer.WriteLine("Get review job status.");
        var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
                jobDetails.Result.Body, Formatting.Indented));

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
        Thread.Sleep(latencyDelay * 1000);

        writer.WriteLine("Get review details.");
        jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
        TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
        jobDetails.Result.Body, Formatting.Indented));
    }
    writer.Flush();
    writer.Close();
}
```

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se si supera il limite, l'SDK genera un'eccezione con il codice di errore 429.
>
> Una chiave di livello gratuito prevede un limite di frequenza di richieste al secondo pari a uno.

## <a name="run-the-program-and-review-the-output"></a>Eseguire il programma ed esaminare l'output

Nella console viene visualizzato l'output di esempio seguente:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Accedere allo strumento di revisione di Content Moderator per visualizzare la revisione dell'immagine in sospeso.

Fare clic su **Next** (Avanti) per inviare.

![Revisione di immagini per moderatori umani](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Visualizzare l'output di esempio nel file di log

> [!NOTE]
> Nel file di output le stringhe **Teamname**, **ContentId**, **CallBackEndpoint** e **WorkflowId** riflettono i valori usati in precedenza.

```json
Create moderation job for an image.
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
}

Get review details.
{
    "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
    "TeamName": "some team name",
    "Status": "InProgress",
    "WorkflowId": "OCR",
    "Type": "Image",
    "CallBackEndpoint": "",
    "ReviewId": "",
    "ResultMetaData": [],
    "JobExecutionReport": [
    {
        "Ts": "2018-01-07T00:38:26.7714671",
        "Msg": "Successfully got hasText response from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:26.4181346",
        "Msg": "Getting hasText from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:25.5122828",
        "Msg": "Starting Execution - Try 1"
    }
    ]
}
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>L'URL di callback, se specificato, riceve la risposta

Verrà visualizzata una risposta simile all'esempio seguente:

> [!NOTE]
> Nella risposta di callback le stringhe **ContentId** e **WorkflowId** riflettono i valori usati in precedenza.

```json
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
    "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
    "WorkFlowId": "OCR",
    "Status": "Complete",
    "ContentType": "Image",
    "CallBackType": "Job",
    "ContentId": "contentID",
    "Metadata": {
        "hastext": "True",
        "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
        "imagename": "contentID"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Consultare questa e altre guide introduttive di Content Moderator .NET per usare [Content Moderator SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e la [soluzione Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) prima di iniziare a implementare l'integrazione.
