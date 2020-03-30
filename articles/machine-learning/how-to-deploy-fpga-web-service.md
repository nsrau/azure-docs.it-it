---
title: Cosa sono FPGA - come eseguire la distribuzione
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire un servizio Web con un modello in esecuzione in FPGA con Azure Machine Learning per un'inferenza a latenza ultrabassa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8cb6cf49e302122849dc2402bcff008e72e15608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472359"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Che cosa sono gli array gate programmabili sul campo (FPGA) e come distribuirlo
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo fornisce un'introduzione agli array di gate a livello di campo (FPGA) e illustra come distribuire i modelli usando Azure Machine Learning in Azure FPGA.

Un circuito FPGA contiene un array di blocchi programmabili per la logica e una gerarchia di interconnessioni riconfigurabili. Le interconnessioni consentono di configurare questi blocchi in diversi modi dopo la produzione. I circuiti FPGA offrono una combinazione di programmabilità e prestazioni superiore agli altri chip.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA rispetto a CPU, GPU e ASIC

Il diagramma e la tabella seguenti evidenziano un confronto tra i circuiti FPGA e gli altri processori.

![Diagramma del confronto FPGA di Azure Machine Learning](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processore||Descrizione|
|---|:-------:|------|
|Application-specific integrated circuit|ASIC|I circuiti personalizzati, ad esempio Google TensorFlow Processor Unit (TPU), offrono la massima efficienza. Non possono essere riconfigurati in base alle esigenze.|
|Field-programmable Gate Arrays|FPGA|I circuiti FPGA, ad esempio quelli disponibili in Azure, assicurano prestazioni simili a quelle dei circuiti ASIC. Sono flessibili e possono essere riconfigurati nel corso del tempo per implementare la nuova logica.|
|Graphics processing unit|GPU|Una scelta comune per i calcoli di intelligenza artificiale che offre funzionalità di elaborazione parallela e rende più veloce il rendering delle immagini rispetto alle CPU.|
|Central processing unit|CPU|Processori generici, le cui prestazioni non sono ideali per l'elaborazione di video e grafica.|

Gli FPGA su Azure si basano sui dispositivi FPGA di Intel, che gli scienziati dei dati e gli sviluppatori utilizzano per accelerare i calcoli dell'iA in tempo reale. L'architettura abilitata per i circuiti FPGA offre un livello elevato di prestazioni, flessibilità, scalabilità ed è disponibile in Azure.

Gli FPGA consentono di ottenere una latenza ridotta per le richieste di inferenza in tempo reale (o punteggio del modello). Non sono necessarie richieste asincrone (invio in batch). L'invio in batch può causare latenza perché rende necessario elaborare più dati. Le implementazioni di unità di elaborazione neurale non richiedono l'invio in batch; pertanto la latenza può essere molto inferiore rispetto ai processori CPU e GPU.

### <a name="reconfigurable-power"></a>Potenza riconfigurabile
I circuiti FPGA possono essere riconfigurati per diversi tipi di modelli di Machine Learning. Questa flessibilità consente di accelerare le applicazioni basandosi sul modello ottimale di memoria e precisione numerica utilizzato. Dal momento che sono riconfigurabili, è possibile rimanere al passo con i requisiti degli algoritmi AI in costante evoluzione.

## <a name="whats-supported-on-azure"></a>Attività supportate su Azure
Microsoft Azure ha realizzato l'investimento in dispositivi FPGA per il cloud più importante al mondo. Con questa architettura hardware abilitata per FPGA, le reti neurali sottoposte a training vengono eseguite rapidamente e con una latenza inferiore. Azure può parallelizzare le reti neurali profonde (DNN) pre-addestrate tra FPGA per scalare orizzontalmente il servizio. È possibile eseguire training preliminare sulle reti neurali profonde, per utilità di funzioni complete per il trasferimento di apprendimento o per ottimizzazione con pesi aggiornati.

FPGA in Azure supporta:FPGAAs on Azure supports:

+ Scenari di riconoscimento e classificazione di immagini
+ Distribuzione di TensorFlow
+ Hardware Intel FPGA

Questi modelli DNN sono attualmente disponibili:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA sono disponibili in queste aree di Azure:FPGAs are available in these Azure regions:
  - Stati Uniti orientali
  - Asia sud-orientale
  - Europa occidentale
  - Stati Uniti occidentali 2

> [!IMPORTANT]
> Per ottimizzare la latenza e la velocità effettiva, il client che invia i dati al modello FPGA deve trovarsi in una delle aree precedenti (quella in cui è stato distribuito il modello).

La **famiglia PBS delle macchine virtuali di Azure** contiene FPGA di Intel Arria 10. Verrà visualizzato come "Standard PBS Family vCPU" quando si controlla l'allocazione delle quote di Azure.It will show as "Standard PBS Family vCPU" when you check your Azure quota allocation. La macchina virtuale PB6 dispone di sei vCPU e un FPGA e verrà automaticamente eseguito il provisioning da Azure ML come parte della distribuzione di un modello in un FPGA. Viene usato solo con Azure ML e non può eseguire flussi di bit arbitrari. Ad esempio, non sarà possibile lampeggiare l'FPGA con bitstream per eseguire la crittografia, la codifica, ecc.

### <a name="scenarios-and-applications"></a>Scenari e applicazioni

Gli FPGA di Azure sono integrati con Azure Machine Learning.Azure FPGA are integrated with Azure Machine Learning. Microsoft usa i circuiti FPGA per la valutazione delle reti neurali profonde, per la classificazione delle ricerche Bing e per l'accelerazione della tecnologia SDN (Software Defined Networking) per ridurre la latenza, rendendo disponibile la CPU per altre attività.

Gli scenari seguenti utilizzano FPGA:
+ [Sistema di ispezione ottica automatizzato](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mappatura della copertura del suolo](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>Esempio: distribuire modelli in FPGAExample: Deploy models on FPGAAs

È possibile distribuire un modello come servizio Web in FPGA con i modelli con accelerazione hardware di Azure Machine Learning.You can deploy a model as a web service on FPGAAs with Azure Machine Learning Hardware Accelerated Models. L'utilizzo di FPGA garantisce un'inferenza di latenza ultra-bassa, anche con una singola dimensione batch. L'inferenza, o punteggio del modello, è la fase in cui il modello distribuito viene usato per la stima, in genere nei dati di produzione.

### <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.  Se non se ne ha una, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- quota FPGA. Usare l'interfaccia della riga di comando di Azure per verificare se si dispone della quota:Use the Azure CLI to check whether you have quota:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Le altre posizioni ``southeastasia`` ``westeurope``possibili ``westus2``sono , , e .

    Il comando restituisce un testo simile al seguente:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Assicurarsi di disporre di almeno 6 vCPU in __CurrentValue__.

    Se non si dispone di quota, [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)inviare una richiesta a .

- Un'area di lavoro di Azure Machine Learning e Azure Machine Learning SDK per Python installata. Per ulteriori informazioni, consultate [Creare un'area di lavoro.](how-to-manage-workspace.md)
 
- Python SDK per modelli con accelerazione hardware:

    ```bash
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Creare e creare modelli

Questo documento descrive come creare un grafico TensorFlow per pre-elaborare l'immagine di input, renderlo un featurizer utilizzando ResNet 50 su un FPGA e quindi eseguire le funzionalità tramite un classificatore addestrato sul set di dati ImageNet.

Seguire le istruzioni per:

* Definire il modello TensorFlowDefine the TensorFlow model
* Convertire il modello
* Distribuire il modello
* Utilizzare il modello distribuito
* Eliminare i servizi distribuiti

Usare [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) per creare una definizione del servizio. Una definizione del servizio è un file che descrive una pipeline di grafici (input, utilità di funzioni e classificatore) basata su TensorFlow. Il comando di distribuzione comprime automaticamente la definizione e i grafici in un file ZIP e carica il file zip in Archiviazione BLOB di Azure. Il DNN è già distribuito per l'esecuzione in FPGA.

### <a name="load-azure-machine-learning-workspace"></a>Caricare l'area di lavoro di Azure Machine Learning

Caricare l'area di lavoro di Azure Machine Learning.Load your Azure Machine Learning workspace.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Pre-elaborare l'immagine

L'input per il servizio Web è un'immagine JPEG.  Il primo passo è decodificare l'immagine JPEG e pre-elaborarla.  Le immagini JPEG vengono trattate come stringhe e il risultato sono tensori che saranno l'input per il modello ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Featurizer di carico

Inizializzare il modello e scaricare un checkpoint TensorFlow della versione quantizzata ResNet50 da usare come utilità di funzioni.  È possibile sostituire "QuantizedResnet50" nel frammento di codice seguente con l'importazione di altre reti neurali profonde:You may replace "QuantizedResnet50" in the code snippet below with importing other deep neural networks:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Aggiungi classificatore

Questo classificatore è stato eseguito con training sul set di dati ImageNet.  Esempi di apprendimento per il trasferimento e formazione dei pesi personalizzati sono disponibili nel set di blocchi appunti di [esempio.](https://aka.ms/aml-notebooks)

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Salvare il modello

Ora che il preprocessore ResNet 50 featurizer e il classificatore sono stati caricati, salvare il grafico e le variabili associate come modello.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Salvare i tensori di input e di uscita
I tensori di input e output creati durante i passaggi di pre-elaborazione e classificatore saranno necessari per la conversione e l'inferenza del modello.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Salvare i tensori di input e output perché sono necessari per le richieste di conversione e inferenza del modello.

I modelli disponibili e i corrispondenti tensori di output del classificatore predefinito sono riportati di seguito, ovvero ciò che si utilizzerebbe per l'inferenza se è stato utilizzato il classificatore predefinito.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registrare il modello

[Registrare](concept-model-management-and-deployment.md) il modello usando l'SDK con il file zip nell'archivio BLOB di Azure.Register the model by using the SDK with the .ip file in Azure Blob storage. L'aggiunta di tag e altri metadati sul modello consente di tenere traccia dei modelli sottoposti a training.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Se è già stato registrato un modello e si desidera caricarlo, è possibile recuperarlo.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Converti modello

Convertire il grafico TensorFlow nel formato Open Neural Network Exchange ([ONNX](https://onnx.ai/)).  È necessario fornire i nomi dei tensori di input e output e questi nomi verranno utilizzati dal client quando si utilizza il servizio Web.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Creare un'immagine Docker

Il modello convertito e tutte le dipendenze vengono aggiunti a un'immagine Docker.The converted model and all dependencies are added to a Docker image.  Questa immagine Docker può quindi essere distribuita e creata un'istanza.  Le destinazioni di distribuzione supportate includono AKS nel cloud o un dispositivo perimetrale, ad esempio [Azure Data Box Edge.](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)  È inoltre possibile aggiungere tag e descrizioni per l'immagine Docker registrata.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Elencare le immagini in base al tag e ottenere i log dettagliati per qualsiasi debug.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Distribuire nel cloud o nell'edge

### <a name="deploy-to-the-cloud"></a>Distribuzione nel cloud

Per distribuire il modello come servizio Web in uno scenario di produzione su vasta scala, usare il servizio Azure Kubernetes. È possibile crearne uno nuovo usando Azure Machine Learning SDK, cli o [Azure Machine Learning Studio.](https://ml.azure.com)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

La distribuzione di AKS può richiedere circa 15 minuti.  Verificare se la distribuzione è riuscita.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Distribuire il contenitore nel cluster AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Testare il servizio cloudTest the cloud service
L'immagine Docker supporta gRPC e l'API "predict" di TensorFlow.  Usare il client di esempio per chiamare nell'immagine Docker per ottenere stime dal modello.  Codice client di esempio è disponibile:Sample client code is available:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se si desidera utilizzare TensorFlow Serving, è possibile [scaricare un client di esempio.](https://www.tensorflow.org/serving/setup)

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Poiché questo classificatore è stato addestrato sul set di dati [ImageNet,](http://www.image-net.org/) eseguire il mapping delle classi alle etichette leggibili dall'utente.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Pulire il servizio
Eliminare il servizio Web, l'immagine e il modello (deve essere eseguito in questo ordine poiché sono presenti dipendenze).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Distribuire in un server perimetrale locale

Tutti i [dispositivi Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contengono un'FPGA per l'esecuzione del modello.  In FPGA è possibile eseguire un solo modello alla volta.  Per eseguire un modello diverso, è sufficiente distribuire un nuovo contenitore. Le istruzioni e il codice di esempio sono disponibili in [questo esempio di Azure.Instructions](https://github.com/Azure-Samples/aml-hardware-accelerated-models)and sample code can be found in this Azure Sample .

## <a name="secure-fpga-web-services"></a>Proteggere i servizi web FPGA

Per proteggere i servizi Web FPGA, vedere il documento [Secure Web Services( Secure Web Services).](how-to-secure-web-service.md)

## <a name="next-steps"></a>Passaggi successivi

Guarda questi blocchi appunti, video e blog:

+ Diversi [blocchi appunti di esempio](https://aka.ms/aml-accel-models-notebooks)

+ [Hardware hyperscale: ML in scala in cima ad Azure : FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063) (Informazioni sul cloud configurabile basato su Microsoft FPGA)

+ [Microsoft unveils Project Brainwave for real-time AI: project homepage](https://www.microsoft.com/research/project/project-brainwave/) (Microsoft presenta Project Brainwave per AI in tempo reale: home page)
