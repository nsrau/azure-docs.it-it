---
title: Distribuire un modello per inferenza con GPU
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come usare il servizio Azure Machine Learning per distribuire un modello come un service.service web e le richieste di inferenza di punteggio per l'apprendimento avanzato Tensorflow abilitate per GPU.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543809"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuire un modello di apprendimento avanzato per inferenza con GPU

Questo articolo illustra come usare il servizio Azure Machine Learning per distribuire un modello come servizio web di apprendimento approfondito Tensorflow abilitate per GPU.

Distribuire il modello a un cluster Azure Kubernetes Service (AKS) per eseguire l'inferenza abilitate per GPU. Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima. Utilizzo GPU anziché CPU offrono prestazioni vantaggi nel calcolo altamente parallelizzabile.

Anche se in questo esempio Usa un modello TensorFlow, è possibile applicare la procedura seguente per qualsiasi framework di machine learning che supporta le GPU, apportando piccole modifiche al file di assegnazione dei punteggi e il file dell'ambiente. 

In questo articolo, si eseguono i passaggi seguenti:

* Creare un cluster AKS abilitate per GPU
* Distribuire un modello Tensorflow GPU
* Eseguire una query di esempio per il modello distribuito

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di servizi Azure Machine Learning.
* Una distribuzione di Python.
* Un Tensorflow registrato salvate modello.
    * Per informazioni su come registrare i modelli, vedere [distribuzione di modelli](../service/how-to-deploy-and-where.md#registermodel).

È possibile completare prima parte di questa serie di pratiche [come eseguire il training di un modello TensorFlow](how-to-train-tensorflow.md), per soddisfare i prerequisiti necessari.

## <a name="provision-an-aks-cluster-with-gpus"></a>Effettuare il provisioning di un cluster AKS con GPU

Azure offre molte opzioni GPU. È possibile usare uno di essi per inferenza. Visualizzare [l'elenco di macchine virtuali serie N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) per un elenco completo delle funzionalità e i costi.

Per altre informazioni sull'uso di servizio contenitore di AZURE con servizio di Azure Machine Learning, vedere [come distribuire e dove](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Verrà fatturata da Azure è fino a quando viene eseguito il provisioning di cluster AKS. Assicurarsi di eliminare il cluster AKS, al termine con esso.

## <a name="write-the-entry-script"></a>Scrivere lo script di ingresso

Salvare il codice seguente alla directory di lavoro come `score.py`. Questo file punteggi immagini quando vengono inviati al servizio. Carica il modello salvato TensorFlow, passa l'immagine di input per la sessione di TensorFlow per ogni richiesta POST e quindi restituisce i punteggi risultanti. Altri framework di inferenza richiedono diversi file di assegnazione dei punteggi.

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

Creare un file di ambiente conda denominato `myenv.yml` per specificare le dipendenze per il servizio. È importante specificare che si usi `tensorflow-gpu` per ottenere prestazioni accelerate.

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

Creare un `InferenceConfig` oggetto che abilita le GPU e assicura che con l'immagine Docker sia installato CUDA.

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

## <a name="issue-a-sample-query-to-your-model"></a>Eseguire una query di esempio per il modello

Inviare una query di test al modello distribuito. Quando si invia un'immagine jpeg al modello, assegna un punteggio l'immagine. Esempio di codice seguente usa una funzione di utilità esterne per caricare immagini. È possibile trovare il codice pertinente in pir [TensorFlow esempio su GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

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
