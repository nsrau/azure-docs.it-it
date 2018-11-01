---
title: Distribuire un modello come servizio Web in un FPGA con Azure Machine Learning
description: Informazioni su come distribuire un servizio Web con un modello in esecuzione in un FPGA con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/29/2018
ms.openlocfilehash: 3b2ff5bf4413c5d6a681aff66f0f338567f3b6a8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242739"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Distribuire un modello come servizio Web in un FPGA con Azure Machine Learning

È possibile distribuire un modello come servizio web in [Field programmable gate arrays (FPGA)](concept-accelerate-with-fpgas.md).  L'uso di FPGA offre inferenze a latenza estremamente bassa, anche con una singola dimensione batch.   

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un'area di lavoro del servizio di Azure Machine Learning e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).
 
  - L'area di lavoro deve trovarsi nell'area degli *Stati Uniti orientali 2*.

  - Installare le funzionalità aggiuntive contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Creare e distribuire un modello
Creare una pipeline per pre-elaborare l'immagine di input, trasformare utilizzando ResNet 50 in un FPGA e quindi eseguire le funzionalità attraverso una classificazione sottoposto con training sul set di dati ImageNet.

Seguire le istruzioni per:

* Definire il modello di pipeline
* Distribuire il modello
* Utilizzare il modello distribuito
* Eliminare i servizi distribuiti

> [!IMPORTANT]
> Per ottimizzare la latenza e la velocità effettiva, il client deve trovarsi nella stessa area di Azure in cui si trova l'endpoint.  Le API vengono attualmente create nell'area Stati Uniti orientali.



### <a name="preprocess-image"></a>Pre-elaborare l'immagine
La prima fase della pipeline consiste nella pre-elaborazione delle immagini.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Aggiungere utilità di funzioni
Inizializzare il modello e scaricare un checkpoint TensorFlow della versione quantizzata ResNet50 da usare come utilità di funzioni.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Aggiungere il classificatore
Questo classificatore è stato eseguito con training sul set di dati ImageNet.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Creare la definizione del servizio
Ora che avete definiti l'immagine di pre-elaborazione, l'utilità di funzioni e il classificatore eseguiti sul servizio, è possibile creare una definizione di servizio. La definizione del servizio è un set di file generato dal modello che viene distribuito nel servizio FPGA. La definizione del servizio è costituita da una pipeline. La pipeline è una serie di fasi che vengono eseguite in ordine.  Le fasi di TensorFlow, fasi Keras e fasi di BrainWave sono supportate.  Le fasi vengono eseguite nell'ordine nel servizio, con l'output di ogni input di fase in quello della fase successiva.

Per creare una fase TensorFlow, specificare una sessione che contiene il grafico (in questo caso viene utilizzato il grafico predefinito) e l'input e output di tensors per questa fase.  Queste informazioni consentono di salvare il grafico in modo che possa essere eseguito nel servizio.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Distribuzione del modello
Creare un servizio dalla definizione del servizio.  L'area di lavoro deve trovarsi nell'area degli Stati Uniti orientali 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Testare il servizio
Per inviare un'immagine per l'API e testare la risposta, aggiungere un mapping dall'ID di classe di output per il nome della classe ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Chiamare il servizio e sostituire il nome del file "your-Image. jpg" di seguito con un'immagine dal computer. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Pulizia del servizio
Eliminare il servizio Web.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Proteggere i servizi web FPGA

Azure Machine Learning eseguito su FPGA fornisce il supporto SSL e l'autenticazione basata su chiavi. In questo modo è possibile limitare l'accesso al servizio e proteggere i dati inviati dai client. [Informazioni su come proteggere il servizio Web](how-to-secure-web-service.md).


## <a name="sample-notebook"></a>Notebook di esempio

I concetti espressi in questo articolo vengono dimostrati nel notebook [project-brainwave/project-brainwave-quickstart.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/project-brainwave/project-brainwave-quickstart.ipynb).

Per ottenere questo blocco appunti:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
