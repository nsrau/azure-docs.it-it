---
title: 'Esercitazione: Creare un progetto di classificazione immagini - Servizio visione artificiale personalizzato, Python'
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
ms.openlocfilehash: 14b805a60637a889698132e169d5a41670a8bce0
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363378"
---
# <a name="tutorial-create-an-image-classification-project-using-the-custom-vision-service-with-python"></a>Esercitazione: Creare un progetto di classificazione immagini usando il Servizio visione artificiale personalizzato con Python

Informazioni su come creare un progetto di classificazione immagini con il Servizio visione artificiale personalizzato e uno script Python di base. Dopo la creazione, è possibile aggiungere tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint predefinito per la stima del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio open source come modello per la compilazione di un'app usando l'API Visione personalizzata.



## <a name="prerequisites"></a>Prerequisiti

- Python 2.7+ o Python 3.5.+.
- Strumento pip.

## <a name="get-the-training-and-prediction-keys"></a>Ottenere le chiavi di training e di stima

Per ottenere le chiavi usate in questo esempio, visitare la [pagina Web di Servizio visione artificiale personalizzato](https://customvision.ai) e selezionare l'__icona a forma di ingranaggio__ in alto a destra. Nella sezione __Account__ copiare i valori dei campi __Training Key__ (Chiave di training) e __Prediction Key__ (Chiave di stima).

![Immagine dell'interfaccia utente delle chiavi](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Installare l'SDK del Servizio visione artificiale personalizzato

Per installare l'SDK del Servizio visione artificiale personalizzato, usare il comando seguente:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Ottenere immagini di esempio

Questo esempio usa le immagini dalla directory `Samples/Images`del progetto [https://github.com/Microsoft/Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images). Clonare o scaricare ed estrarre il progetto nell'ambiente di sviluppo.

## <a name="create-a-custom-vision-service-project"></a>Creare un progetto di Servizio visione artificiale personalizzato

Per creare un nuovo progetto di Servizio visione artificiale personalizzato, creare un nuovo file denominato `sample.py`. Usare il codice seguente come contenuto del file:

> [!IMPORTANT]
> Impostare `training_key` sul valore della chiave di training recuperato in precedenza.
>
> Impostare `prediction_key` sul valore della chiave di stima recuperato in precedenza.

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>Aggiungere i tag al progetto

Per aggiungere i tag al progetto, aggiungere il codice seguente alla fine del file `sample.py`:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Caricare immagini nel progetto

Per aggiungere le immagini di esempio al progetto, inserire il codice seguente dopo la creazione dei tag. Questo codice carica l'immagine con il tag corrispondente:

> [!IMPORTANT]
>
> Passare al percorso contenente le immagini in base a dove è stato scaricato il progetto Cognitive-CustomVision-Windows in precedenza.

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>Eseguire il training del progetto

Per eseguire il training del classificatore, aggiungere il codice seguente alla fine del file `sample.py`:

```python
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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Ottenere e usare l'endpoint di stima predefinito

Per inviare un'immagine all'endpoint di stima e recuperare la stima, aggiungere il codice seguente alla fine del file `sample.py`:

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>Eseguire l'esempio

Eseguire la soluzione. Nella console vengono visualizzati i risultati della stima.

```
python sample.py
```

L'output dell'applicazione è simile al testo seguente:

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