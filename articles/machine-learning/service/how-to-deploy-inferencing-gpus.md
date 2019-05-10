---
title: Come distribuire un modello di apprendimento avanzato per inferenza con GPU
titleSuffix: Azure Machine Learning service
description: Informazioni su come distribuire un modello di apprendimento avanzato come servizio web che usa una GPU per inferenza. In questo articolo, viene distribuito un modello Tensorflow in un cluster Azure Kubernetes Service. Il cluster Usa una VM abilitate per GPU per ospitare il servizio web e le richieste di inferenza di punteggio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515175"
---
# <a name="how-to-do-gpu-inferencing"></a>Come eseguire questa operazione inferenza GPU

Informazioni su come usare inferenza GPU per un modello di machine learning distribuito come servizio web. In questo articolo descrive come usare il servizio Azure Machine Learning per distribuire un modello di apprendimento approfondito Tensorflow di esempio. Il modello viene distribuito in un cluster Azure Kubernetes Service (AKS) che usa una macchina virtuale basata su GPU per ospitare il servizio. Quando le richieste vengono inviate al servizio, il modello Usa la GPU per eseguire l'inferenza.

Le GPU offrono vantaggi di prestazioni rispetto alla CPU su calcolo altamente parallelizzabile. Training e inferenza dei modelli (soprattutto per i batch di grandi dimensioni delle richieste) per l'apprendimento avanzato sono casi d'uso eccellente per GPU.  

In questo esempio mostra come distribuire un modello TensorFlow salvati in Azure Machine Learning. 

## <a name="goals-and-prerequisites"></a>Gli obiettivi e prerequisiti

Seguire le istruzioni per:
* Creare una GPU abilitata cluster servizio contenitore di AZURE
* Distribuire un modello con Tensorflow GPU

Prerequisiti:
* Area di lavoro di Azure Machine Learning services
* Python
* Tensorflow SavedModel registrato. Per informazioni su come registrare i modelli vedere [distribuzione di modelli](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Questo articolo si basa sul [distribuzione di modelli di Tensorflow per AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), che usa TensorFlow salvato modella e distribuisce a un cluster del servizio contenitore di AZURE. Tuttavia, con piccole modifiche per il file di assegnazione dei punteggi e il file di ambiente è applicabile a qualsiasi framework di machine learning che supporta le GPU.  

## <a name="provision-aks-cluster-with-gpus"></a>Cluster AKS effettuare il provisioning con GPU
Azure offre molte opzioni GPU, ognuno dei quali può essere utilizzato per inferenza. Visualizzare [l'elenco di serie N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) per un elenco completo delle funzionalità e i costi. 

Per altre informazioni sull'uso di servizio contenitore di AZURE con servizio di Azure Machine Learning, vedere il [come distribuire e in cui l'articolo.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Verrà fatturata da Azure è fino a quando viene eseguito il provisioning di cluster AKS. Assicurarsi di eliminare il cluster AKS, dopo aver terminato di usarlo.


## <a name="write-entry-script"></a>Scrivere script di ingresso

Salvare il codice seguente alla directory di lavoro come `score.py`. Questo file verrà utilizzato per calcolare il punteggio immagini come vengono inviati al servizio. Questo file viene caricato il TensorFlow salvato modello e quindi ogni post di richiesta passa l'immagine di input per la sessione di TensorFlow e restituisce i punteggi risultanti.
Altri framework di inferenza richiederanno diversi file di assegnazione dei punteggi.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Definire l'ambiente Conda
Creare un file di ambiente conda denominato `myenv.yml` per specificare le dipendenze per il servizio. È importante specificare che si sta utilizzando `tensorflow-gpu` per ottenere prestazioni accelerate.
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>Definire InferenceConfig GPU

Creare un [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) che consente di specificare che si sta abilitando GPU. Ciò garantirà che CUDA sia installato nell'immagine.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

Per altre informazioni, vedere [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) e [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
## <a name="deploy-the-model"></a>Distribuire il modello

Distribuire il modello nel cluster AKS e attendere che venga creato il servizio.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Servizio Azure Machine Learning non verrà distribuito un modello con un `InferenceConfig` GPU che prevede un cluster senza GPU.

Per altre informazioni, vedere [modello](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Query di esempio di problema per distribuzione modello

Eseguire una query di esempio per il modello distribuito. Questo modello verrà assegnare un punteggio qualsiasi immagine jpeg di inviare una richiesta post. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Per ottimizzare la latenza e la velocità effettiva, il client deve trovarsi nella stessa area di Azure in cui si trova l'endpoint.  Le API vengono attualmente create nell'area Stati Uniti orientali.

## <a name="cleaning-up-the-resources"></a>Pulitura delle risorse

Eliminare le risorse dopo aver completato la demo.

> [!IMPORTANT]
> Verrà fatturata da Azure è basato su quanto tempo viene distribuito il cluster AKS. Assicurarsi di pulizia dopo avere completato con esso.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli in FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Distribuire modelli con ONNX](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Il training dei modelli di rete neurale profonda Tensorflow](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
