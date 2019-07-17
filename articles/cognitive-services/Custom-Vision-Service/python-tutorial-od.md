---
title: "Guida introduttiva: Creare un progetto di rilevamento degli oggetti con l'SDK Visione personalizzata per Python"
titlesuffix: Azure Cognitive Services
description: Creare un progetto, aggiungere i tag, caricare le immagini, eseguire il training del progetto e rilevare oggetti usando Python SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: 623bf0b054544d2c25f3542043afe20d778fdd24
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603489"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-python-sdk"></a>Guida introduttiva: Creare un progetto di rilevamento degli oggetti con l'SDK Visione personalizzata per Python

Questo articolo fornisce informazioni e codice di esempio utili per iniziare a usare Custom Vision SDK con Python per compilare un modello di rilevamento degli oggetti. Dopo la creazione, è possibile aggiungere aree con tag, caricare immagini, eseguire il training del progetto, ottenere l'URL dell'endpoint di stima pubblicato del progetto e usare l'endpoint per un test a livello di codice dell'immagine. Usare questo esempio come modello per la creazione di un'applicazione Python personalizzata.

## <a name="prerequisites"></a>Prerequisiti

- [Python 2.7 o 3.5 e versioni successive](https://www.python.org/downloads/)
- Strumento [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-custom-vision-sdk"></a>Installare Custom Vision SDK

Per installare l'SDK del Servizio visione artificiale personalizzato per Python, usare il comando seguente in PowerShell:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

È possibile scaricare le immagini con gli [esempi per Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples).

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Aggiungere il codice

Creare un nuovo file denominato *sample.py* nella directory del progetto preferita.

### <a name="create-the-custom-vision-service-project"></a>Creare il progetto di Servizio visione artificiale personalizzato

Per creare un nuovo progetto di Servizio visione artificiale personalizzato, aggiungere il codice seguente allo script. Inserire le chiavi di sottoscrizione nelle definizioni appropriate. La differenza tra la creazione di un progetto di rilevamento di oggetti e uno di classificazione di immagini è data dal dominio specificato per la chiamata a **create_project**.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry, Region

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "detectModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Find the object detection domain
obj_detection_domain = next(domain for domain in trainer.get_domains() if domain.type == "ObjectDetection" and domain.name == "General")

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Detection Project", domain_id=obj_detection_domain.id)
```

### <a name="create-tags-in-the-project"></a>Creare tag nel progetto

Per creare i tag degli oggetti nel progetto, aggiungere il codice seguente alla fine del file *sample.py*:

```Python
# Make two tags in the new project
fork_tag = trainer.create_tag(project.id, "fork")
scissors_tag = trainer.create_tag(project.id, "scissors")
```

### <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

Quando si aggiungono tag alle immagini nei progetti di rilevamento degli oggetti, è necessario specificare l'area di ogni oggetto contrassegnato usando coordinate normalizzate.

Per aggiungere le immagini, i tag e le aree al progetto, inserire il codice seguente dopo la creazione dei tag. Per questa esercitazione le aree sono hardcoded in linea con il codice. Le aree specificano il rettangolo delimitatore in coordinate normalizzate e le coordinate sono specificate in questo ordine: sinistra, alto, larghezza, altezza.

```Python
fork_image_regions = {
    "fork_1": [ 0.145833328, 0.3509314, 0.5894608, 0.238562092 ],
    "fork_2": [ 0.294117659, 0.216944471, 0.534313738, 0.5980392 ],
    "fork_3": [ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 ],
    "fork_4": [ 0.254901975, 0.185898721, 0.5232843, 0.594771266 ],
    "fork_5": [ 0.2365196, 0.128709182, 0.5845588, 0.71405226 ],
    "fork_6": [ 0.115196079, 0.133611143, 0.676470637, 0.6993464 ],
    "fork_7": [ 0.164215669, 0.31008172, 0.767156839, 0.410130739 ],
    "fork_8": [ 0.118872553, 0.318251669, 0.817401946, 0.225490168 ],
    "fork_9": [ 0.18259804, 0.2136765, 0.6335784, 0.643790841 ],
    "fork_10": [ 0.05269608, 0.282303959, 0.8088235, 0.452614367 ],
    "fork_11": [ 0.05759804, 0.0894935, 0.9007353, 0.3251634 ],
    "fork_12": [ 0.3345588, 0.07315363, 0.375, 0.9150327 ],
    "fork_13": [ 0.269607842, 0.194068655, 0.4093137, 0.6732026 ],
    "fork_14": [ 0.143382356, 0.218578458, 0.7977941, 0.295751631 ],
    "fork_15": [ 0.19240196, 0.0633497, 0.5710784, 0.8398692 ],
    "fork_16": [ 0.140931368, 0.480016381, 0.6838235, 0.240196079 ],
    "fork_17": [ 0.305147052, 0.2512582, 0.4791667, 0.5408496 ],
    "fork_18": [ 0.234068632, 0.445702642, 0.6127451, 0.344771236 ],
    "fork_19": [ 0.219362751, 0.141781077, 0.5919118, 0.6683006 ],
    "fork_20": [ 0.180147052, 0.239820287, 0.6887255, 0.235294119 ]
}

scissors_image_regions = {
    "scissors_1": [ 0.4007353, 0.194068655, 0.259803921, 0.6617647 ],
    "scissors_2": [ 0.426470578, 0.185898721, 0.172794119, 0.5539216 ],
    "scissors_3": [ 0.289215684, 0.259428144, 0.403186262, 0.421568632 ],
    "scissors_4": [ 0.343137264, 0.105833367, 0.332107842, 0.8055556 ],
    "scissors_5": [ 0.3125, 0.09766343, 0.435049027, 0.71405226 ],
    "scissors_6": [ 0.379901975, 0.24308826, 0.32107842, 0.5718954 ],
    "scissors_7": [ 0.341911763, 0.20714055, 0.3137255, 0.6356209 ],
    "scissors_8": [ 0.231617644, 0.08459154, 0.504901946, 0.8480392 ],
    "scissors_9": [ 0.170343131, 0.332957536, 0.767156839, 0.403594762 ],
    "scissors_10": [ 0.204656869, 0.120539248, 0.5245098, 0.743464053 ],
    "scissors_11": [ 0.05514706, 0.159754932, 0.799019635, 0.730392158 ],
    "scissors_12": [ 0.265931368, 0.169558853, 0.5061275, 0.606209159 ],
    "scissors_13": [ 0.241421565, 0.184264734, 0.448529422, 0.6830065 ],
    "scissors_14": [ 0.05759804, 0.05027781, 0.75, 0.882352948 ],
    "scissors_15": [ 0.191176474, 0.169558853, 0.6936275, 0.6748366 ],
    "scissors_16": [ 0.1004902, 0.279036, 0.6911765, 0.477124184 ],
    "scissors_17": [ 0.2720588, 0.131977156, 0.4987745, 0.6911765 ],
    "scissors_18": [ 0.180147052, 0.112369314, 0.6262255, 0.6666667 ],
    "scissors_19": [ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 ],
    "scissors_20": [ 0.158088237, 0.04047389, 0.6691176, 0.843137264 ]
}
```

Usare quindi questa mappa di associazioni per caricare ogni immagine di esempio con le relative coordinate di area. Aggiungere il codice seguente.

```Python
# Update this with the path to where you downloaded the images.
base_image_url = "<path to the images>"

# Go through the data table above and create the images
print ("Adding images...")
tagged_images_with_regions = []

for file_name in fork_image_regions.keys():
    x,y,w,h = fork_image_regions[file_name]
    regions = [ Region(tag_id=fork_tag.id, left=x,top=y,width=w,height=h) ]

    with open(base_image_url + "images/fork/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

for file_name in scissors_image_regions.keys():
    x,y,w,h = scissors_image_regions[file_name]
    regions = [ Region(tag_id=scissors_tag.id, left=x,top=y,width=w,height=h) ]

    with open(base_image_url + "images/scissors/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

upload_result = trainer.create_images_from_files(project.id, images=tagged_images_with_regions)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-project-and-publish"></a>Training del progetto e pubblicazione

Questo codice crea la prima iterazione del progetto e quindi la pubblica nell'endpoint di stima. Il nome assegnato all'iterazione pubblicata può essere usato per inviare le richieste di stima. L'iterazione è disponibile nell'endpoint di stima solo dopo che è stata pubblicata.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Ottenere e usare l'iterazione pubblicata nell'endpoint di stima

Per inviare un'immagine all'endpoint di stima e recuperare la stima, aggiungere il codice seguente alla fine del file:

```Python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

# Open the sample image and get back the prediction results.
with open(base_image_url + "images/Test/test_od_image.jpg", mode="rb") as test_data:
    results = predictor.detect_image(project.id, publish_iteration_name, test_data)

# Display the results.    
for prediction in results.predictions:
    print("\t" + prediction.tag_name + ": {0:.2f}% bbox.left = {1:.2f}, bbox.top = {2:.2f}, bbox.width = {3:.2f}, bbox.height = {4:.2f}".format(prediction.probability * 100, prediction.bounding_box.left, prediction.bounding_box.top, prediction.bounding_box.width, prediction.bounding_box.height))
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire *sample.py*.

```powershell
python sample.py
```

L'output dell'applicazione dovrebbe essere visualizzato nella console. È quindi possibile verificare che l'immagine di test (disponibile in **samples/vision/images/Test**) sia contrassegnata in modo appropriato e che l'area di rilevamento sia corretta.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come eseguire ogni passaggio del processo di rilevamento degli oggetti nel codice. Questo esempio esegue una sola iterazione del training, ma spesso è necessario eseguire il training e il test del modello più volte per ottenere una maggiore precisione. La guida seguente è incentrata sulla classificazione delle immagini, ma i principi sono simili a quelli del rilevamento di oggetti.

> [!div class="nextstepaction"]
> [Testare un modello e ripeterne il training](test-your-model.md)
