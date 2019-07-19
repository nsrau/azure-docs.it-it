---
title: Distribuire un modello per l'inferenza con GPU
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come usare il servizio Azure Machine Learning per distribuire un modello di apprendimento avanzato Tensorflow abilitato per GPU come servizio Web. servizio e richieste di inferenza dei punteggi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326982"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuire un modello di apprendimento avanzato per l'inferenza con GPU

Questo articolo illustra come usare il servizio Azure Machine Learning per distribuire un modello di apprendimento avanzato Tensorflow abilitato per GPU come servizio Web.

Distribuire il modello in un cluster Azure Kubernetes Service (AKS) per eseguire l'inferenza tramite GPU. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene utilizzato per la stima. L'uso di GPU anziché delle CPU offre vantaggi a livello di prestazioni per il calcolo altamente eseguibili.

Sebbene in questo esempio venga usato un modello TensorFlow, è possibile applicare i passaggi seguenti a qualsiasi framework di Machine Learning che supporti GPU apportando piccole modifiche al file di assegnazione dei punteggi e al file dell'ambiente. 

In questo articolo, si eseguono i passaggi seguenti:

* Creare un cluster AKS abilitato per GPU
* Distribuire un modello Tensorflow GPU
* Eseguire una query di esempio per il modello distribuito

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning Services.
* Una distribuzione di Python.
* Modello salvato Tensorflow registrato.
    * Per informazioni su come registrare i modelli, vedere [distribuire i modelli](../service/how-to-deploy-and-where.md#registermodel).

È possibile completare la prima parte di questa serie di procedure, [come eseguire il training di un modello TensorFlow](how-to-train-tensorflow.md)per soddisfare i prerequisiti necessari.

## <a name="provision-an-aks-cluster-with-gpus"></a>Effettuare il provisioning di un cluster AKS con GPU

Azure dispone di molte opzioni GPU diverse. È possibile usarli per l'inferenza. Per una suddivisione completa delle funzionalità e dei costi, vedere [l'elenco delle VM serie N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) .

Per ulteriori informazioni sull'utilizzo di AKS con Azure Machine Learning Service, vedere [How to deploy and where](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure verrà fatturato fino a quando viene effettuato il provisioning del cluster AKS. Assicurarsi di eliminare il cluster AKS al termine dell'operazione.

## <a name="write-the-entry-script"></a>Scrivere lo script di immissione

Salvare il codice seguente nella directory di lavoro come `score.py`. Questo file assegna un punteggio alle immagini man mano che vengono inviate al servizio. Carica il modello salvato TensorFlow, passa l'immagine di input alla sessione TensorFlow a ogni richiesta POST e quindi restituisce i punteggi risultanti. Altri Framework di inferenza richiedono file di assegnazione dei punteggi diversi.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>Definire l'ambiente conda

Creare un file di ambiente conda `myenv.yml` denominato per specificare le dipendenze per il servizio. È importante specificare che si sta usando `tensorflow-gpu` per ottenere prestazioni accelerate.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>Definire la classe InferenceConfig GPU

Creare un `InferenceConfig` oggetto che Abilita le GPU e garantisce che CUDA venga installato con l'immagine docker.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

Per altre informazioni, vedere:

- [Classe InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Classe AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Distribuire il modello

Distribuire il modello nel cluster AKS e attendere che crei il servizio.

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
> Azure Machine Learning servizio non distribuirà un modello con `InferenceConfig` un oggetto che prevede che la GPU sia abilitata per un cluster che non dispone di una GPU.

Per ulteriori informazioni, vedere [classe modello](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Eseguire una query di esempio per il modello

Inviare una query di test al modello distribuito. Quando si invia un'immagine JPEG al modello, l'immagine viene punteggiata. Nell'esempio di codice seguente viene utilizzata una funzione di utilità esterna per caricare le immagini. È possibile trovare il codice pertinente nell' [esempio PIR TensorFlow su GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> Per ridurre al minimo la latenza e ottimizzare la velocità effettiva, assicurarsi che il client si trovi nella stessa area di Azure dell'endpoint. In questo esempio le API vengono create nell'area di Azure Stati Uniti orientali.

## <a name="clean-up-the-resources"></a>Pulire le risorse

Se il cluster AKS è stato creato in modo specifico per questo esempio, eliminare le risorse al termine dell'operazione.

> [!IMPORTANT]
> Azure addebita la fatturazione in base al tempo di distribuzione del cluster AKS. Assicurarsi di pulirlo al termine dell'operazione.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire il modello in FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Distribuire il modello con ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Eseguire il training di modelli DNN di Tensorflow](../service/how-to-train-tensorflow.md)
