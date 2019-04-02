---
title: "Guida introduttiva: Creare un progetto di rilevamento degli oggetti con l'SDK di Visione personalizzata per Java"
titlesuffix: Azure Cognitive Services
description: Creare un progetto, aggiungere i tag, caricare le immagini, eseguire il training del progetto e rilevare oggetti usando Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 00684df614771437f33655538a808468ee778d29
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487006"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Guida introduttiva: Creare un progetto di rilevamento degli oggetti con l'SDK di Visione personalizzata per Java

Questo articolo fornisce informazioni e codice di esempio utili per iniziare a usare Custom Vision SDK con Java per compilare un modello di rilevamento degli oggetti. Dopo la creazione, è possibile aggiungere aree con tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio come modello per la creazione di un'applicazione Java personalizzata.

## <a name="prerequisites"></a>Prerequisiti

- Un ambiente di sviluppo Java a scelta
- [JDK 7 o 8](https://aka.ms/azure-jdks) installato.
- Maven installato

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Ottenere Custom Vision SDK e il codice di esempio

Per scrivere un'app Java che usa Visione personalizzata saranno necessari i pacchetti maven di Visione personalizzata. Questi sono inclusi nel progetto di esempio che si scaricherà, ma è possibile accedervi singolarmente qui.

È possibile installare Custom Vision SDK dal repository centrale Maven:
- [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clonare o scaricare il progetto [Cognitive Services Java SDK Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Passare alla cartella **Vision/CustomVision/**.

Questo progetto Java crea un nuovo progetto di rilevamento degli oggetti di Visione personalizzata denominato __Sample Java OD Project__, accessibile tramite il [sito Web di Visione personalizzata](https://customvision.ai/). Quindi carica le immagini per eseguire il training e testare un classificatore. In questo progetto il classificatore ha lo scopo di determinare se un albero è un __abete canadese__ o un __ciliegio giapponese__.

[!INCLUDE [get-keys](includes/get-keys.md)]

Il programma è configurato in modo da archiviare i dati chiave come variabili di ambiente. Impostare queste variabili passando alla cartella **Vision/CustomVision** in PowerShell. Quindi immettere i comandi:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Informazioni sul codice

Caricare il progetto `Vision/CustomVision` nell'ambiente IDE Java e aprire il file _CustomVisionSamples.java_. Trovare il metodo **runSample** e impostare come commento la chiamata al metodo **ImageClassification_Sample**. In questo modo viene eseguito lo scenario di classificazione immagini, che non è illustrato in questa guida. Il metodo **ObjectDetection_Sample** implementa la funzionalità principale di questa guida introduttiva. Passare alla sua definizione ed esaminare il codice. 

### <a name="create-a-new-custom-vision-service-project"></a>Creare un nuovo progetto di Servizio visione artificiale personalizzato

Passare al blocco di codice che crea un client di training e un progetto di rilevamento degli oggetti. Il progetto creato verrà visualizzato nel [sito Web di Visione personalizzata](https://customvision.ai/) visitato in precedenza. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>Aggiungere i tag al progetto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate. Passare alla definizione della mappa `regionMap`. Questo codice associa ognuna delle immagini di esempio alla relativa area contrassegnata.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

Passare quindi al blocco di codice che aggiunge le immagini al progetto. Le immagini vengono lette dalla cartella **src/main/resources** del progetto e caricate nel servizio con le coordinate di area e i tag appropriati.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

Il frammento di codice precedente usa due funzioni helper che recuperano le immagini come flussi di risorse e le caricano nel servizio.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project-and-publish"></a>Training del progetto e pubblicazione

Questo codice crea la prima iterazione del progetto e quindi la pubblica nell'endpoint di stima. Il nome assegnato all'iterazione pubblicata può essere usato per inviare le richieste di stima. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>Usare l'endpoint di stima

L'endpoint di stima, qui rappresentato dall'oggetto `predictor`, è il riferimento usato per inviare un'immagine al modello corrente e ottenere una stima di classificazione. In questo esempio `predictor` è definito altrove tramite la variabile di ambiente della chiave di stima.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Per compilare ed eseguire la soluzione tramite maven, eseguire il comando seguente nella directory del progetto in PowerShell:

```powershell
mvn compile exec:java
```

Visualizzare l'output della console per i risultati della registrazione e della stima. È quindi possibile verificare che l'immagine di test sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come eseguire ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida seguente è incentrata sulla classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](test-your-model.md)
