---
title: 'Avvio rapido: Creare un progetto di rilevamento degli oggetti con Custom Vision SDK per C#'
titlesuffix: Azure Cognitive Services
description: Creare un progetto, aggiungere i tag, caricare le immagini, eseguire il training del progetto e rilevare oggetti usando .NET SDK con C#.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: 1ffe75b088b3e6db5717746ab2fc00ef4b350e8b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971870"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Avvio rapido: Creare un progetto di rilevamento degli oggetti con Custom Vision SDK per .NET

Questo articolo fornisce informazioni e codice di esempio utili per iniziare a usare Custom Vision SDK con C# per compilare un modello di rilevamento degli oggetti. Dopo la creazione, è possibile aggiungere aree con tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio come modello per la creazione di un'applicazione .NET personalizzata. 

## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Ottenere Custom Vision SDK e il codice di esempio
Per scrivere un'app .NET che usa Visione personalizzata saranno necessari i pacchetti NuGet di Visione personalizzata. Questi sono inclusi nel progetto di esempio che si scaricherà, ma è possibile accedervi singolarmente qui.

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clonare o scaricare il progetto [Cognitive Services .NET Samples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Passare alla cartella **CustomVision/ObjectDetection** e aprire _ObjectDetection.csproj_ in Visual Studio.

Questo progetto di Visual Studio crea un nuovo progetto di Visione personalizzata denominato __My New Project__, accessibile tramite il [sito Web di Visione personalizzata](https://customvision.ai/). Carica quindi le immagini per eseguire il training e testare un modello di rilevamento degli oggetti. In questo progetto, il training del modello è stato eseguito in modo da rilevare forchette e forbici nelle immagini.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Informazioni sul codice

Aprire il file _Program.cs_ ed esaminare il codice. Inserire le chiavi di sottoscrizione nelle definizioni appropriate nel metodo **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

### <a name="create-a-new-custom-vision-service-project"></a>Creare un nuovo progetto di Servizio visione artificiale personalizzato

Il frammento di codice successivo crea un progetto di rilevamento di oggetti. Il progetto creato verrà visualizzato nel [sito Web di Visione personalizzata](https://customvision.ai/) visitato in precedenza. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>Aggiungere tag al progetto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate. Il codice seguente associa ognuna delle immagini di esempio alla relativa area contrassegnata.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

Quindi, questa mappa di associazioni viene usata per caricare ogni immagine di esempio con le relative coordinate di area.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

A questo punto, tutte le immagini di esempio sono state caricate e ognuna ha un tag (**fork** oppure **scissors**, forchetta o forbici) e un rettangolo di pixel associato al tag.

### <a name="train-the-project"></a>Eseguire il training del progetto

Questo codice crea la prima iterazione di training nel progetto.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="set-the-current-iteration-as-default"></a>Impostare l'iterazione corrente come predefinita

Questo codice contrassegna l'iterazione corrente come iterazione predefinita. L'iterazione predefinita riflette la versione del modello che risponderà alle richieste di stima. È necessario aggiornarla ogni volta che si ripete il training del modello.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=119-124)]

### <a name="create-a-prediction-endpoint"></a>Creare un endpoint di stima

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=126-131)]

### <a name="use-the-prediction-endpoint"></a>Usare l'endpoint di stima

Questa parte dello script carica l'immagine di test, esegue query sull'endpoint del modello e restituisce i dati di stima nella console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=133-145)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Quando l'applicazione viene eseguita, dovrebbe aprire una finestra della console e scrivere l'output seguente:

```
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```
È quindi possibile verificare che l'immagine di test (disponibile in **Images/Test/**) sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta. A questo punto si può premere un tasto qualsiasi per uscire dall'applicazione.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come eseguire ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida seguente è incentrata sulla classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](test-your-model.md)
