---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 9536670f8529da3a4251596b2c9642e50445fc0e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91989581"
---
Introduzione alla libreria client di Visione personalizzata per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per creare un modello di rilevamento oggetti. Si creerà un progetto, si aggiungeranno i tag, si eseguirà il training del progetto con le immagini di esempio e si userà l'URL dell'endpoint di previsione del progetto per testarlo a livello di codice. Usare questo esempio come modello per la creazione di un'app di riconoscimento immagini personalizzata.

> [!NOTE]
> Se si intende creare un modello di rilevamento oggetti ed eseguirne il training _senza_ scrivere codice, vedere invece le [indicazioni basate sul browser](../../get-started-build-detector.md).

Usare la libreria client di Visione personalizzata per .NET per:

* Creare un nuovo progetto Visione personalizzata
* Aggiungere tag al progetto
* Caricare e contrassegnare le immagini
* Eseguire il training del progetto
* Testare l'endpoint di stima

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet) | [Codice sorgente della libreria (training)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(previsione)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Pacchetto (NuGet) [(training)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(previsione)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [Esempi](https://docs.microsoft.com/samples/browse/?products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [IDE di Visual Studio](https://visualstudio.microsoft.com/vs/) o la versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="creare una risorsa Visione personalizzata"  target="_blank">creare una risorsa Visione personalizzata <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per creare la risorsa di training e di previsione e ottenere le chiavi e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint delle risorse create sono necessari per connettere l'applicazione a Visione personalizzata. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Creare un'applicazione .NET Core con Visual Studio. 

### <a name="install-the-client-library"></a>Installare la libreria client 

Dopo aver creato un nuovo progetto, installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione del progetto in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia**, **Includi versione preliminare** e cercare `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` e `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction`. Selezionare la versione più recente e fare clic su **Installa**. 

#### <a name="cli"></a>[CLI](#tab/cli)

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `custom-vision-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un unico file di origine: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installare la libreria client 

Nella directory dell'applicazione installare la libreria client di Visione personalizzata per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs), che contiene gli esempi di codice di questo argomento.

Dalla directory del progetto aprire il file *program.cs* e aggiungere le direttive `using` seguenti:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

Nel metodo **Main** dell'applicazione creare le variabili per l'endpoint e la chiave della risorsa.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Accedere al portale di Azure. Se le risorse Visione personalizzata create nella sezione **Prerequisiti** sono state distribuite correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. Le chiavi e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. Sarà necessario recuperare sia la chiave di training che la chiave di previsione.
>
> Al termine, ricordarsi di rimuovere le chiavi dal codice e di non renderle mai pubbliche. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) di Servizi cognitivi.

Nel metodo **Main** dell'applicazione aggiungere le chiamate per i metodi usati in questa guida di avvio rapido. Queste verranno implementate in un secondo momento.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Modello a oggetti

|Nome|Description|
|---|---|
|[CustomVisionTrainingClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-dotnet) | Questa classe gestisce la creazione, il training e la pubblicazione dei modelli. |
|[CustomVisionPredictionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)| Questa classe gestisce l'esecuzione di query sui modelli per le previsioni di rilevamento oggetti.|
|[PredictionModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet-preview)| Questa classe definisce una singola previsione di oggetto in una singola immagine. Include le proprietà per l'ID e il nome dell'oggetto, la posizione del rettangolo di delimitazione dell'oggetto e un punteggio di attendibilità.|

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione personalizzata per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Creare un nuovo progetto Visione personalizzata](#create-a-new-custom-vision-project)
* [Aggiungere tag al progetto](#add-tags-to-the-project)
* [Caricare e contrassegnare le immagini](#upload-and-tag-images)
* [Eseguire il training del progetto](#train-the-project)
* [Pubblicare l'iterazione corrente](#publish-the-current-iteration)
* [Testare l'endpoint di stima](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticare il client

In un nuovo metodo, creare un'istanza dei client di training e di previsione usando l'endpoint e le chiavi.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Creare un nuovo progetto Visione personalizzata

Il frammento di codice successivo crea un progetto di rilevamento di oggetti. Il progetto creato verrà visualizzato nel [sito Web di Visione personalizzata](https://customvision.ai/). Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un rilevatore](../../get-started-build-detector.md) nella guida al portale Web, vedere il metodo [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Aggiungere tag al progetto

Questo metodo definisce i tag su cui si eseguirà il training del modello.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Scaricare prima di tutto le immagini di esempio per questo progetto. Salvare il contenuto della [cartella immagini di esempio](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) nel dispositivo locale.

Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate. Il codice seguente associa ognuna delle immagini di esempio alla relativa area contrassegnata.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> Se per i progetti personali non è disponibile un'utilità di clic e trascinamento per contrassegnare le coordinate delle aree, è possibile usare l'interfaccia utente Web nel [sito Web di Visione personalizzata](https://www.customvision.ai/). In questo esempio le coordinate sono già disponibili.

Quindi, questa mappa di associazioni viene usata per caricare ogni immagine di esempio con le relative coordinate di area. È possibile caricare fino a 64 immagini in un singolo batch. Potrebbe essere necessario modificare il valore di `imagePath` in modo che punti ai percorsi di cartella corretti.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

A questo punto, tutte le immagini di esempio sono state caricate e sono stati aggiunti i tag a ogni immagine (**forchetta** o **forbici**) con un rettangolo di pixel associato.

## <a name="train-the-project"></a>Eseguire il training del progetto

Questo metodo crea la prima iterazione di training nel progetto. Esegue una query sul servizio fino al completamento del training.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Eseguire il training con i tag selezionati
>
> Facoltativamente, è possibile eseguire il training solo su un subset dei tag applicati. Questa operazione può essere eseguita se non è ancora stato applicato un numero sufficiente di determinati tag, ma si dispone di un numero sufficiente di altri. Nella chiamata [TrainProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) usare il parametro *trainingParameters*. Costruire un [TrainingParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?view=azure-dotnet&preserve-view=true) e impostare la relativa proprietà **SelectedTags** su un elenco di ID dei tag che si intende usare. Il modello eseguirà il training in modo da riconoscere solo i tag nell'elenco.

## <a name="publish-the-current-iteration"></a>Pubblicare l'iterazione corrente

Questo metodo rende disponibile l'iterazione corrente del modello per l'esecuzione di query. È possibile usare il nome del modello come riferimento per inviare richieste di previsione. È necessario immettere il valore corrente per `predictionResourceId`. È possibile trovare l'ID della risorsa di previsione nella scheda **Panoramica** della risorsa nel portale di Azure, indicato come **ID sottoscrizione**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testare l'endpoint di stima

Questa parte dello script carica l'immagine di test, esegue query sull'endpoint del modello e restituisce i dati di stima nella console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Eseguire l'applicazione

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Eseguire l'applicazione facendo clic sul pulsante **Debug** nella parte superiore della finestra dell'ambiente di sviluppo integrato.

#### <a name="cli"></a>[CLI](#tab/cli)

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

---

Quando l'applicazione viene eseguita, dovrebbe aprire una finestra della console e scrivere l'output seguente:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

È quindi possibile verificare che l'immagine di test (disponibile in **Images/Test/**) sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta. A questo punto si può premere un tasto qualsiasi per uscire dall'applicazione.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato eseguito ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida seguente è incentrata sulla classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)

* Informazioni su Visione personalizzata
* [Documentazione di riferimento sull'SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)