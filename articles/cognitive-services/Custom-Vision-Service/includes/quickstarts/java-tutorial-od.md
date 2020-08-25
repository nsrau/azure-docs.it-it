---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: a822ab52024a801f4443a9dd864b4b96ec52d000
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511313"
---
Questo articolo illustra come iniziare a usare la libreria client di Visione personalizzata con Java per creare un modello di rilevamento oggetti. Dopo la creazione, è possibile aggiungere aree con tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio come modello per la creazione di un'applicazione Java personalizzata.

## <a name="prerequisites"></a>Prerequisiti

- Un ambiente di sviluppo Java a scelta
- [JDK 7 o 8](https://aka.ms/azure-jdks) installato.
- [Maven](https://maven.apache.org/) installato
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Ottenere la libreria client di Visione personalizzata e il codice di esempio

Per scrivere un'app Java che usa Visione personalizzata saranno necessari i pacchetti maven di Visione personalizzata. Questi pacchetti sono inclusi nel progetto di esempio che si scaricherà, ma è possibile accedervi singolarmente qui.

È possibile trovare la libreria client di Visione personalizzata nel repository centrale Maven:
- [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clonare o scaricare il progetto [Cognitive Services Java SDK Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Passare alla cartella **Vision/CustomVision/** .

Questo progetto Java crea un nuovo progetto di rilevamento degli oggetti di Visione personalizzata denominato __Sample Java OD Project__, accessibile tramite il [sito Web di Visione personalizzata](https://customvision.ai/). Quindi carica le immagini per eseguire il training e testare un classificatore. In questo progetto il classificatore ha lo scopo di determinare se un oggetto è una **forchetta** o un paio di **forbici**.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

Il programma è configurato in modo da fare riferimento ai dati chiave come variabili di ambiente. Passare alla cartella **Vision/CustomVision** e immettere i comandi di PowerShell seguenti per impostare le variabili di ambiente. 

> [!NOTE]
> Se si usa un sistema operativo non Windows, vedere [Configurare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) per istruzioni.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Informazioni sul codice

Caricare il progetto `Vision/CustomVision` nell'ambiente IDE Java e aprire il file _CustomVisionSamples.java_. Trovare il metodo **runSample** e impostare come commento la chiamata al metodo **ImageClassification_Sample**. Questo metodo esegue lo scenario di classificazione immagini, che non è illustrato in questa guida. Il metodo **ObjectDetection_Sample** implementa la funzionalità principale di questa guida introduttiva. Passare alla sua definizione ed esaminare il codice. 

### <a name="create-a-new-custom-vision-service-project"></a>Creare un nuovo progetto di Servizio visione artificiale personalizzato

Passare al blocco di codice che crea un client di training e un progetto di rilevamento degli oggetti. Il progetto creato verrà visualizzato nel [sito Web di Visione personalizzata](https://customvision.ai/) visitato in precedenza. Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un rilevatore](../../get-started-build-detector.md) nella guida al portale Web, vedere gli overload del metodo [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Aggiungere i tag al progetto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate. Passare alla definizione della mappa `regionMap`. Questo codice associa ognuna delle immagini di esempio alla relativa area contrassegnata.

> [!NOTE]
> Se non si ha un'utilità di clic e trascinamento per contrassegnare le coordinate delle aree, è possibile usare l'interfaccia utente Web in [Customvision.ai](https://www.customvision.ai/). In questo esempio le coordinate sono già disponibili.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Passare quindi al blocco di codice che aggiunge le immagini al progetto. Le immagini vengono lette dalla cartella **src/main/resources** del progetto e caricate nel servizio con le coordinate di area e i tag appropriati.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Il frammento di codice precedente usa due funzioni helper che recuperano le immagini come flussi di risorse e le caricano nel servizio. È possibile caricare fino a 64 immagini in un singolo batch.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Training del progetto e pubblicazione

Questo codice crea la prima iterazione del modello di previsione e quindi la pubblica nell'endpoint di previsione. Il nome assegnato all'iterazione pubblicata può essere usato per inviare le richieste di stima. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Usare l'endpoint di stima

L'endpoint di stima, qui rappresentato dall'oggetto `predictor`, è il riferimento usato per inviare un'immagine al modello corrente e ottenere una stima di classificazione. In questo esempio `predictor` è definito altrove tramite la variabile di ambiente della chiave di stima.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Per compilare ed eseguire la soluzione con Maven, passare alla directory del progetto (**Vision/CustomVision**) in un prompt dei comandi ed eseguire il comando run:

```bash
mvn compile exec:java
```

Visualizzare l'output della console per i risultati della registrazione e della stima. È quindi possibile verificare che l'immagine di test sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come è possibile eseguire ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida di formazione seguente riguarda la classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)
