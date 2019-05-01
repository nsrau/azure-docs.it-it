---
title: 'Guida introduttiva: Python SDK'
titleSuffix: Azure Cognitive Services
description: In questo avvio rapido si apprenderà come usare Python SDK per attività comuni, ad esempio analizzare un'immagine, ottenere una descrizione, riconoscere un testo e generare un'anteprima.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: ce7e8788ec807c8ceccb49a7d435041b34e75348
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917085"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>SDK di Visione artificiale di Servizi cognitivi di Azure per Python

Il servizio Visione artificiale consente agli sviluppatori di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione delle informazioni. Gli algoritmi di Visione artificiale analizzano il contenuto di un'immagine in diversi modi, in base alle caratteristiche visive a cui si è interessati.

* [Analizzare un'immagine](#analyze-an-image)
* [Ottenere l'elenco dei domini soggetto](#get-subject-domain-list)
* [Analizzare un'immagine in base al dominio](#analyze-an-image-by-domain)
* [Ottenere la descrizione testuale di un'immagine](#get-text-description-of-an-image)
* [Ottenere testo scritto a mano da un'immagine](#get-text-from-image)
* [Generare un'anteprima](#generate-thumbnail)

Per altre informazioni sul servizio, vedere [Informazioni su Visione artificiale][computervision_docs].

Per altre informazioni, vedere:

* [Documentazione di riferimento sull'SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Documentazione su Visione artificiale di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.6 o versione successiva][python]
* [Chiave gratuita di Visione artificiale][computervision_resource] ed endpoint associato. Questi valori sono necessari per la creazione dell'istanza dell'oggetto client [ComputerVisionClient][ref_computervisionclient]. Usare uno dei metodi seguenti per ottenerli.

### <a name="if-you-dont-have-an-azure-subscription"></a>Se non si ha una sottoscrizione di Azure

Creare una chiave gratuita valida 7 giorni con l'esperienza **[Prova][computervision_resource]** per il servizio Visione artificiale. Dopo aver creato la chiave, copiarla insieme al nome dell'endpoint. Questa operazione è necessaria per [creare il client](#create-client).

Dopo la creazione della chiave, conservare i valori seguenti:

* Valore della chiave: una stringa di 32 caratteri nel formato `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Endpoint della chiave: l'URL dell'endpoint di base, https\://westcentralus.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Se si ha una sottoscrizione di Azure

Il metodo più semplice per creare una risorsa nella sottoscrizione consiste nell'usare il comando seguente dell'[interfaccia della riga di comando di Azure][azure_cli]. In questo modo viene creata una chiave di Servizi cognitivi che può essere usata in molti servizi cognitivi. È necessario scegliere il nome del gruppo di risorse _esistente_, ad esempio "my-cogserv-group", e il nome della nuova risorsa di Visione artificiale, ad esempio "my-computer-vision-resource".

```Bash
RES_REGION=westeurope
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>Installare l'SDK

Installare il [pacchetto][pypi_computervision] dell'SDK di Visione artificiale di Servizi cognitivi di Azure per Python con [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Dopo aver creato la risorsa Visione artificiale, per creare un'istanza dell'oggetto client sono necessarie l'**endpoint** e una delle **chiavi dell'account** di tale risorsa.

Usare questi valori per la creazione dell'istanza dell'oggetto client [ComputerVisionClient][ref_computervisionclient].

Usare ad esempio il terminale Bash per impostare le variabili di ambiente:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Per gli utenti di sottoscrizioni di Azure, ottenere le credenziali per la chiave e l'endpoint

Se non si ricordano la chiave e l'endpoint, è possibile usare il metodo seguente per trovarle. Se è necessario creare un endpoint e un'area, è possibile usare il metodo per i [titolari delle sottoscrizioni di Azure](#if-you-have-an-azure-subscription) o per gli [utenti senza una sottoscrizione di Azure](#if-you-dont-have-an-azure-subscription).

Usare il frammento seguente dell'[interfaccia della riga di comando di Azure][cloud_shell] per popolare due variabili di ambiente con l'**endpoint** e una delle **chiavi** dell'account Visione artificiale. Questi valori sono disponibili anche nel [portale di Azure][azure_portal]. Il frammento è presentato nel formato per la shell Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_ENDPOINT=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query endpoint \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Creare il client

Ottenere la chiave e l'endpoint dalle variabili di ambiente e quindi creare l'oggetto client [ComputerVisionClient][ref_computervisionclient].

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get endpoint and key from environment variables
import os
endpoint = os.environ['ACCOUNT_ENDPOINT']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionClient(endpoint, credentials)
```

## <a name="examples"></a>Esempi

Prima di eseguire una delle attività seguenti, è necessario un oggetto client [ComputerVisionClient][ref_computervisionclient].

### <a name="analyze-an-image"></a>Analizzare un'immagine

È possibile analizzare un'immagine per individuare determinate caratteristiche con [`analyze_image`][ref_computervisionclient_analyze_image]. Usare la proprietà [`visual_features`][ref_computervision_model_visualfeatures] per impostare i tipi di analisi da eseguire sull'immagine. I valori comuni sono `VisualFeatureTypes.tags` e `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Ottenere l'elenco dei domini soggetto

Esaminare i domini soggetto usati per analizzare l'immagine con [`list_models`][ref_computervisionclient_list_models]. Questi nomi di dominio verranno usati per [analizzare un'immagine in base al dominio](#analyze-an-image-by-domain). Un esempio di dominio è `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analizzare un'immagine in base al dominio

È possibile analizzare un'immagine in base al dominio soggetto con [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]. Ottenere l'[elenco dei domini soggetto supportati](#get-subject-domain-list) per usare il nome di dominio corretto.

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Ottenere la descrizione testuale di un'immagine

È possibile ottenere una descrizione testuale di un'immagine, in base alla lingua, con [`describe_image`][ref_computervisionclient_describe_image]. Se si esegue l'analisi del testo per le parole chiave associate all'immagine, richiedere diverse descrizioni con la proprietà `max_description`. Gli esempi di descrizione testuale dell'immagine seguente includono `a train crossing a bridge over a body of water`, `a large bridge over a body of water` e `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Ottenere testo da un'immagine

È possibile ottenere qualsiasi testo stampato o scritto a mano da un'immagine. A questo scopo sono necessarie due chiamate all'SDK: [`batch_read_file`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python#batch-read-file-url--mode--custom-headers-none--raw-false----operation-config-) e [`get_read_operation_result`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python#get-read-operation-result-operation-id--custom-headers-none--raw-false----operation-config-). La chiamata a `batch_read_file` è asincrona. Prima di estrarre dati di testo, è necessario controllare nei risultati della chiamata a `get_read_operation_result` se la prima chiamata è stata completata con [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes]. I risultati includono sia il testo sia le coordinate del rettangolo di selezione per il testo.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while True:
    result = client.get_read_operation_result(operationId)
    if result.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Get data
if result.status == TextOperationStatusCodes.succeeded:
    for textResult in result.recognition_results:
        for line in textResult.lines:
            print(line.text)
            print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Generare un'anteprima

È possibile generare un'anteprima in formato JPG di un'immagine con [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. L'anteprima non deve necessariamente avere le stesse proporzioni dell'immagine originale.

Installare **Pillow** per usare questo esempio:

```bash
pip install Pillow
```

Dopo aver installato Pillow, usare il pacchetto dell'esempio di codice seguente per generare l'immagine di anteprima.

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="general"></a>Generale

Quando si interagisce con l'oggetto client [ComputerVisionClient][ref_computervisionclient] usando l'SDK per Python, per la restituzione degli errori viene usata la classe [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception]. Gli errori restituiti dal servizio corrispondono agli stessi codici di stato HTTP restituiti per le richieste di API REST.

Se si prova ad analizzare un'immagine con una chiave non valida, ad esempio, viene restituito un errore `401`. Nel frammento seguente, l'[errore][ref_httpfailure] viene gestito normalmente rilevando l'eccezione e visualizzando informazioni aggiuntive sull'errore.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and endpoint are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Gestire gli errori temporanei con nuovi tentativi

Mentre si usa il client [ComputerVisionClient][ref_computervisionclient], potrebbero verificarsi errori temporanei causati dai [limiti di frequenza][computervision_request_units] applicati dal servizio o altri problemi temporanei come interruzioni della rete. Per informazioni sulla gestione di questi tipi di errori, vedere [Modello di ripetizione dei tentativi][azure_pattern_retry] nella guida Modelli di progettazione cloud e l'articolo correlato [Modello a interruttore][azure_pattern_circuit_breaker].

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Applicazione di tag di contenuto alle immagini](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://azure.microsoft.com/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/
