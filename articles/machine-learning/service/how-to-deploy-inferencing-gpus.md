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
ms.date: 07/24/2019
ms.openlocfilehash: 9cf39230d6a2c615925222b6545a091a4be941ac
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847972"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuire un modello di apprendimento avanzato per l'inferenza con GPU

Questo articolo illustra come usare il servizio Azure Machine Learning per distribuire un modello abilitato per GPU come servizio Web. Le informazioni contenute in questo articolo si basano sulla distribuzione di un modello in Azure Kubernetes Service (AKS). Il cluster AKS fornisce una risorsa GPU utilizzata dal modello per l'inferenza.

L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene utilizzato per eseguire stime. L'uso delle GPU anziché delle CPU offre vantaggi a livello di prestazioni per il calcolo altamente eseguibili.

> [!TIP]
> Sebbene i frammenti di codice in questo articolo utilizzano un modello TensorFlow, è possibile applicare le informazioni a qualsiasi framework di Machine Learning che supporti GPU.

> [!NOTE]
> Le informazioni contenute in questo articolo si basano sulle informazioni contenute nell'articolo [How to Deploy to Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) . In questo articolo viene in genere illustrata la distribuzione in AKS, in questo articolo viene illustrata la distribuzione specifica della GPU

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro del servizio Azure Machine Learning](how-to-manage-workspace.md).

* Ambiente di sviluppo Python con Azure Machine Learning SDK installato. Per ulteriori informazioni, vedere [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Modello registrato che utilizza una GPU.

    * Per informazioni su come registrare i modelli, vedere [distribuire i modelli](../service/how-to-deploy-and-where.md#registermodel).

    * Per creare e registrare il modello Tensorflow usato per creare questo documento, vedere [come eseguire il training di un modello Tensorflow](how-to-train-tensorflow.md).

* Informazioni generali [su come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

Per connettersi a un'area di lavoro esistente, usare il codice seguente:

> [!IMPORTANT]
> Questo frammento di codice prevede che la configurazione dell'area di lavoro venga salvata nella directory corrente o nel relativo elemento padre. Per ulteriori informazioni sulla creazione di un'area di lavoro, vedere [creare e gestire aree di lavoro del servizio Azure Machine Learning](how-to-manage-workspace.md).   Per ulteriori informazioni sul salvataggio della configurazione nel file, vedere [creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Creare un cluster Kubernetes con GPU

Il servizio Kubernetes di Azure offre molte opzioni GPU diverse. È possibile usarli per l'inferenza del modello. Per una suddivisione completa delle funzionalità e dei costi, vedere [l'elenco delle VM serie N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) .

Il codice seguente illustra come creare un nuovo cluster AKS per l'area di lavoro:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

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
> Azure effettuerà la fatturazione fino a quando il cluster AKS esiste. Assicurarsi di eliminare il cluster AKS al termine dell'operazione.

Per altre informazioni sull'uso di AKS con Azure Machine Learning Service, vedere [come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Scrivere lo script di immissione

Lo script di immissione riceve i dati inviati al servizio Web, li passa al modello e restituisce i risultati di assegnazione dei punteggi. Lo script seguente carica il modello Tensorflow all'avvio, quindi usa il modello per assegnare un punteggio ai dati.

> [!TIP]
> Lo script di immissione è specifico del modello. Ad esempio, lo script deve essere in grado di comprendere il Framework da usare con il modello, i formati di dati e così via.

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
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, 'mnist-tf.model.meta'))
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

Questo file è denominato `score.py`. Per ulteriori informazioni sugli script di immissione, vedere [How and where to deploy](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definire l'ambiente conda

Il file dell'ambiente conda specifica le dipendenze per il servizio. Sono incluse le dipendenze richieste dal modello e dallo script di immissione. Il YAML seguente definisce l'ambiente per un modello Tensorflow. Specifica `tensorflow-gpu`che utilizzerà la GPU utilizzata in questa distribuzione:

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

Per questo esempio, il file viene salvato come `myenv.yml`.

## <a name="define-the-deployment-configuration"></a>Definire la configurazione della distribuzione

La configurazione della distribuzione definisce l'ambiente del servizio Kubernetes di Azure usato per eseguire il servizio Web:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Per ulteriori informazioni, vedere la documentazione di riferimento per [AksService. deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-).

## <a name="define-the-inference-configuration"></a>Definire la configurazione dell'inferenza

La configurazione dell'inferenza punta allo script di immissione e al file dell'ambiente conda. Abilita anche il supporto GPU, che installa CUDA nell'immagine Docker creata per il servizio Web:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml",
                                   enable_gpu=True)
```

Per ulteriori informazioni, vedere la documentazione di riferimento per [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Distribuire il modello

Distribuire il modello nel cluster AKS e attendere che crei il servizio.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> Se l' `InferenceConfig` oggetto ha `enable_gpu=True`, il `deployment_target` parametro deve fare riferimento a un cluster che fornisce una GPU. In caso contrario, la distribuzione avrà esito negativo.

Per ulteriori informazioni, vedere la documentazione di riferimento per il [modello](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Eseguire una query di esempio per il servizio

Inviare una query di test al modello distribuito. Quando si invia un'immagine JPEG al modello, l'immagine viene punteggiata. L'esempio di codice seguente Scarica i dati di test e quindi seleziona un'immagine di test casuale da inviare al servizio. 

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Per ulteriori informazioni sulla creazione di un'applicazione client, vedere [creare client per utilizzare il servizio Web distribuito](how-to-consume-web-service.md).

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
