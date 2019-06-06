---
title: Come e dove distribuire i modelli
titleSuffix: Azure Machine Learning service
description: Informazioni su come e dove distribuire i modelli del servizio Azure Machine Learning, inclusi Istanze di Azure Container, il servizio Azure Kubernetes, Azure IoT Edge e dispositivi FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/31/2019
ms.custom: seoapril2019
ms.openlocfilehash: 1be9d11db9a1c614614e0a4023f84b15588ba5f0
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742943"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuire modelli con il servizio di Azure Machine Learning

Informazioni su come distribuire il modello di machine learning come servizio web nel cloud di Azure o nei dispositivi IoT Edge. 

Il flusso di lavoro è simile indipendentemente [in cui si distribuisce](#target) del modello:

1. Registrare il modello.
1. Preparare la distribuzione (specifica la destinazione di calcolo di asset, utilizzo,)
1. Distribuire il modello per la destinazione di calcolo.
1. Testare il modello distribuito, l'acronimo di servizio web.

Per altre informazioni sui concetti relativi al flusso di lavoro di distribuzione, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prerequisiti

- Un modello. Se non è un modello con training, è possibile usare il modello di & file di dipendenza fornita [in questa esercitazione](https://aka.ms/azml-deploy-cloud).

- Il [estensione di comando di Azure per il servizio di Machine Learning](reference-azure-machine-learning-cli.md), [SDK Python di Azure Machine Learning](https://aka.ms/aml-sdk), o nella [estensione Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a id="registermodel"></a> Registrare il modello

Registrare i modelli di machine learning nell'area di lavoro Azure Machine Learning. Il modello può provenire da Azure Machine Learning o può provenire da altrove. Gli esempi seguenti illustrano come registrare un modello dal file:

### <a name="register-a-model-from-an-experiment-run"></a>Registrare un modello da un esperimento eseguito

+ **Esempio di Scikit-Learn con il SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```
+ **Usando l'interfaccia della riga di comando**
  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```


+ **Usando Visual Studio Code**

  Registrare i modelli usando tutti i file di modello o le cartelle con il [Visual Studio Code](how-to-vscode-tools.md#deploy-and-manage-models) estensione.

### <a name="register-an-externally-created-model"></a>Registrare un modello creato esternamente

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

È possibile registrare un modello creato esternamente, fornendo una **percorso locale** al modello. È possibile fornire una cartella o un singolo file.

+ **Esempio ONNX con Python SDK:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

+ **Usando l'interfaccia della riga di comando**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

**Tempo stimato**: circa 10 secondi.

Per altre informazioni, vedere la documentazione di riferimento per la [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo

Di seguito, destinazioni di calcolo o le risorse di calcolo, può essere usato per ospitare la distribuzione del servizio web. 

| Destinazione del calcolo | Uso | Descrizione |
| ----- | ----- | ----- |
| [Servizio web locale](#local) | Test/debug | Soluzione idonea per limitate di test e risoluzione dei problemi.
| [Servizio Azure Kubernetes](#aks) | Inferenza in tempo reale | Soluzione ideale per le distribuzioni di produzione su vasta scala. Fornisce la scalabilità automatica e tempi di risposta rapidi. |
| [Istanze di Azure Container](#aci) | Test | Soluzione idonea per la scala ridotta, carichi di lavoro basati su CPU. |
| [Ambiente di calcolo di Machine Learning](how-to-run-batch-predictions.md) | Inferenza del batch | Eseguire l'inferenza del batch nel calcolo senza server. Supporta le macchine virtuali con priorità bassa o normale. |
| [Azure IoT Edge](#iotedge) | (Anteprima) Modulo di IoT | Distribuire e gestire modelli di Machine Learning nei dispositivi IoT. |


## <a name="prepare-to-deploy"></a>Preparare la distribuzione

Per distribuire come servizio web, è necessario creare una configurazione di inferenza (`InferenceConfig`) e una configurazione di distribuzione. Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima, in genere sui dati di produzione. Nel file di configurazione l'inferenza, specificare gli script e le dipendenze necessarie per gestire il modello. Nel file di configurazione di distribuzione è specificare informazioni dettagliate su come rendere disponibile il modello di destinazione di calcolo.


### <a id="script"></a> 1. Definire le dipendenze & script di ingresso

Lo script di ingresso riceve i dati inviati a un servizio web distribuito e lo passa al modello. Riceve quindi la risposta restituita dal modello e la restituisce al client. **Lo script è specifico per il modello**; è necessario comprendere i dati che il modello accetta e restituisce.

Lo script contiene due funzioni che caricano ed eseguono il modello:

* `init()`: questa funzione carica in genere il modello in un oggetto globale. Questa funzione viene eseguita solo una volta quando viene avviato il contenitore Docker per il servizio web.

* `run(input_data)`: questa funzione usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON. È anche possibile usare dati binari non elaborati. È possibile trasformare i dati prima dell'invio al modello o prima della restituzione al client.

#### <a name="optional-automatic-swagger-schema-generation"></a>(Facoltativo) Generazione automatica dello schema di Swagger

Per automaticamente generare uno schema per il servizio web, fornire un esempio di input e/o di output nel costruttore per un esempio e il tipo e gli oggetti di tipo definito, vengono utilizzati per creare automaticamente lo schema. Servizio Azure Machine Learning crea quindi un' [OpenAPI](https://swagger.io/docs/specification/about/) specifica (Swagger) per il servizio web durante la distribuzione.

Attualmente sono supportati i tipi seguenti:

* `pandas`
* `numpy`
* `pyspark`
* oggetto di Python standard

Per usare la generazione dello schema, includere il `inference-schema` pacchetto nel file di ambiente conda. L'esempio seguente usa `[numpy-support]` poiché lo script di ingresso Usa un tipo di parametro numpy: 

#### <a name="example-dependencies-file"></a>Esempio di file delle dipendenze
Il codice YAML seguente è riportato un esempio di un file di dipendenze Conda per l'inferenza.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Se si desidera utilizzare la generazione automatica dello schema, lo script di ingresso **devono** importare la `inference-schema` pacchetti. 

Definire l'input e output formato di esempio tra il `input_sample` e `output_sample` variabili, che rappresentano i formati di richiesta e risposta per il servizio web. Utilizzare questi esempi di input e output elementi Decorator di funzione nel `run()` (funzione). Il scikit-informazioni di esempio seguente usa la generazione dello schema.

> [!TIP]
> Dopo aver distribuito il servizio, usare il `swagger_uri` proprietà per recuperare il documento di schema JSON.

#### <a name="example-entry-script"></a>Script di esempio di voce

Nell'esempio seguente viene illustrato come accettare e restituire i dati JSON:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType

def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
output_sample = np.array([3726.995])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Esempio di script con un input dictionary (utilizzo di supporto da Power BI)

Nell'esempio seguente viene illustrato come definire i dati di input come < chiave: valore > dizionario, usando i frame di dati. Questo metodo è supportato per l'utilizzo del servizio web distribuito da Power BI ([altre informazioni su come usare il servizio web da Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType

def init():
    global model
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```
Per altri script di esempio, vedere gli esempi seguenti:

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Assegnazione dei punteggi in dati binari: [Come usare un servizio web](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definire il InferenceConfig

La configurazione di inferenza dei tipi descrive come configurare il modello per generare stime. Nell'esempio seguente viene illustrato come creare una configurazione di inferenza:

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

In questo esempio, la configurazione contiene gli elementi seguenti:

* Una directory che contiene gli asset necessari per l'inferenza
* Che questo modello richiede Python
* Il [script di ingresso](#script), che consente di gestire le richieste web inviate al servizio distribuito
* Il file conda che descrive i pacchetti Python necessari per l'inferenza

Per informazioni sulla funzionalità InferenceConfig, vedere la [configurazione avanzata](#advanced-config) sezione.

### <a name="3-define-your-deployment-configuration"></a>3. Definire la configurazione della distribuzione

Prima di distribuire, è necessario definire la configurazione della distribuzione. La configurazione della distribuzione è specifica per la destinazione di calcolo che ospiterà il servizio web. Durante la distribuzione in locale, ad esempio, è necessario specificare la porta in cui il servizio accetta le richieste.

È necessario anche creare la risorsa di calcolo. Ad esempio, se esegue l'operazione non è già un servizio Kubernetes di Azure associato all'area di lavoro.

Nella tabella seguente fornisce un esempio di creazione di una configurazione di distribuzione per ogni destinazione di calcolo:

| Destinazione del calcolo | Esempio di configurazione di distribuzione |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Istanza di contenitore di Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Servizio Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Le sezioni seguenti illustrano come creare la configurazione della distribuzione e quindi usarlo per distribuire il servizio web.

## <a name="deploy-to-target"></a>Distribuzione di destinazione

### <a id="local"></a> Distribuzione locale

Per distribuire in locale, è necessario disporre **Docker installato** nel computer locale.

+ **Tramite il SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Usando l'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

### <a id="aci"></a> Istanze di contenitore di Azure (sviluppo/test)

Usare Istanze di Azure Container per distribuire i modelli come servizio Web se una o più delle condizioni seguenti sono vere:
- È necessario distribuire e convalidare rapidamente il modello.
- Si sta eseguendo il test di un modello in fase di sviluppo. 

Per informazioni su quote e aree di disponibilità per ACI, vedere la [quote e aree disponibili per istanze di contenitore di Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) articolo.

+ **Tramite il SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Usando l'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```


+ **Usando Visual Studio Code**

  Per [distribuire i modelli con Visual Studio Code](how-to-vscode-tools.md#deploy-and-manage-models) non è necessario creare un contenitore ACI per verificare in anticipo, poiché i contenitori ACI vengono creati in tempo reale.

Per altre informazioni, vedere la documentazione di riferimento per le classi [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a>Azure Kubernetes Service (sviluppo/test e produzione)

È possibile usare un cluster servizio Azure Kubernetes esistente o crearne uno nuovo tramite il SDK di Azure Machine Learning, l'interfaccia della riga di comando o il portale di Azure.

<a id="deploy-aks"></a>

Se si dispone già di un cluster del servizio contenitore di AZURE collegato, è possibile distribuire ad esso. Se ancora stato creato o collegato un cluster AKS, seguire la procedura per <a href="#create-attach-aks">creare un nuovo cluster AKS</a>.

+ **Tramite il SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Usando l'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

+ **Usando Visual Studio Code**

  È anche possibile [distribuire al servizio contenitore di AZURE tramite l'estensione di Visual Studio Code](how-to-vscode-tools.md#deploy-and-manage-models), ma è necessario configurare i cluster servizio contenitore di AZURE in anticipo.

Altre informazioni sulla distribuzione AKS e scalabilità automatica nel [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) riferimento.

#### Creare un nuovo cluster servizio contenitore di AZURE<a id="create-attach-aks"></a>
**Tempo stimato:** Circa 5 minuti.

Creare o collegare un cluster del servizio contenitore di AZURE è una volta in elaborazione per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, è necessario creare un nuovo cluster alla successiva che è necessario distribuire. È possibile avere più cluster servizio contenitore di AZURE collegato all'area di lavoro.

Se si desidera creare un cluster del servizio contenitore di AZURE per sviluppo, la convalida e test, si imposta `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` quando si usa [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py). Un cluster creato con questa impostazione avrà solo un nodo.

> [!IMPORTANT]
> Impostazione `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` crea un cluster AKS che non è adatto per la gestione del traffico di produzione. Volte in cui l'inferenza può contenere più di in un cluster creato per la produzione. Tolleranza di errore non è garantita per i cluster di sviluppo/test.
>
> È consigliabile che i cluster creati per sviluppo/test usano almeno due CPU virtuali.

Nell'esempio seguente viene illustrato come creare un nuovo cluster Azure Kubernetes Service:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Per altre informazioni sulla creazione di un cluster del servizio contenitore di AZURE di fuori di Azure Machine Learning SDK, vedere gli articoli seguenti:
* [Creare un cluster del servizio contenitore di AZURE](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Creare un cluster del servizio contenitore di AZURE (portale)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Per altre informazioni sul `cluster_purpose` parametro, vedere la [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) riferimento.

> [!IMPORTANT]
> Per [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se si selezionano valori personalizzati per agent_count e vm_size, è necessario verificare che il valore di agent_count moltiplicato per il valore di vm_size sia maggiore o uguale a 12 CPU virtuali. Ad esempio, se come vm_size si usa "Standard_D3_v2", che dispone di 4 CPU virtuali, per agent_count è consigliabile selezionare 3 o un valore superiore.

**Tempo stimato**: circa 20 minuti.

#### <a name="attach-an-existing-aks-cluster"></a>Collegare un cluster servizio contenitore di AZURE esistente

Se si dispone già del cluster servizio contenitore di AZURE nella sottoscrizione di Azure, ed è versione 1.12. # #, è possibile utilizzarlo per distribuire l'immagine.

> [!WARNING]
> Quando si collega un cluster del servizio contenitore di AZURE a un'area di lavoro, è possibile definire come verrà usata cluster mediante l'impostazione di `cluster_purpose` parametro.
>
> Se non si impostano i `cluster_purpose` parametro oppure impostare `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, quindi il cluster deve avere almeno 12 CPU virtuali disponibili.
>
> Se si imposta `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, quindi il cluster non è necessario avere 12 CPU virtuali. Tuttavia un cluster configurato per sviluppo/test non è adatto per il traffico a livello di produzione e possa aumentare i tempi di inferenza.

Il codice seguente illustra come collegare un 1.12 servizio contenitore di AZURE esistente. # # cluster all'area di lavoro:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Per ulteriori informazioni sul `attack_configuration()`, vedere la [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) riferimento.

Per altre informazioni sul `cluster_purpose` parametro, vedere la [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) riferimento.

## <a name="consume-web-services"></a>Utilizzare i servizi Web

Ogni servizio web distribuito fornisce un'API REST, pertanto è possibile creare applicazioni client in una vasta gamma di linguaggi di programmazione. Se è stata abilitata l'autenticazione per il servizio, è necessario fornire una chiave del servizio come un token nell'intestazione di richiesta.

### <a name="request-response-consumption"></a>Consumo di richiesta-risposta

Ecco un esempio di come richiamare il servizio in Python:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Per altre informazioni, vedere [Creare applicazioni client per utilizzare i servizi Web](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a> Inferenza del batch
Obiettivi di calcolo di Machine Learning di Azure vengono create e gestite dal servizio di Azure Machine Learning. Possono essere utilizzati per la stima batch dalla pipeline di Azure Machine Learning.

Per una procedura dettagliata di inferenza del batch con calcolo di Azure Machine Learning, vedere la [come eseguire stime in Batch](how-to-run-batch-predictions.md) articolo.

### <a id="iotedge"></a> Inferenza di IoT Edge
Supporto per la distribuzione e il bordo è disponibile in anteprima. Per altre informazioni, vedere la [distribuire Azure Machine Learning come modulo di IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) articolo.


## <a id="update"></a> L'aggiornamento dei servizi web

Quando si crea un nuovo modello, è necessario aggiornare manualmente ogni servizio che si desidera usare il nuovo modello. Per aggiornare il servizio Web, usare il metodo `update`. Il codice seguente viene illustrato come aggiornare il servizio web per usare un nuovo modello:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

<a id="advanced-config"></a>

## <a name="advanced-settings"></a>Impostazioni avanzate 

**<a id="customimage"></a> Usare un'immagine di base personalizzata**

Internamente, InferenceConfig crea un'immagine Docker contenente il modello e altre risorse necessarie per il servizio. Se non specificato, viene usata un'immagine di base predefinito.

Quando si crea un'immagine da utilizzare con la configurazione di inferenza dei tipi, l'immagine deve soddisfare i requisiti seguenti:

* Ubuntu 16.04 o versione successiva.
* 4.5 conda. & o versione successiva.
* Python 3.5. & o 3.6. #.

Per usare un'immagine personalizzata, impostare il `base_image` proprietà della configurazione di inferenza per l'indirizzo dell'immagine. L'esempio seguente illustra come usare un'immagine da entrambi una pubblica e privata registro contenitori di Azure:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

L'immagine seguente gli URI sono per le immagini fornite da Microsoft e può essere usato senza fornire un valore di password o nome utente:

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Per usare queste immagini, impostare il `base_image` all'URI nell'elenco precedente. Impostare `base_image_registry.address` su `mcr.microsoft.com`.

> [!IMPORTANT]
> Le immagini di Microsoft che usano CUDA o TensorRT devono essere utilizzate solo nei servizi di Microsoft Azure.

Per altre informazioni sul caricamento di immagini personalizzate in un registro contenitori di Azure, vedere [Push la prima immagine in un registro contenitori Docker privati](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Se il modello viene eseguito il training nel calcolo di Azure Machine Learning, usando __1.0.22 versione o versioni successive__ di Azure Machine Learning SDK, viene creata un'immagine durante il training. Nell'esempio seguente viene illustrato come utilizzare questa immagine:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="clean-up-resources"></a>Pulire le risorse
Per eliminare un servizio Web distribuito, usare `service.delete()`.
Per eliminare un modello registrato, usare `model.delete()`.

Per altre informazioni, vedere la documentazione di riferimento [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Passaggi successivi
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)

