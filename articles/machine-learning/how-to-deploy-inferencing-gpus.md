---
title: Distribuire un modello per l'inferenza con GPUDeploy a model for inference with GPU
titleSuffix: Azure Machine Learning
description: Questo articolo illustra come usare Azure Machine Learning per distribuire un modello di deep learning Tensorflow abilitato per GPU come servizio Web.service e richieste di inferenza del punteggio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398334"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuire un modello di deep learning per l'inferenza con GPUDeploy a deep learning model for inference with GPU
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come usare Azure Machine Learning per distribuire un modello abilitato per GPU come servizio Web.This article teaches you how to use Azure Machine Learning to deploy a GPU-enabled model as a web service. Le informazioni contenute in questo articolo si basano sulla distribuzione di un modello nel servizio Azure Kubernetes (AKS). Il cluster AKS fornisce una risorsa GPU utilizzata dal modello per l'inferenza.

L'inferenza, o punteggio del modello, è la fase in cui il modello distribuito viene usato per eseguire stime. L'utilizzo di GPU al posto delle CPU offre vantaggi in termini di prestazioni sul calcolo altamente parallelizzabile.

> [!IMPORTANT]
> Per le distribuzioni di servizi Web, l'inferenza GPU è supportata solo nel servizio Azure Kubernetes.For web service deployments, GPU inference is only supported on Azure Kubernetes Service. Per l'inferenza tramite una pipeline di __Machine Learning,__ le GPU sono supportate solo in Azure Machine Learning Compute. Per ulteriori informazioni sull'utilizzo delle pipeline di ML, vedere [Eseguire stime batch](how-to-use-parallel-run-step.md). 

> [!TIP]
> Anche se i frammenti di codice in questo articolo usano un modello TensorFlow, è possibile applicare le informazioni a qualsiasi framework di apprendimento automatico che supporta GPU.

> [!NOTE]
> Le informazioni contenute in questo articolo si basano sulle informazioni contenute nell'articolo Come distribuire il servizio Azure Kubernetes.The information in this article builds on the information in the [How to deploy to Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) article. Dove tale articolo in genere riguarda la distribuzione in AKS, questo articolo riguarda la distribuzione specifica GPU.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area](how-to-manage-workspace.md)di lavoro di Azure Machine Learning.For more information, see Create an Azure Machine Learning workspace .

