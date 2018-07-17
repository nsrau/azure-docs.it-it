---
title: Content Moderator di Azure - Avviare i processi di moderazione con .NET | Microsoft Docs
description: Come avviare i processi di moderazione usando Azure Content Moderator SDK per .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373153"
---
# <a name="start-moderation-jobs-using-net"></a>Avviare i processi di moderazione usando .NET

Questo articolo contiene informazioni ed esempi di codice per iniziare a usare Content Moderator SDK per .NET allo scopo di:
 
- Avviare un processo di moderazione per eseguire analisi e creare revisioni per moderatori umani
- Ottenere lo stato della revisione in sospeso
- Monitorare e ottenere lo stato finale della revisione
- Inviare il risultato all'URL callback

Questo articolo presuppone che si abbia già familiarità con Visual Studio e C#.

## <a name="sign-up-for-content-moderator-services"></a>Eseguire la registrazione per i servizi Content Moderator

Per usare i servizi Content Moderator tramite l'API REST o l'SDK, è necessaria una chiave di sottoscrizione.
Vedere la [guida introduttiva](quick-start.md) per informazioni su come ottenere la chiave.

## <a name="define-a-custom-moderation-workflow"></a>Definire un flusso di lavoro di moderazione personalizzato

Un processo di moderazione analizza il contenuto usando le API e usa un **flusso di lavoro** per determinare se creare o meno revisioni.
Anche se lo strumento di revisione contiene un flusso di lavoro predefinito, per questa guida introduttiva verrà [definito un flusso di lavoro personalizzato](Review-Tool-User-Guide/Workflows.md).

Usare il nome del flusso di lavoro nel codice che avvia il processo di moderazione.

## <a name="create-your-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aggiungere un nuovo progetto **App console (.NET Framework)** alla soluzione.

   Nel codice di esempio assegnare al progetto il nome **CreateReviews**.

1. Selezionare questo progetto come progetto di avvio singolo per la soluzione.

1. Aggiungere un riferimento all'assembly del progetto **ModeratorHelper** creato nella [guida introduttiva all'helper client di Content Moderator](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Installare i pacchetti necessari

Installare i pacchetti NuGet seguenti:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aggiornare le istruzioni using del programma

Modificare le istruzioni using del programma.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>Inizializzare le impostazioni specifiche dell'applicazione

Aggiungere le costanti e i campi statici seguenti alla classe **Program** in Program.cs.

> [!NOTE]
> Impostare la costante TeamName sul nome usato al momento della creazione della sottoscrizione di Content Moderator. È possibile recuperare il valore di TeamName dal [sito Web di Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> Una volta effettuato l'accesso, selezionare **Credentials** (Credenziali) dal menu **Settings** (Impostazioni) (ingranaggio).
>
> Il nome del team è il valore del campo **ID** nella sezione **API**.


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
    /// the Conent Moderator web site. Your team name is the Id associated 
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
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Aggiungere il codice per eseguire la moderazione automatica, creare una revisione e ottenere i dettagli del processo

> [!Note]
> In pratica, si imposta il valore di **CallbackEndpoint** dell'URL callback sull'URL che riceve i risultati della revisione manuale (tramite una richiesta HTTP POST).

Iniziare aggiungendo il codice seguente al metodo **Main**.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
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

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se si supera il limite, l'SDK genera un'eccezione con il codice di errore 429. 
>
> Una chiave di livello gratuito prevede un unico limite di frequenza RPS.

## <a name="run-the-program-and-review-the-output"></a>Eseguire il programma ed esaminare l'output

Nella console viene visualizzato l'output di esempio seguente:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Accedere allo strumento di revisione di Content Moderator per visualizzare la revisione dell'immagine in sospeso.

Fare clic su **Avanti** per inviare.

![Revisione di immagini per moderatori umani](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Visualizzare l'output di esempio nel file di log

> [!NOTE]
> Nel file di output le stringhe **Teamname**, **ContentId**, **CallBackEndpoint** e **WorkflowId** riflettono i valori usati in precedenza.

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


## <a name="your-callback-url-if-provided-receives-this-response"></a>L'URL callback, se fornito, riceve la risposta.

Verrà visualizzata una risposta simile all'esempio seguente:

> [!NOTE]
> Nella risposta di callback le stringhe **ContentId** e **WorkflowId** riflettono i valori usati in precedenza.

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


## <a name="next-steps"></a>Passaggi successivi

[Scaricare la soluzione Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) per questa e altre guide introduttive di Content Moderator per .NET e iniziare a implementare l'integrazione.
