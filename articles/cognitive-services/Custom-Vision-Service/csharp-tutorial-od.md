---
title: "Guida introduttiva: Creare un progetto di rilevamento degli oggetti con l'SDK per C# - Visione personalizzata"
titleSuffix: Azure Cognitive Services
description: Creare un progetto, aggiungere i tag, caricare le immagini, eseguire il training del progetto e rilevare oggetti usando .NET SDK con C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 7baa3e2de00997496edb5b445f8426d899ac65a4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383714"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Guida introduttiva: Creare un progetto di rilevamento degli oggetti con Custom Vision SDK per .NET

Questo articolo fornisce informazioni e codice di esempio utili per iniziare a usare Custom Vision SDK con C# per compilare un modello di rilevamento degli oggetti. Dopo la creazione, è possibile aggiungere aree con tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio come modello per la creazione di un'applicazione .NET personalizzata. 

## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Ottenere Custom Vision SDK e il codice di esempio

Per scrivere un'app .NET che usa Visione personalizzata saranno necessari i pacchetti NuGet di Visione personalizzata. Questi pacchetti sono inclusi nel progetto di esempio che si scaricherà, ma è possibile accedervi singolarmente qui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonare o scaricare il progetto [Cognitive Services .NET Samples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Passare alla cartella **CustomVision/ObjectDetection** e aprire _ObjectDetection.csproj_ in Visual Studio.

Questo progetto di Visual Studio crea un nuovo progetto di Visione personalizzata denominato __My New Project__, accessibile tramite il [sito Web di Visione personalizzata](https://customvision.ai/). Carica quindi le immagini per eseguire il training e testare un modello di rilevamento degli oggetti. In questo progetto, il training del modello è stato eseguito in modo da rilevare forchette e forbici nelle immagini.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Informazioni sul codice

Aprire il file _Program.cs_ ed esaminare il codice. [Creare variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per le chiavi di training e di stima denominate rispettivamente `CUSTOM_VISION_TRAINING_KEY` e `CUSTOM_VISION_PREDICTION_KEY`. Lo script eseguirà la ricerca.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Inoltre, ottenere l'URL dell'endpoint dalla pagina Impostazioni del sito Visione personalizzata. Salvarlo in una variabile di ambiente denominata `CUSTOM_VISION_ENDPOINT`. Lo script salva un riferimento nella radice della classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Creare un nuovo progetto di Servizio visione artificiale personalizzato

Il frammento di codice successivo crea un progetto di rilevamento di oggetti. Il progetto creato verrà visualizzato nel [sito Web di Visione personalizzata](https://customvision.ai/) visitato in precedenza. Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un rilevatore](get-started-build-detector.md) nella guida al portale Web, vedere il metodo [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Aggiungere tag al progetto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate. Il codice seguente associa ognuna delle immagini di esempio alla relativa area contrassegnata.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Quindi, questa mappa di associazioni viene usata per caricare ogni immagine di esempio con le relative coordinate di area. È possibile caricare fino a 64 immagini in un singolo batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

A questo punto, tutte le immagini di esempio sono state caricate e ognuna ha un tag (**fork** oppure **scissors**, forchetta o forbici) e un rettangolo di pixel associato al tag.

### <a name="train-the-project"></a>Eseguire il training del progetto

Questo codice crea la prima iterazione di training nel progetto.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Pubblicare l'iterazione corrente

Il nome assegnato all'iterazione pubblicata può essere usato per inviare le richieste di stima. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Creare un endpoint di stima

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Usare l'endpoint di stima

Questa parte dello script carica l'immagine di test, esegue query sull'endpoint del modello e restituisce i dati di stima nella console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Quando l'applicazione viene eseguita, dovrebbe aprire una finestra della console e scrivere l'output seguente:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

È quindi possibile verificare che l'immagine di test (disponibile in **Images/Test/** ) sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta. A questo punto si può premere un tasto qualsiasi per uscire dall'applicazione.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come eseguire ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida seguente è incentrata sulla classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](test-your-model.md)
