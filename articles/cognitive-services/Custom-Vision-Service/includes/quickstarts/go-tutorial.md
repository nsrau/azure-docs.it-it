---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 481c0d85420ab2cc57f5636ed1862a525ace553b
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604981"
---
Questa guida fornisce istruzioni e codice di esempio per iniziare a usare la libreria client di Visione personalizzata per Go per creare un modello di classificazione immagini. Si creerà un progetto, si aggiungeranno tag, si eseguirà il training del progetto e si userà l'URL dell'endpoint di stima del progetto per testarlo a livello di codice. Usare questo esempio come modello per la creazione di un'applicazione di riconoscimento immagini personalizzata.

> [!NOTE]
> Se si vuole creare un modello di classificazione ed eseguirne il training _senza_ scrivere codice, vedere invece le [istruzioni basate sul browser](../../getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prerequisiti 

- [Go 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Installare la libreria client di Visione personalizzata

Per scrivere un'app di analisi immagini con Visione personalizzata per Go, è necessaria la libreria client di Visione personalizzata. In PowerShell eseguire questo comando:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

o se si usa `dep` nell'esecuzione del repository:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>Aggiungere il codice

Creare un nuovo file denominato *sample.go* nella directory del progetto preferita.

## <a name="create-the-custom-vision-project"></a>Creare il progetto di Visione personalizzata

Per creare un nuovo progetto di Servizio visione artificiale personalizzato, aggiungere il codice seguente allo script. Inserire le chiavi di sottoscrizione nelle definizioni appropriate. Inoltre, ottenere l'URL dell'endpoint dalla pagina Impostazioni del sito Visione personalizzata.

Per specificare altre opzioni quando si crea il progetto, come illustrato in [Creare un classificatore](../../getting-started-build-a-classifier.md) nella guida al portale Web, vedere il metodo [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_).

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

## <a name="create-tags-in-the-project"></a>Creare tag nel progetto

Per creare i tag di classificazione per il progetto, aggiungere il codice seguente alla fine del file *sample.go*:

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Per aggiungere le immagini di esempio al progetto, inserire il codice seguente dopo la creazione dei tag. Questo codice carica ogni immagine con il tag corrispondente. È possibile caricare fino a 64 immagini in un singolo batch.

> [!NOTE]
> Occorrerà anche modificare il percorso delle immagini in base alla posizione in cui è stato scaricato il progetto Cognitive Services Go SDK Samples.

```go
fmt.Println("Adding images...")
japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

for _, file := range hemLockImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
}

for _, file := range japaneseCherryImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
}
```

## <a name="train-and-publish-the-project"></a>Eseguire il training del progetto e pubblicarlo

Questo codice crea la prima iterazione del modello di previsione e quindi la pubblica nell'endpoint di previsione. Il nome assegnato all'iterazione pubblicata può essere usato per inviare le richieste di stima. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

```go
fmt.Println("Training...")
iteration, _ := trainer.TrainProject(ctx, *project.ID)
for {
    if *iteration.Status != "Training" {
        break
    }
    fmt.Println("Training status: " + *iteration.Status)
    time.Sleep(1 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
}
fmt.Println("Training status: " + *iteration.Status)

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

## <a name="use-the-prediction-endpoint"></a>Usare l'endpoint di stima

Per inviare un'immagine all'endpoint di stima e recuperare la stima, aggiungere il codice seguente alla fine del file:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire *sample.go*.

```shell
go run sample.go
```

L'output dell'applicazione dovrebbe essere simile al testo seguente:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Si può quindi verificare che all'immagine di test (disponibile in **<url_immagine_base>/Images/Test/**) siano stati applicati i tag appropriati. È anche possibile tornare al [sito Web di Visione personalizzata](https://customvision.ai) e vedere lo stato corrente del progetto appena creato.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come è possibile eseguire ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](../../test-your-model.md)

* Informazioni su Visione personalizzata
* [Documentazione di riferimento sull'SDK (esercitazione)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training)
* [Documentazione di riferimento sull'SDK (previsione)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)