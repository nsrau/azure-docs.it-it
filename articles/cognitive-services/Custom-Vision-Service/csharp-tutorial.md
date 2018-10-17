---
title: "Esercitazione: Creare un'app Windows per il Servizio visione artificiale personalizzato con C#"
titlesuffix: Azure Cognitive Services
description: Creare un progetto, aggiungere i tag, caricare le immagini, eseguire il training del progetto ed effettuare una stima usando l'endpoint predefinito.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 9e5ed71d4620f7ffeac8acb15f90d67964a86870
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366642"
---
# <a name="tutorial-use-the-custom-vision-service-from-a-c-application"></a>Esercitazione: Usare il Servizio visione artificiale personalizzato da un'applicazione C#

Informazioni su come usare il Servizio visione artificiale personalizzato da un'applicazione C#. Dopo la creazione, è possibile aggiungere tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio open source come modello per la compilazione di un'app per Windows usando l'API Servizio visione artificiale personalizzato.

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di Visual Studio 2017 per Windows.

## <a name="get-the-custom-vision-sdk-and-samples"></a>Ottenere l'SDK e gli esempi di Visione personalizzata
Per compilare questo esempio sono necessari i pacchetti NuGet dell'SDK del Servizio visione artificiale personalizzato:

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

È possibile scaricare le immagini con gli [esempi per C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision).

## <a name="get-the-training-and-prediction-keys"></a>Ottenere le chiavi di training e di stima

Per ottenere le chiavi usate in questo esempio, visitare la [pagina Web di Servizio visione artificiale personalizzato](https://customvision.ai) e selezionare l'__icona a forma di ingranaggio__ in alto a destra. Nella sezione __Account__ copiare i valori dei campi __Training Key__ (Chiave di training) e __Prediction Key__ (Chiave di stima).

![Immagine dell'interfaccia utente delle chiavi](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Informazioni sul codice

In Visual Studio, aprire il progetto che si trova nella directory `Samples/CustomVision.Sample/` del progetto SDK.

Questa applicazione usa la chiave di training recuperata in precedenza per creare un nuovo progetto denominato __Il mio nuovo progetto__. Quindi carica le immagini per eseguire il training e testare un classificatore. Il classificatore indica se un albero è __hemlock__ o un __ciliegio giapponese__.

I frammenti di codice seguenti implementano la funzionalità principale di questo esempio:

* __Creare un nuovo progetto di Servizio visione artificiale personalizzato__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Creare tag in un progetto__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Caricare e contrassegnare immagini__:

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __Training del classificatore__:

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __Impostare un'iterazione predefinita per l'endpoint di stima__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Creare un endpoint di stima__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Inviare un'immagine all'endpoint di stima__:

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

1. Apportare le modifiche seguenti per aggiungere le chiavi di training e di stima all'applicazione:

    * Aggiungere la __chiave di training__ alla riga seguente:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Aggiungere la __chiave di stima__ alla riga seguente:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Eseguire l'applicazione. Quando l'applicazione viene eseguita, nella console viene scritto l'output seguente:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Premere un tasto qualsiasi per uscire dall'applicazione.
