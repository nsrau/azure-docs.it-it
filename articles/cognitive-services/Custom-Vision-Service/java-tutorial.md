---
title: 'Esercitazione: Creare un progetto di classificazione immagini con Custom Vision SDK per Java'
titlesuffix: Azure Cognitive Services
description: Creare un progetto, aggiungere i tag, caricare le immagini, eseguire il training del progetto ed effettuare una stima usando l'endpoint predefinito.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: d02dced071594732978f961c1b9d8effcd2e35ab
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415620"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Esercitazione: Creare un progetto di classificazione immagini con Custom Vision SDK per Java

Informazioni su come creare un progetto di classificazione immagini con il Servizio visione artificiale personalizzato e Java. Dopo la creazione, è possibile aggiungere tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio open source come modello per la compilazione di un'app usando l'API Visione personalizzata.

## <a name="prerequisites"></a>Prerequisiti

- [JDK 7 o 8](https://aka.ms/azure-jdks) installato.
- Maven installato.

## <a name="get-the-training-and-prediction-keys"></a>Ottenere le chiavi di training e di stima

Per ottenere le chiavi usate in questo esempio, visitare la [pagina Web di Servizio visione artificiale personalizzato](https://customvision.ai) e selezionare l'__icona a forma di ingranaggio__ in alto a destra. Nella sezione __Account__ copiare i valori dei campi __Training Key__ (Chiave di training) e __Prediction Key__ (Chiave di stima).

![Immagine dell'interfaccia utente delle chiavi](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Installare l'SDK del Servizio visione artificiale personalizzato

È possibile installare Custom Vision SDK dal repository centrale Maven:
* [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Informazioni sul codice

Il progetto completo, incluse le immagini, è disponibile negli [esempi di Visione personalizzata di Azure per il repository Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Usare l'ambiente di sviluppo integrato Java preferito per aprire il progetto `Vision/CustomVision`. 

Questa applicazione usa la chiave di training recuperata in precedenza per creare un nuovo progetto denominato __Sample Java Project__ (Progetto Java di esempio). Quindi carica le immagini per eseguire il training e testare un classificatore. Il classificatore indica se un albero è __hemlock__ o un __ciliegio giapponese__.

I frammenti di codice seguenti implementano la funzionalità principale di questo esempio:

## <a name="create-a-custom-vision-service-project"></a>Creare un progetto di Servizio visione artificiale personalizzato

> [!IMPORTANT]
> Impostare `trainingApiKey` sul valore della chiave di training recuperato in precedenza.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Aggiungere i tag al progetto

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Caricare immagini nel progetto

L'esempio è stato configurato in modo che le immagini siano incluse nel pacchetto finale. Le immagini vengono lette dalla sezione delle risorse del file JAR e caricate nel servizio.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

Il frammento di codice precedente usa due funzioni di supporto che recuperano le immagini come flussi di risorse e le caricano nel servizio.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>Eseguire il training del progetto

Verrà creata la prima iterazione del progetto, che verrà contrassegnata come iterazione predefinita. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Ottenere e usare l'endpoint di stima predefinito

> [!IMPORTANT]
> Impostare `predictionApiKey` sul valore della chiave di stima recuperato in precedenza.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>Eseguire l'esempio

Per compilare ed eseguire la soluzione con Maven:

```
mvn compile exec:java
```

L'output dell'applicazione è simile al testo seguente:

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```