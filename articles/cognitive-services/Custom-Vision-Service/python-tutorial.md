---
title: "Guida introduttiva: Creare un progetto di classificazione immagini con l'SDK Visione personalizzata per Python"
titlesuffix: Azure Cognitive Services
description: Creare un progetto, aggiungere i tag, caricare le immagini, eseguire il training del progetto ed effettuare una stima usando Python SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 11/2/2018
ms.author: areddish
ms.openlocfilehash: 66fc773dd354f80428d6fd65906610b901260a59
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407035"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Guida introduttiva: Creare un progetto di classificazione immagini con l'SDK Visione personalizzata per Python

Questo articolo fornisce informazioni e codice di esempio utili per iniziare a usare Custom Vision SDK con Python per compilare un modello di classificazione delle immagini. Dopo la creazione, è possibile aggiungere tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio come modello per la creazione di un'applicazione Python personalizzata. Se si preferisce eseguire la procedura di compilazione e utilizzo di un modello di classificazione _senza_ codice, vedere le [indicazioni basate su browser](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prerequisiti

- [Python 2.7 o 3.5 e versioni successive](https://www.python.org/downloads/)
- Strumento [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-custom-vision-sdk"></a>Installare Custom Vision SDK

Per installare l'SDK del Servizio visione artificiale personalizzato per Python, usare il comando seguente in PowerShell:

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>Aggiungere il codice

Creare un nuovo file denominato *sample.py* nella directory del progetto preferita.

### <a name="create-the-custom-vision-service-project"></a>Creare il progetto di Servizio visione artificiale personalizzato

Per creare un nuovo progetto di Servizio visione artificiale personalizzato, aggiungere il codice seguente allo script. Inserire le chiavi di sottoscrizione nelle definizioni appropriate.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Creare tag nel progetto

Per creare i tag di classificazione per il progetto, aggiungere il codice seguente alla fine del file *sample.py*:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Per aggiungere le immagini di esempio al progetto, inserire il codice seguente dopo la creazione dei tag. Questo codice carica ogni immagine con il tag corrispondente. Sarà necessario immettere il percorso dell'URL dell'immagine di base, che varia a seconda di dove è stato scaricato il progetto Cognitive Services Python SDK Samples.

> [!NOTE]
> Occorrerà anche modificare il percorso delle immagini in base a dove è stato scaricato il progetto cognitive-services-java-sdk-samples.

```Python
base_image_url = "<path to project>"

print("Adding images...")

image_entry = lambda image_path, tag_id: ImageFileCreateEntry(
    name=image_path.split("/")[-1], contents=image_path, tag_ids=[tag_id]
)

image_list = [
    image_entry(
        base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num),
        hemlock_tag.id,
    )
    for image_num in range(1, 10)
] + [
    image_entry(
        base_image_url
        + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num),
        cherry_tag.id,
    )
    for image_num in range(1, 10)
]

trainer.create_images_from_files(project.id, images=image_list)
```

### <a name="train-the-classifier"></a>Training del classificatore

Questo codice crea la prima iterazione del progetto e la contrassegna come iterazione predefinita. L'iterazione predefinita riflette la versione del modello che risponderà alle richieste di stima. È necessario aggiornarla ogni volta che si ripete il training del modello.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>Ottenere e usare l'endpoint di stima predefinito

Per inviare un'immagine all'endpoint di stima e recuperare la stima, aggiungere il codice seguente alla fine del file:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire *sample.py*.

```PowerShell
python sample.py
```

L'output dell'applicazione dovrebbe essere simile al testo seguente:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Si può quindi verificare che all'immagine di test (disponibile in **<url_immagine_base>/Images/Test/**) siano stati applicati i tag appropriati. È anche possibile tornare al [sito Web di Visione personalizzata](https://customvision.ai) e vedere lo stato corrente del progetto appena creato.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come eseguire ogni passaggio del processo di classificazione delle immagini nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](test-your-model.md)