* Un ambiente di sviluppo Python con Azure Machine Learning SDK installato. Per altre informazioni, vedere [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Modello registrato che utilizza una GPU.

    * Per informazioni su come registrare i modelli, vedere [Distribuire modelli](how-to-deploy-and-where.md#registermodel).

    * Per creare e registrare il modello Tensorflow utilizzato per creare questo documento, vedere Come eseguire il [training di un modello TensorFlow](how-to-train-tensorflow.md).

* Una conoscenza generale di [come e dove distribuire](how-to-deploy-and-where.md)i modelli .

## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

Per connettersi a un'area di lavoro esistente, utilizzare il codice seguente:To connect to an existing workspace, use the following code:

> [!IMPORTANT]
> Questo frammento di codice prevede che la configurazione dell'area di lavoro venga salvata nella directory corrente o nel relativo elemento padre. Per altre informazioni sulla creazione di un'area di lavoro, vedere Creare e gestire aree di lavoro di Azure Machine Learning.For more information on creating a workspace, see [Create and manage Azure Machine Learning workspaces.](how-to-manage-workspace.md)   Per ulteriori informazioni sul salvataggio della configurazione in un file, consultate Creare un file di [configurazione dell'area di lavoro.](how-to-configure-environment.md#workspace)

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Creare un cluster Kubernetes con GPUCreate a Kubernetes cluster with Gpu

Il servizio Azure Kubernetes offre molte opzioni GPU diverse. È possibile utilizzare uno qualsiasi di essi per l'inferenza del modello. Vedere [l'elenco delle macchine virtuali serie N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) per una suddivisione completa delle funzionalità e dei costi.

Il codice seguente illustra come creare un nuovo cluster AKS per l'area di lavoro:The following code demonstrates how to create a new AKS cluster for your workspace:

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
> Azure ti addebiterà finché esiste il cluster AKS. Assicurarsi di eliminare il cluster AKS al termine dell'operazione.

Per altre informazioni sull'uso di AKS con Azure Machine Learning, vedere [Come distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Scrivere lo script di immissione

Lo script di ingresso riceve i dati inviati al servizio Web, li passa al modello e restituisce i risultati del punteggio. Lo script seguente carica il modello Tensorflow all'avvio e quindi usa il modello per assegnare un punteggio ai dati.

> [!TIP]
> Lo script di avvio è specifico del modello. Ad esempio, lo script deve conoscere il framework da utilizzare con il modello, i formati di dati e così via.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Questo file `score.py`è denominato . Per ulteriori informazioni sugli script di ingresso, vedere [Come e dove distribuire](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definire l'ambiente conda

Il file di ambiente conda specifica le dipendenze per il servizio. Include le dipendenze richieste sia dal modello che dallo script di immissione. Si noti che è necessario indicare i valori predefiniti di azureml con verion >1.0.45 come dipendenza pip, perché contiene la funzionalità necessaria per ospitare il modello come servizio Web. Il valore YAML seguente definisce l'ambiente per un modello Tensorflow. Specifica `tensorflow-gpu`, che utilizzerà la GPU utilizzata in questa distribuzione:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

Per questo esempio, il `myenv.yml`file viene salvato come .

## <a name="define-the-deployment-configuration"></a>Definire la configurazione di distribuzioneDefine the deployment configuration

La configurazione di distribuzione definisce l'ambiente del servizio Azure Kubernetes usato per eseguire il servizio Web:The deployment configuration defines the Azure Kubernetes Service environment used to run the web service:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Per ulteriori informazioni, vedere la documentazione di riferimento relativa [a AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definire la configurazione di inferenzaDefine the inference configuration

La configurazione dell'inferenza punta allo script di voce e a un oggetto ambiente, che usa un'immagine docker con supporto GPU. Si noti che il file YAML utilizzato per la definizione dell'ambiente deve elencare i valori predefiniti di azureml con la versione >1.0.45 come dipendenza pip, perché contiene la funzionalità necessaria per ospitare il modello come servizio Web.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Per ulteriori informazioni sugli ambienti, vedere Creare e gestire ambienti per la formazione e la [distribuzione.](how-to-use-environments.md)
Per ulteriori informazioni, vedere la documentazione di riferimento relativa a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

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
> Se `InferenceConfig` l'oggetto `enable_gpu=True`dispone `deployment_target` di , il parametro deve fare riferimento a un cluster che fornisce una GPU. In caso contrario, la distribuzione avrà esito negativo.

Per ulteriori informazioni, vedere la documentazione di riferimento relativa [a Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Eseguire una query di esempio al servizioIssue a sample query to your service

Inviare una query di prova al modello distribuito. Quando si invia un'immagine jpeg al modello, l'immagine viene modellata. L'esempio di codice seguente scarica i dati di test e quindi seleziona un'immagine di test casuale da inviare al servizio.

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

Per ulteriori informazioni sulla creazione di un'applicazione client, vedere Creare client per utilizzare il [servizio Web distribuito.](how-to-consume-web-service.md)

## <a name="clean-up-the-resources"></a>Pulire le risorse

Se il cluster AKS è stato creato in modo specifico per questo esempio, eliminare le risorse al termine dell'operazione.

> [!IMPORTANT]
> Azure fattura l'utente in base alla durata della distribuzione del cluster AKS. Assicurarsi di pulirlo dopo aver finito con esso.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire il modello in FPGADeploy model on FPGA](how-to-deploy-fpga-web-service.md)
* [Distribuire il modello con ONNXDeploy model with ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Modelli DNN Train Tensorflow](how-to-train-tensorflow.md)
