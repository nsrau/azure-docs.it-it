---
title: 'Guida introduttiva: Python SDK'
titleSuffix: Azure Cognitive Services
description: Questa guida introduttiva illustra come usare l'SDK per Python per attività comuni.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: pafarley
ms.openlocfilehash: 3043067f326f782c51be38382070ae0db0e90f4d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314171"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>SDK di Visione artificiale di Servizi cognitivi di Azure per Python

Il servizio Visione artificiale consente agli sviluppatori di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione delle informazioni. Gli algoritmi di Visione artificiale analizzano il contenuto di un'immagine in diversi modi, in base alle caratteristiche visive a cui si è interessati. Visione artificiale può ad esempio determinare se un'immagine include contenuto per adulti o spinto, trovare tutti i visi in un'immagine oppure ottenere testo stampato o scritto a mano. Il servizio è compatibile con formati di immagine comuni come JPEG e PNG. 

È possibile usare Visione artificiale in un'applicazione per:

- Analizzare le immagini per ottenere informazioni dettagliate
- Estrarre testo dalle immagini
- Generare anteprime

Per altre informazioni, vedere:

* [Documentazione di riferimento sull'SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Documentazione su Visione artificiale di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. [Creare un account gratuito][azure_sub]
* [Risorsa Visione artificiale][computervision_resource] di Azure
* [Python 3.6 o versione successiva][python]

Se è necessario un account API Visione artificiale, è possibile crearne uno con questo comando dell'[interfaccia della riga di comando di Azure][azure_cli]:

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind ComputerVision \
    --sku S1 \
    --yes
```

## <a name="installation"></a>Installazione

Installare l'SDK di Visione artificiale di Servizi cognitivi di Azure con [pip][pip]. Facoltativamente, eseguire l'installazione in un [ambiente virtuale][venv].

### <a name="configure-a-virtual-environment-optional"></a>Configurare un ambiente virtuale (facoltativo)

Nonostante non sia obbligatorio, è possibile mantenere gli ambienti del sistema di base e degli SDK di Azure isolati tra loro usando un [ambiente virtuale][virtualenv]. Eseguire questi comandi per configurare un ambiente virtuale con [venv][venv], ad esempio `cogsrv-vision-env`, e quindi passare a tale ambiente:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```

### <a name="install-the-sdk"></a>Installare l'SDK

Installare il [pacchetto][pypi_computervision] dell'SDK di Visione artificiale di Servizi cognitivi di Azure per Python con [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Autenticazione

Dopo aver creato la risorsa Visione artificiale, per creare un'istanza dell'oggetto client sono necessarie l'**area** e una delle **chiavi dell'account** di tale risorsa.

Usare questi valori durante la creazione dell'istanza dell'oggetto client [ComputerVisionAPI][ref_computervisionclient]. 

### <a name="get-credentials"></a>Ottenere le credenziali

Usare il frammento seguente dell'[interfaccia della riga di comando di Azure][cloud_shell] per popolare due variabili di ambiente con l'**area** e una delle **chiavi** dell'account Visione artificiale. Questi valori sono disponibili anche nel [portale di Azure][azure_portal]. Il frammento è presentato nel formato per la shell Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```

### <a name="create-client"></a>Creare il client

Dopo aver popolato le variabili di ambiente `ACCOUNT_REGION` e `ACCOUNT_KEY`, è possibile creare l'oggetto client [ComputerVisionAPI][ref_computervisionclient].

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

## <a name="usage"></a>Uso

Dopo aver inizializzato un oggetto client [ComputerVisionAPI][ref_computervisionclient], è possibile:

* Analizzare un'immagine. È possibile analizzare un'immagine per individuare determinate caratteristiche come visi, colori e tag.   
* Generare anteprime. Creare un'immagine JPEG personalizzata da usare come anteprima dell'immagine originale.
* Ottenere la descrizione di un'immagine. Ottenere una descrizione dell'immagine in base al dominio soggetto. 

Per altre informazioni sul servizio, vedere [Informazioni su Visione artificiale][computervision_docs].

## <a name="examples"></a>Esempi

Le sezioni seguenti contengono diversi frammenti di codice relativi ad alcune delle attività più comuni di Visione artificiale:

* [Analizzare un'immagine](#analyze-an-image)
* [Ottenere l'elenco dei domini soggetto](#get-subject-domain-list)
* [Analizzare un'immagine in base al dominio](#analyze-an-image-by-domain)
* [Ottenere la descrizione testuale di un'immagine](#get-text-description-of-an-image)
* [Ottenere testo scritto a mano da un'immagine](#get-text-from-image)
* [Generare un'anteprima](#generate-thumbnail)

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
domain = "landmarks"
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"
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

È possibile ottenere qualsiasi testo stampato o scritto a mano da un'immagine. A questo scopo sono necessarie due chiamate all'SDK: [`recognize_text`][ref_computervisionclient_recognize_text] e [`get_text_operation_result`][ref_computervisionclient_get_text_operation_result]. La chiamata a recognize_text è asincrona. Prima di estrarre dati di testo è necessario controllare nei risultati della chiamata a get_text_operation_result se la prima chiamata è stata completata con [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes]. I risultati includono sia il testo sia le coordinate del rettangolo di selezione per il testo. 

```Python
url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Generare un'anteprima

È possibile generare un'anteprima in formato JPG di un'immagine con [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. L'anteprima non deve necessariamente avere le stesse proporzioni dell'immagine originale. 

Questo esempio usa il pacchetto [Pillow][pypi_pillow] per salvare la nuova immagine di anteprima in locale.

```Python
from PIL import Image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="general"></a>Generale

Quando si interagisce con l'oggetto client [ComputerVisionAPI][ref_computervisionclient] usando l'SDK per Python, per la restituzione degli errori viene usata la classe [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception]. Gli errori restituiti dal servizio corrispondono agli stessi codici di stato HTTP restituiti per le richieste di API REST.

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
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Gestire gli errori temporanei con nuovi tentativi

Mentre si usa il client [ComputerVisionAPI][ref_computervisionclient], potrebbero verificarsi errori temporanei causati dai [limiti di frequenza][computervision_request_units] applicati dal servizio o altri problemi temporanei come interruzioni della rete. Per informazioni sulla gestione di questi tipi di errori, vedere [Modello di ripetizione dei tentativi][azure_pattern_retry] nella guida Modelli di progettazione cloud e l'articolo correlato [Modello a interruttore][azure_pattern_circuit_breaker].

## <a name="next-steps"></a>Passaggi successivi

### <a name="more-sample-code"></a>Altro codice di esempio

Diversi esempi per l'SDK di Visione artificiale per Python sono disponibili nel repository GitHub dell'SDK. Questi esempi offrono codice di esempio per altri scenari comuni correlati all'uso di Visione artificiale:

* [recognize_text][recognize-text]

### <a name="additional-documentation"></a>Documentazione aggiuntiva

Per informazioni più complete sul servizio Visione artificiale, vedere la [documentazione su Visione artificiale di Azure][computervision_docs] in docs.microsoft.com.

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure
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


[computervision_resource]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

