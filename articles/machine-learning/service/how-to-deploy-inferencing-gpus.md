---
title: Distribuire un modello per inferenza con GPU
titleSuffix: Azure Machine Learning service
description: Informazioni su come distribuire un modello di apprendimento avanzato come servizio web che usa una GPU per l'inferenza. In questo articolo, viene distribuito un modello Tensorflow in un cluster Azure Kubernetes Service. Il cluster Usa una VM abilitate per GPU per ospitare il servizio web e le richieste di inferenza di punteggio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5f455d4f972153af934ab8966d0f1753fc55aa21
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205908"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuire un modello di apprendimento avanzato per inferenza con GPU

Informazioni su come usare l'inferenza GPU per un modello di machine learning distribuito come servizio web. Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima, in genere sui dati di produzione.

Questo articolo illustra come usare il servizio Azure Machine Learning per distribuire un esempio Tensorflow model a un cluster Azure Kubernetes Service (AKS) in una macchina virtuale (VM) di supporto per GPU per l'apprendimento avanzato. Quando le richieste vengono inviate al servizio, il modello Usa la GPU per eseguire i carichi di lavoro di inferenza.

Le GPU offrono vantaggi di prestazioni rispetto alla CPU su calcolo altamente parallelizzabile. Casi d'uso eccellente per le macchine virtuali abilitate per GPU includono apprendimento avanzato del modello di training e inferenza, in particolare per batch di grandi dimensioni delle richieste.

Questo esempio illustra come distribuire un TensorFlow salvato modello in Azure Machine Learning. Seguire questa procedura:

* Creare un cluster AKS abilitate per GPU
* Distribuire un modello Tensorflow GPU

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di servizi di Azure Machine Learning
* A Python distro
* Un Tensorflow registrato salvate modello. Per informazioni su come registrare i modelli, vedere [distribuzione di modelli](../service/how-to-deploy-and-where.md#registermodel).

Questo articolo si basa sul notebook di Jupyter [distribuzione di modelli di Tensorflow per AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb). Il notebook di Jupyter Usa TensorFlow salvato modelli e li distribuisce in un cluster AKS. È inoltre possibile applicare il notebook per qualsiasi framework di machine learning che supporta le GPU, apportando piccole modifiche al file di assegnazione dei punteggi e il file dell'ambiente.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Effettuare il provisioning di un cluster AKS con GPU

Azure offre molte opzioni GPU. È possibile usare uno di essi per inferenza. Visualizzare [l'elenco di macchine virtuali serie N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) per un elenco completo delle funzionalità e i costi.

Per altre informazioni sull'uso di servizio contenitore di AZURE con servizio di Azure Machine Learning, vedere [come distribuire e dove](../service/how-to-deploy-and-where.md#deploy-aks).

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
> Verrà fatturata da Azure è fino a quando viene eseguito il provisioning di cluster AKS. Assicurarsi di eliminare il cluster AKS, al termine con esso.

## <a name="write-the-entry-script"></a>Scrivere lo script di ingresso

Salvare il codice seguente alla directory di lavoro come `score.py`. Questo file punteggi immagini quando vengono inviati al servizio. Carica il modello salvato TensorFlow, passa l'immagine di input per la sessione di TensorFlow per ogni richiesta POST e quindi restituisce i punteggi risultanti. Altri framework di inferenza richiedono diversi file di assegnazione dei punteggi.

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

## <a name="define-the-conda-environment"></a>Definire l'ambiente conda

Creare un file di ambiente conda denominato `myenv.yml` per specificare le dipendenze per il servizio. È importante specificare che si usi `tensorflow-gpu` per ottenere prestazioni accelerate.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Definire la classe InferenceConfig GPU

Creare un `InferenceConfig` oggetto che abilita le GPU e assicura che con l'immagine Docker sia installato CUDA.

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
                                   enable_gpu=True)
```

Per altre informazioni, vedere:

- [Classe InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Classe AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Distribuire il modello

Distribuire il modello nel cluster AKS e attendere che venga creato il servizio.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Servizio Azure Machine Learning non distribuita un modello con un `InferenceConfig` oggetto previsto GPU deve essere abilitata in un cluster che non ha una GPU.

Per altre informazioni, vedere [classe modello](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Eseguire una query di esempio per il modello distribuito

Inviare una query di test al modello distribuito. Quando si invia un'immagine jpeg al modello, assegna un punteggio l'immagine.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Per ridurre al minimo la latenza e ottimizzare la velocità effettiva, verificare che il client sia nella stessa area di Azure come endpoint. In questo esempio, le API vengono create nell'area di Azure di East US.

## <a name="clean-up-the-resources"></a>La pulizia delle risorse

Eliminare le risorse dopo aver terminato con questo esempio.

> [!IMPORTANT]
> Fatture di Azure che è basato su quanto tempo viene distribuito il cluster AKS. Assicurarsi di pulizia dopo avere completato con esso.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli in FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Distribuire modelli con ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Il training dei modelli di rete neurale profonda Tensorflow](../service/how-to-train-tensorflow.md)
