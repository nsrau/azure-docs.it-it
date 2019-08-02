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
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: d26d1ca1ebceed481604d08d12cd9d5010495ab6
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618425"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuire modelli con il servizio di Azure Machine Learning

Informazioni su come distribuire un modello di machine learning come servizio Web nel cloud di Azure o per IoT Edge dispositivi. 

Il flusso di lavoro è simile indipendentemente da [dove si distribuisce](#target) il modello:

1. Registrare il modello.
1. Preparare la distribuzione (specificare gli asset, l'utilizzo, la destinazione di calcolo)
1. Distribuire il modello nella destinazione di calcolo.
1. Testare il modello distribuito, denominato anche servizio Web.

Per altre informazioni sui concetti relativi al flusso di lavoro di distribuzione, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prerequisiti

- Un modello. Se non si dispone di un modello sottoposto a training, è possibile utilizzare il modello & i file di dipendenza forniti in [questa esercitazione](https://aka.ms/azml-deploy-cloud).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)o l' [estensione di Visual Studio code Azure Machine Learning](how-to-vscode-tools.md).

## <a id="registermodel"></a>Registrare il modello

Un contenitore logico modello registrato per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

I modelli di apprendimento automatico vengono registrati nell'area di lavoro Azure Machine Learning. Il modello può provenire da Azure Machine Learning o può provenire da un'altra posizione. Gli esempi seguenti illustrano come registrare un modello da un file:

### <a name="register-a-model-from-an-experiment-run"></a>Registrare un modello da un'esecuzione dell'esperimento

+ **Esempio di Scikit-learn con SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Per includere più file nella registrazione del modello, impostare `model_path` sulla directory che contiene i file.

+ **Uso dell'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  > [!TIP]
  > Per includere più file nella registrazione del modello, impostare `--asset-path` sulla directory che contiene i file.

+ **Utilizzo di VS Code**

  Registrare i modelli usando i file o le cartelle del modello con l'estensione [vs code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-an-externally-created-model"></a>Registrare un modello creato esternamente

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

È possibile registrare un modello creato esternamente fornendo un **percorso locale** al modello. È possibile specificare una cartella o un singolo file.

+ **Esempio di ONNX con Python SDK:**
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

  > [!TIP]
  > Per includere più file nella registrazione del modello, impostare `model_path` sulla directory che contiene i file.

+ **Uso dell'interfaccia della riga di comando**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Per includere più file nella registrazione del modello, impostare `-p` sulla directory che contiene i file.

**Tempo stimato**: circa 10 secondi.

Per altre informazioni, vedere la documentazione di riferimento per la [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Per ulteriori informazioni sull'utilizzo dei modelli sottoposti a training al di fuori del servizio Azure Machine Learning, vedere [come distribuire un modello esistente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo

Per ospitare la distribuzione del servizio Web, è possibile usare le seguenti destinazioni di calcolo, o risorse di calcolo. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Preparare la distribuzione

Per distribuire come servizio Web, è necessario creare una configurazione di inferenza`InferenceConfig`() e una configurazione di distribuzione. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione. Nella configurazione dell'inferenza è possibile specificare gli script e le dipendenze necessari per gestire il modello. Nella configurazione della distribuzione specificare i dettagli relativi al modo in cui gestire il modello nella destinazione di calcolo.

> [!IMPORTANT]
> Il Azure Machine Learning SDK non fornisce un modo per il servizio Web o per le distribuzioni di IoT Edge per accedere all'archivio dati o ai set di dati. Se è necessario che il modello distribuito acceda ai dati archiviati all'esterno della distribuzione, ad esempio in un account di archiviazione di Azure, è necessario sviluppare una soluzione di codice personalizzata usando l'SDK pertinente. Ad esempio, [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python).
>
> Un'altra alternativa che può funzionare per lo scenario è la [stima in batch](how-to-run-batch-predictions.md), che fornisce l'accesso agli archivi dati quando si esegue il punteggio.

### <a id="script"></a> 1. Definire lo script di immissione & dipendenze

Lo script di immissione riceve i dati inviati a un servizio Web distribuito e li passa al modello. Riceve quindi la risposta restituita dal modello e la restituisce al client. **Lo script è specifico del modello**. deve comprendere i dati previsti e restituiti dal modello.

Lo script contiene due funzioni che caricano ed eseguono il modello:

* `init()`: questa funzione carica in genere il modello in un oggetto globale. Questa funzione viene eseguita una sola volta quando viene avviato il contenitore Docker per il servizio Web.

* `run(input_data)`: questa funzione usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON. È anche possibile usare dati binari non elaborati. È possibile trasformare i dati prima dell'invio al modello o prima della restituzione al client.

#### <a name="what-is-getmodelpath"></a>Che cos'è get_model_path?

Quando si registra un modello, è necessario specificare un nome di modello utilizzato per la gestione del modello nel registro di sistema. Usare questo nome con il [modello. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) per recuperare il percorso dei file di modello nel file system locale. Se si registra una cartella o una raccolta di file, questa API restituisce il percorso della directory che contiene tali file.

Quando si registra un modello, è necessario assegnargli un nome, che corrisponde alla posizione in cui viene inserito il modello, localmente o durante la distribuzione del servizio.

Nell'esempio seguente viene restituito un percorso a un singolo file denominato `sklearn_mnist_model.pkl` , che è stato registrato con il `sklearn_mnist`nome:

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-swagger-schema-generation"></a>Opzionale Generazione automatica dello schema spavalderia

Per generare automaticamente uno schema per il servizio Web, fornire un esempio di input e/o output nel costruttore per uno degli oggetti tipo definiti e il tipo e l'esempio vengono utilizzati per creare automaticamente lo schema. Azure Machine Learning servizio crea quindi una specifica [openapi](https://swagger.io/docs/specification/about/) (spavalderia) per il servizio Web durante la distribuzione.

Attualmente sono supportati i tipi seguenti:

* `pandas`
* `numpy`
* `pyspark`
* oggetto Python standard

Per usare la generazione dello schema, `inference-schema` includere il pacchetto nel file dell'ambiente conda. Nell'esempio seguente viene `[numpy-support]` usato poiché lo script entry usa un tipo di parametro numpy: 

#### <a name="example-dependencies-file"></a>File delle dipendenze di esempio
Il YAML seguente è un esempio di file di dipendenze conda per l'inferenza.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Se si desidera utilizzare la generazione automatica dello schema, è **necessario** che lo script `inference-schema` di immissione importi i pacchetti. 

Definire i formati di esempio di input e output `input_sample` nelle `output_sample` variabili e, che rappresentano i formati di richiesta e risposta per il servizio Web. Usare questi esempi negli elementi Decorator `run()` della funzione di input e output della funzione. L'esempio Scikit-learn seguente usa la generazione dello schema.

> [!TIP]
> Dopo aver distribuito il servizio, utilizzare `swagger_uri` la proprietà per recuperare il documento JSON dello schema.

#### <a name="example-entry-script"></a>Script di immissione di esempio

L'esempio seguente illustra come accettare e restituire i dati JSON:

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


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Script di esempio con input del dizionario (utilizzo del supporto da Power BI)

Nell'esempio seguente viene illustrato come definire i dati di input come < Key: value > Dictionary, usando dataframe. Questo metodo è supportato per l'utilizzo del servizio Web distribuito da Power BI (ulteriori[informazioni su come utilizzare il servizio Web da Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


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

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Assegnazione di punteggi ai dati binari: [Come utilizzare un servizio Web](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definire il InferenceConfig

Nella configurazione dell'inferenza viene descritto come configurare il modello per eseguire stime. Nell'esempio seguente viene illustrato come creare una configurazione di inferenza. Questa configurazione specifica il runtime, lo script di immissione e (facoltativamente) il file dell'ambiente conda:

```python
inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Per ulteriori informazioni, vedere il riferimento alla classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Per informazioni sull'uso di un'immagine Docker personalizzata con la configurazione dell'inferenza, vedere [come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Esempio di interfaccia della riga di comando di InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Il comando seguente illustra come distribuire un modello usando l'interfaccia della riga di comando:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In questo esempio, la configurazione contiene gli elementi seguenti:

* Questo modello richiede Python
* [Script di immissione](#script), usato per gestire le richieste Web inviate al servizio distribuito
* File conda che descrive i pacchetti Python necessari per l'inferenza

Per informazioni sull'uso di un'immagine Docker personalizzata con la configurazione dell'inferenza, vedere [come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definire la configurazione di distribuzione

Prima di distribuire, è necessario definire la configurazione della distribuzione. __La configurazione della distribuzione è specifica per la destinazione di calcolo in cui verrà ospitato il servizio Web__. Ad esempio, quando si distribuisce localmente, è necessario specificare la porta in cui il servizio accetta le richieste.

Potrebbe anche essere necessario creare la risorsa di calcolo. Ad esempio, se non è già stato associato un servizio Azure Kubernetes all'area di lavoro.

La tabella seguente fornisce un esempio di creazione di una configurazione di distribuzione per ogni destinazione di calcolo:

| Destinazione del calcolo | Esempio di configurazione della distribuzione |
| ----- | ----- |
| Locale | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Istanza di contenitore di Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Servizio Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

> [!TIP]
> Prima di distribuire il modello come servizio, è consigliabile profilarlo per determinare i requisiti di CPU e memoria ottimali. È possibile profilare il modello usando SDK o l'interfaccia della riga di comando. Per ulteriori informazioni, vedere [profile ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) e [AZ ml Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) Reference.
>
> I risultati della profilatura del modello vengono `Run` emessi come un oggetto. Per ulteriori informazioni, vedere il riferimento alla classe [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) .

## <a name="deploy-to-target"></a>Distribuisci nella destinazione

La distribuzione usa la configurazione di distribuzione per la configurazione dell'inferenza per distribuire il modello o i modelli. Il processo di distribuzione è simile indipendentemente dalla destinazione di calcolo. La distribuzione in AKS è leggermente diversa, perché è necessario fornire un riferimento al cluster AKS.

### <a id="local"></a>Distribuzione locale

Per eseguire la distribuzione localmente, è necessario che Docker sia **installato** nel computer locale.

#### <a name="using-the-sdk"></a>Uso dell'SDK

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per ulteriori informazioni, vedere la documentazione di riferimento per [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)e [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per eseguire la distribuzione usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `mymodel:1` con il nome e la versione del modello registrato:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Per ulteriori informazioni, vedere il riferimento [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="aci"></a>Istanze di contenitore di Azure (DEVTEST)

Vedere [eseguire la distribuzione in istanze di contenitore di Azure](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST & PRODUCTion)

Vedere [eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Utilizzare i servizi Web

Ogni servizio Web distribuito fornisce un'API REST, quindi è possibile creare applicazioni client in un'ampia gamma di linguaggi di programmazione. Se è stata abilitata l'autenticazione per il servizio, è necessario specificare una chiave di servizio come token nell'intestazione della richiesta.

### <a name="request-response-consumption"></a>Consumo richiesta-risposta

Di seguito è riportato un esempio di come richiamare il servizio in Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Per altre informazioni, vedere [Creare applicazioni client per utilizzare i servizi Web](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a>Inferenza batch
Azure Machine Learning le destinazioni di calcolo vengono create e gestite dal servizio Azure Machine Learning. Possono essere usati per la stima in batch da Azure Machine Learning pipeline.

Per una procedura dettagliata di inferenza batch con Azure Machine Learning calcolo, vedere l'articolo [come eseguire stime batch](how-to-run-batch-predictions.md) .

### <a id="iotedge"></a>Inferenza IoT Edge
Il supporto per la distribuzione in Edge è in anteprima. Per ulteriori informazioni, vedere l'articolo [distribuire Azure machine learning come modulo di IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) .


## <a id="update"></a>Aggiornare i servizi Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>Distribuzione continua del modello 

È possibile distribuire i modelli in modo continuo usando l'estensione Machine Learning per [Azure DevOps](https://azure.microsoft.com/services/devops/). Usando l'estensione Machine Learning per Azure DevOps, è possibile attivare una pipeline di distribuzione quando un nuovo modello di apprendimento automatico viene registrato nell'area di lavoro Azure Machine Learning servizio. 

1. Iscriversi a [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), che rende possibile l'integrazione e il recapito continui dell'applicazione in qualsiasi piattaforma o qualsiasi cloud. Azure Pipelines [differisce dalle pipeline di ml](concept-ml-pipelines.md#compare). 

1. [Creare un progetto DevOps di Azure.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installare l' [estensione Machine Learning per Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Usare le __connessioni al servizio__ per configurare una connessione dell'entità servizio all'area di lavoro del servizio Azure Machine Learning per accedere a tutti gli elementi. Passare a Impostazioni progetto, fare clic su connessioni al servizio e selezionare Azure Resource Manager.

    ![visualizzazione-servizio-connessione](media/how-to-deploy-and-where/view-service-connection.png) 

1. Definire AzureMLWorkspace come __livello di ambito__ e inserire i parametri successivi.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Quindi, per distribuire continuamente il modello di Machine Learning usando il Azure Pipelines, in pipeline selezionare __rilascia__. Aggiungere un nuovo elemento, selezionare l'elemento del modello AzureML e la connessione del servizio creata nel passaggio precedente. Selezionare il modello e la versione per attivare una distribuzione. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Abilitare il trigger del modello nell'elemento del modello. Attivando il trigger ogni volta che la versione specificata (ad esempio la versione più recente di tale modello è registra nell'area di lavoro, viene attivata una pipeline di rilascio di Azure DevOps. 

    ![Enable-Model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

Per esempi e progetti di esempio, vedere [il repository MLOps](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>Pulire le risorse
Per eliminare un servizio Web distribuito, usare `service.delete()`.
Per eliminare un modello registrato, usare `model.delete()`.

Per ulteriori informazioni, vedere la documentazione di riferimento per [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)e [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Passaggi successivi
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)

