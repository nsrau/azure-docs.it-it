---
title: Distribuire modelli in dispositivi FPGA
titleSuffix: Azure Machine Learning service
description: Informazioni su come distribuire un servizio Web con un modello in esecuzione in un dispositivo FPGA con il servizio Azure Machine Learning per inferenze a latenza estremamente bassa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cfe21d2119b92665c5950d792dec6500257c6316
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024173"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Come distribuire un modello come servizio Web in un dispositivo FPGA con il servizio Azure Machine Learning

È possibile distribuire un modello come servizio web in [campo matrici programmable gate (FPGA)](concept-accelerate-with-fpgas.md) con modelli con accelerazione Hardware di Azure Machine Learning. L'uso di FPGA offre inferenze a latenza estremamente bassa, anche con una singola dimensione batch.

Questi sono i modelli attualmente disponibili:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

Il FPGA è disponibili in queste aree di Azure:
  - Stati Uniti orientali
  - Stati Uniti occidentali 2
  - Europa occidentale
  - Asia sud-orientale

> [!IMPORTANT]
> Per ottimizzare la latenza e velocità effettiva, i client che invia dati al modello FPGA deve essere in una delle aree di sopra (quello) che è stato distribuito il modello).

## <a name="prerequisites"></a>Prerequisiti

- Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

- Un'area di lavoro del servizio di Azure Machine Learning e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).
 
  - Installare il SDK per Python per i modelli con accelerazione hardware:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Notebook di esempio

Per comodità [notebook di esempio](https://aka.ms/aml-notebooks) sono disponibili per l'esempio di seguito e, oltre a altri esempi.  Cercare cartelle how-to-uso-azureml e distribuzione per i modelli con accelerati.

## <a name="create-and-containerize-your-model"></a>Creare e distribuire il modello in un contenitore

Questo documento verrà descritto come creare un grafico di TensorFlow per pre-elaborare l'immagine di input, renderlo un featurizer con ResNet 50 in un FPGA e quindi eseguire le funzionalità attraverso un classificatore con training eseguito su set di dati ImageNet.

Seguire le istruzioni per:

* Definire il modello TensorFlow
* Distribuire il modello
* Utilizzare il modello distribuito
* Eliminare i servizi distribuiti

### <a name="load-azure-ml-workspace"></a>Carica area di lavoro di Azure Machine Learning

Caricare l'area di lavoro di Azure Machine Learning.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace
 
ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Pre-elaborare l'immagine

L'input al servizio web è un'immagine JPEG.  Il primo passaggio è decodificare l'immagine JPEG e pre-elaborazione.  Le immagini JPEG vengono trattate come stringhe e il risultato sono tensors che rappresenterà l'input per il modello ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Caricare featurizer

Inizializzare il modello e scaricare un checkpoint TensorFlow della versione quantizzata ResNet50 da usare come utilità di funzioni.  È possibile sostituire "QuantizedResnet50" nel frammento di codice seguente con importando altre reti neurali profonde:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Aggiungi classificazione

Questo classificatore è stato eseguito con training sul set di dati ImageNet.  Sono disponibili nel set di esempi per il trasferimento di apprendimento e formazione i pesi personalizzati [notebook di esempio](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Salvare il modello

Ora che il preprocessore, featurizer ResNet 50 e la funzione di classificazione sono stati caricati, salvare il grafico e le variabili associate come modello.

```python
model_name = "resnet50"
model_def_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_def_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_def_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="register-model"></a>Registrare il modello

[Registrare](./concept-model-management-and-deployment.md) il modello creato.  Aggiunta di tag e altri metadati relativi al modello consente di tenere traccia dei modelli con training.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws
                                  model_path = model_def_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Se già stato registrato un modello e si intende caricarli, è possibile recuperarlo.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Conversione del modello

Il grafico di TensorFlow deve essere convertito nel formato di scambio di rete neurale aperta ([ONNX](https://onnx.ai/)).  È necessario specificare i nomi delle tensors input e output e questi nomi verranno usati dal client quando si usano il servizio web.

```python
input_tensor = in_images.name
output_tensors = classifier_output.name

print(input_tensor)
print(output_tensors)


from azureml.accel.accel_onnx_converter import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensor, output_tensors)
convert_request.wait_for_completion(show_output=True)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print(converted_model.name, converted_model.url, converted_model.version, converted_model.id,converted_model.created_time)
```

### <a name="create-docker-image"></a>Creare l'immagine Docker

Il modello convertito e tutte le dipendenze vengono aggiunti a un'immagine Docker.  Questa immagine di Docker può essere distribuita e crearne un'istanza nel cloud o un dispositivo periferico supportati, ad esempio [finestra di dati Azure Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  È anche possibile aggiungere tag e descrizioni per l'immagine Docker registrato.

```python
from azureml.core.image import Image
from azureml.accel.accel_container_image import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)


image.wait_for_creation(show_output = True)
```

Elencare le immagini da tag e ottenere i log dettagliati per qualsiasi tipo di debug.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Distribuzione di modelli

### <a name="deploy-to-the-cloud"></a>Distribuire nel cloud

Per distribuire il modello come servizio Web in uno scenario di produzione su vasta scala, usare il servizio Azure Kubernetes. È possibile crearne uno nuovo tramite il SDK di Azure Machine Learning, CLI o il portale di Azure.

```python
# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'my-aks-9' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)

%%time
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)

#Set the web service configuration (using default here)
aks_config = AksWebservice.deploy_configuration()

%%time
aks_service_name ='aks-service-1'

aks_service = Webservice.deploy_from_image(workspace = ws, 
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
print(aks_service.scoring_uri)
```

#### <a name="test-the-cloud-service"></a>Testare il servizio cloud
L'immagine Docker supporta gRPC e il serve TensorFlow "stima" API.  Usare il client di esempio per chiamare l'immagine Docker per ottenere le stime dal modello.  Esempio di codice client è disponibile:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se si desidera utilizzare Gestione TensorFlow, puoi [scaricare un esempio di client](https://www.tensorflow.org/serving/setup).

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image using input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensor, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Pulizia del servizio
Eliminare il servizio web, immagini e modello (deve essere eseguita in quest'ordine poiché sono presenti dipendenze).

```python
aks_service.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Distribuire in un server perimetrale locale

Tutti i [dispositivi Azure Data finestra Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contengono un FPGA per l'esecuzione del modello.  Un solo modello può essere eseguito nel FPGA in una sola volta.  Per eseguire un modello diverso, è sufficiente distribuire un nuovo contenitore. Istruzioni ed esempi di codice è reperibile nel [in questo esempio di Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Proteggere i servizi web FPGA

Per informazioni sulla protezione di servizi Web FPGA, vedere il documento [Proteggere i servizi Web](how-to-secure-web-service.md).