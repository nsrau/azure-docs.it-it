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
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: 14ced5ed45bcc91e6b6c812f2d1cbb61e139cc4f
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278941"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuire modelli con il servizio di Azure Machine Learning

Informazioni su come distribuire un modello di machine learning come servizio Web nel cloud di Azure o per IoT Edge dispositivi.

Il flusso di lavoro è simile indipendentemente da [dove si distribuisce](#target) il modello:

1. Registrare il modello.
1. Preparare la distribuzione (specificare gli asset, l'utilizzo, la destinazione di calcolo).
1. Distribuire il modello nella destinazione di calcolo.
1. Testare il modello distribuito, denominato anche servizio Web.

Per altre informazioni sui concetti relativi al flusso di lavoro di distribuzione, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro del servizio Azure Machine Learning](how-to-manage-workspace.md).

- Un modello. Se non si dispone di un modello sottoposto a training, è possibile utilizzare il modello & i file di dipendenza forniti in [questa esercitazione](https://aka.ms/azml-deploy-cloud).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o l' [estensione di Visual Studio code Azure Machine Learning](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

Il codice seguente illustra come connettersi a un'area di lavoro del servizio Azure Machine Learning usando le informazioni memorizzate nella cache per l'ambiente di sviluppo locale:

**Uso dell'SDK**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Per altre informazioni sull'uso dell'SDK per connettersi a un'area di lavoro, vedere l' [sdk Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace).

**Uso dell'interfaccia della riga di comando**

Quando si usa l'interfaccia della riga `-w` di `--workspace-name` comando, usare il parametro o per specificare l'area di lavoro per il comando.

**Utilizzo di VS Code**

Quando si utilizza VS Code, l'area di lavoro viene selezionata utilizzando un'interfaccia grafica. Per ulteriori informazioni, vedere la pagina relativa alla [distribuzione e gestione dei modelli](how-to-vscode-tools.md#deploy-and-manage-models) nella documentazione sull'estensione vs code.

## <a id="registermodel"></a>Registrare il modello

Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

> [!TIP]
> Quando si registra un modello, è possibile specificare il percorso di una posizione cloud (da un'esecuzione di training) o una directory locale. Questo percorso consente di individuare solo i file da caricare come parte del processo di registrazione. non è necessario che corrisponda al percorso utilizzato nello script di immissione. Per ulteriori informazioni, vedere [la](#what-is-get_model_path)pagina relativa a get_model_path.

I modelli di apprendimento automatico vengono registrati nell'area di lavoro Azure Machine Learning. Il modello può provenire da Azure Machine Learning o può provenire da un'altra posizione. Negli esempi seguenti viene illustrato come registrare un modello:

### <a name="register-a-model-from-an-experiment-run"></a>Registrare un modello da un'esecuzione dell'esperimento

Nei frammenti di codice di questa sezione viene illustrata la registrazione di un modello da un'esecuzione di training:

> [!IMPORTANT]
> Questi frammenti di codice presuppongono che sia stata eseguita in precedenza un'esecuzione del training `run` e che sia possibile accedere all'oggetto (esempio di SDK) o al valore dell'ID esecuzione (esempio di CLI). Per altre informazioni sui modelli di training, vedere [creare e usare destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

+ **Uso dell'SDK**

  Quando si usa l'SDK per eseguire il training di un modello, è possibile ricevere un oggetto Run o [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) , a seconda di come è stato [eseguito](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) il training del modello. Ogni oggetto può essere usato per registrare un modello creato da un'esecuzione dell'esperimento.

  + Registrare un modello da un `azureml.core.Run` oggetto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` Fa riferimento alla posizione cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, impostare `model_path` sulla directory che contiene i file. Per ulteriori informazioni, vedere il riferimento [Run. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) .

  + Registrare un modello da un `azureml.train.automl.run.AutoMLRun` oggetto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    In questo esempio, i `metric` parametri `iteration` e non vengono specificati, che determina la registrazione dell'iterazione con la metrica primaria migliore. Il `model_id` valore restituito dall'esecuzione viene utilizzato al posto di un nome di modello.

    Per ulteriori informazioni, vedere la Guida di riferimento a [AutoMLRun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) .

+ **Uso dell'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` Fa riferimento alla posizione cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, impostare `--asset-path` sulla directory che contiene i file.

+ **Utilizzo di VS Code**

  Registrare i modelli usando i file o le cartelle del modello con l'estensione [vs code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file locale

È possibile registrare un modello fornendo un **percorso locale** al modello. È possibile specificare una cartella o un singolo file. È possibile utilizzare questo metodo per registrare sia i modelli sottoposti a training con Azure Machine Learning servizio che quindi scaricati oppure i modelli sottoposti a training al di fuori Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Esempio di ONNX con Python SDK:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Per includere più file nella registrazione del modello, impostare `model_path` sulla directory che contiene i file.

+ **Uso dell'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Per includere più file nella registrazione del modello, impostare `-p` sulla directory che contiene i file.

**Tempo stimato**: circa 10 secondi.

Per altre informazioni, vedere la documentazione di riferimento per la [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Per ulteriori informazioni sull'utilizzo dei modelli sottoposti a training al di fuori del servizio Azure Machine Learning, vedere [come distribuire un modello esistente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo

Per ospitare la distribuzione del servizio Web, è possibile usare le seguenti destinazioni di calcolo, o risorse di calcolo. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Preparare la distribuzione

La distribuzione del modello richiede diversi elementi:

* Uno __script di immissione__. Questo script accetta richieste, assegna punteggi alla richiesta utilizzando il modello e restituisce i risultati.

    > [!IMPORTANT]
    > Lo script di immissione è specifico del modello. deve comprendere il formato dei dati della richiesta in ingresso, il formato dei dati previsti dal modello e il formato dei dati restituiti ai client.
    >
    > Se i dati della richiesta sono in un formato non utilizzabile dal modello, lo script può trasformarlo in un formato accettabile. Può anche trasformare la risposta prima di restituirla al client.

    > [!IMPORTANT]
    > Il Azure Machine Learning SDK non fornisce un modo per il servizio Web o per le distribuzioni di IoT Edge per accedere all'archivio dati o ai set di dati. Se è necessario che il modello distribuito acceda ai dati archiviati all'esterno della distribuzione, ad esempio in un account di archiviazione di Azure, è necessario sviluppare una soluzione di codice personalizzata usando l'SDK pertinente. Ad esempio, [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python).
    >
    > Un'altra alternativa che può funzionare per lo scenario è la [stima in batch](how-to-run-batch-predictions.md), che fornisce l'accesso agli archivi dati quando si esegue il punteggio.

* **Dipendenze**, ad esempio gli script helper o i pacchetti Python/conda necessari per eseguire lo script di immissione o il modello

* __Configurazione della distribuzione__ per la destinazione di calcolo che ospita il modello distribuito. Questa configurazione descrive elementi quali i requisiti di memoria e CPU necessari per eseguire il modello.

Queste entità sono incapsulate in una __configurazione di inferenza__e una __configurazione di distribuzione__. La configurazione dell'inferenza fa riferimento allo script di immissione e ad altre dipendenze. Queste configurazioni sono definite a livello di codice quando si usa l'SDK e come file JSON quando si usa l'interfaccia della riga di comando per eseguire la distribuzione.

### <a id="script"></a> 1. Definire lo script di immissione & dipendenze

Lo script di immissione riceve i dati inviati a un servizio Web distribuito e li passa al modello. Riceve quindi la risposta restituita dal modello e la restituisce al client. **Lo script è specifico del modello**. deve comprendere i dati previsti e restituiti dal modello.

Lo script contiene due funzioni che caricano ed eseguono il modello:

* `init()`: questa funzione carica in genere il modello in un oggetto globale. Questa funzione viene eseguita una sola volta quando viene avviato il contenitore Docker per il servizio Web.

* `run(input_data)`: questa funzione usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON. È anche possibile usare dati binari non elaborati. È possibile trasformare i dati prima dell'invio al modello o prima della restituzione al client.

#### <a name="what-is-get_model_path"></a>Che cos'è get_model_path?

Quando si registra un modello, è necessario specificare un nome di modello utilizzato per la gestione del modello nel registro di sistema. Usare questo nome con il [modello. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) per recuperare il percorso dei file di modello nel file system locale. Se si registra una cartella o una raccolta di file, questa API restituisce il percorso della directory che contiene tali file.

Quando si registra un modello, è necessario assegnargli un nome, che corrisponde alla posizione in cui viene inserito il modello, localmente o durante la distribuzione del servizio.

> [!IMPORTANT]
> Se è stato eseguito il training di un modello usando Machine `model_id` Learning automatico, viene usato un valore come nome del modello. Per un esempio di registrazione e distribuzione di un modello di cui è stato eseguito il [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment)training con ml automatizzato, vedere.

Nell'esempio seguente viene restituito un percorso a un singolo file denominato `sklearn_mnist_model.pkl` , che è stato registrato con il `sklearn_mnist`nome:

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Opzionale Generazione automatica dello schema

Per generare automaticamente uno schema per il servizio Web, fornire un esempio di input e/o output nel costruttore per uno degli oggetti tipo definiti e il tipo e l'esempio vengono utilizzati per creare automaticamente lo schema. Azure Machine Learning servizio crea quindi una specifica [openapi](https://swagger.io/docs/specification/about/) (spavalderia) per il servizio Web durante la distribuzione.

Attualmente sono supportati i tipi seguenti:

* `pandas`
* `numpy`
* `pyspark`
* oggetto Python standard

Per usare la generazione dello schema, `inference-schema` includere il pacchetto nel file dell'ambiente conda.

##### <a name="example-dependencies-file"></a>File delle dipendenze di esempio

Il seguente YAML è un esempio di file di dipendenze conda per l'inferenza:

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

##### <a name="example-entry-script"></a>Script di immissione di esempio

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

Nell'esempio seguente viene illustrato come definire i dati di input come `<key: value>` dizionario, usando un frame di dati. Questo metodo è supportato per l'utilizzo del servizio Web distribuito da Power BI (ulteriori[informazioni su come utilizzare il servizio Web da Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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

<a id="binary"></a>

#### <a name="binary-data"></a>Dati binari

Se il modello accetta dati binari, ad esempio un'immagine, è necessario modificare il file `score.py` usato per la distribuzione in modo da accettare richieste HTTP non elaborate. Per accettare dati non elaborati, `AMLRequest` usare la classe nello script di immissione e `@rawhttp` aggiungere l'elemento Decorator alla `run()` funzione.

Di seguito è riportato un esempio `score.py` di un oggetto che accetta dati binari:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La `AMLRequest` classe si trova `azureml.contrib` nello spazio dei nomi. Gli elementi in questo spazio dei nomi cambiano spesso quando si lavora per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.
>
> Se è necessario eseguire il test nell'ambiente di sviluppo locale, è possibile installare i componenti usando il comando seguente:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Condivisione di risorse tra le origini (CORS)

La condivisione delle risorse tra le origini è un modo per consentire la richiesta di risorse in una pagina Web da un altro dominio. CORS funziona in base alle intestazioni HTTP inviate con la richiesta del client e restituite con la risposta del servizio. Per altre informazioni su CORS e sulle intestazioni valide, vedere [condivisione di risorse tra le origini](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in Wikipedia.

Per configurare la distribuzione del modello per il supporto di CORS `AMLResponse` , usare la classe nello script di immissione. Questa classe consente di impostare le intestazioni nell'oggetto risposta.

Nell'esempio seguente viene impostata `Access-Control-Allow-Origin` l'intestazione per la risposta dallo script di immissione:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La `AMLResponse` classe si trova `azureml.contrib` nello spazio dei nomi. Gli elementi in questo spazio dei nomi cambiano spesso quando si lavora per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.
>
> Se è necessario eseguire il test nell'ambiente di sviluppo locale, è possibile installare i componenti usando il comando seguente:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definire il InferenceConfig

Nella configurazione dell'inferenza viene descritto come configurare il modello per eseguire stime. Questa configurazione non fa parte dello script di immissione. fa riferimento allo script di immissione e viene usato per individuare tutte le risorse richieste dalla distribuzione. Viene usato in un secondo momento durante la distribuzione del modello.

La configurazione dell'inferenza può usare ambienti Azure Machine Learning per definire le dipendenze software necessarie per la distribuzione. Gli ambienti consentono di creare, gestire e riutilizzare le dipendenze software necessarie per il training e la distribuzione. Nell'esempio seguente viene illustrato il caricamento di un ambiente dall'area di lavoro e la relativa utilizzo con la configurazione dell'inferenza:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Per altre informazioni sugli ambienti, vedere [creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md).

È anche possibile specificare direttamente le dipendenze senza usare un ambiente. Nell'esempio seguente viene illustrato come creare una configurazione di inferenza che carica le dipendenze software da un file conda:

```python
from azureml.core.model import InferenceConfig

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

Prima di distribuire, è necessario definire la configurazione della distribuzione. __La configurazione della distribuzione è specifica per la destinazione di calcolo in cui verrà ospitato il servizio Web__. Ad esempio, quando si distribuisce localmente, è necessario specificare la porta in cui il servizio accetta le richieste. La configurazione della distribuzione non fa parte dello script di immissione. Viene usato per definire le caratteristiche della destinazione di calcolo in cui verrà ospitato lo script del modello e della voce.

Potrebbe anche essere necessario creare la risorsa di calcolo. Ad esempio, se non è già stato associato un servizio Azure Kubernetes all'area di lavoro.

La tabella seguente fornisce un esempio di creazione di una configurazione di distribuzione per ogni destinazione di calcolo:

| Destinazione del calcolo | Esempio di configurazione della distribuzione |
| ----- | ----- |
| Locale | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Istanza di contenitore di Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Servizio Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Ognuna di queste classi per i servizi Web locali, ACI e AKS può essere importata da `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profilatura

Prima di distribuire il modello come servizio, è consigliabile profilarlo per determinare i requisiti di CPU e memoria ottimali. È possibile profilare il modello usando SDK o l'interfaccia della riga di comando. Gli esempi seguenti illustrano come usare la profilatura dall'SDK:

> [!IMPORTANT]
> Quando si usa la profilatura, la configurazione di inferenza fornita non può fare riferimento a un ambiente Azure Machine Learning. Definire invece le dipendenze software utilizzando il `conda_file` parametro `InferenceConfig` dell'oggetto.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(true)
profiling_results = profile.get_results()
print(profiling_results)
```

Questo codice visualizza un risultato simile al testo seguente:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

I risultati della profilatura del modello vengono `Run` emessi come un oggetto.

Per informazioni sull'uso della profilatura dall'interfaccia della riga di comando, vedere [AZ ml Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Per ulteriori informazioni, vedere i documenti di riferimento seguenti:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profilo ()](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Schema del file di configurazione dell'inferenza](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Distribuisci nella destinazione

La distribuzione usa la configurazione di distribuzione per la configurazione dell'inferenza per distribuire i modelli. Il processo di distribuzione è simile indipendentemente dalla destinazione di calcolo. La distribuzione in AKS è leggermente diversa, perché è necessario fornire un riferimento al cluster AKS.

### <a id="local"></a>Distribuzione locale

Per eseguire la distribuzione localmente, è necessario che Docker sia installato nel computer locale.

#### <a name="using-the-sdk"></a>Uso dell'SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per ulteriori informazioni, vedere la documentazione di riferimento per [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)e [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per eseguire la distribuzione usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `mymodel:1` con il nome e la versione del modello registrato:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Per ulteriori informazioni, vedere il riferimento [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Servizio Web NotebookVM (DEVTEST)

Vedere [distribuire un modello nelle VM notebook](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Istanze di contenitore di Azure (DEVTEST)

Vedere [eseguire la distribuzione in istanze di contenitore di Azure](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST & PRODUCTion)

Vedere [eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Utilizzare i servizi Web

Ogni servizio Web distribuito fornisce un'API REST, quindi è possibile creare applicazioni client in un'ampia gamma di linguaggi di programmazione. Se è stata abilitata l'autenticazione della chiave per il servizio, è necessario specificare una chiave di servizio come token nell'intestazione della richiesta.
Se è stata abilitata l'autenticazione del token per il servizio, è necessario fornire un token di Azure Machine Learning JWT come bearer token nell'intestazione della richiesta.

> [!TIP]
> È possibile recuperare il documento JSON dello schema dopo la distribuzione del servizio. Utilizzare la [Proprietà swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) del servizio Web distribuito, ad esempio `service.swagger_uri`, per ottenere l'URI del file di spavalderia del servizio Web locale.

### <a name="request-response-consumption"></a>Consumo richiesta-risposta

Di seguito è riportato un esempio di come richiamare il servizio in Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

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

### <a name="web-service-schema-openapi-specification"></a>Schema del servizio Web (specifica OpenAPI)

Se è stata utilizzata la generazione automatica dello schema con la distribuzione, è possibile ottenere l'indirizzo della specifica OpenAPI per il servizio utilizzando la [Proprietà swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). Ad esempio `print(service.swagger_uri)`. Usare una richiesta GET (o aprire l'URI in un browser) per recuperare la specifica.

Il documento JSON seguente è un esempio di schema (OpenAPI Specification) generato per una distribuzione:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Per ulteriori informazioni sulla specifica, vedere la [specifica Open API](https://swagger.io/specification/).

Per un'utilità che consente di creare librerie client dalla specifica, vedere [spavalderia-codegen](https://github.com/swagger-api/swagger-codegen).

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

    [![visualizzazione-servizio-connessione](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png) 

1. Definire AzureMLWorkspace come __livello di ambito__ e inserire i parametri successivi.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Quindi, per distribuire continuamente il modello di Machine Learning usando il Azure Pipelines, in pipeline selezionare __rilascia__. Aggiungere un nuovo elemento, selezionare l'elemento del modello AzureML e la connessione del servizio creata nel passaggio precedente. Selezionare il modello e la versione per attivare una distribuzione. 

    [![Select-AzureMLmodel-artefatto](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Abilitare il trigger del modello nell'elemento del modello. Attivando il trigger ogni volta che la versione specificata (ad esempio la versione più recente di tale modello è registra nell'area di lavoro, viene attivata una pipeline di rilascio di Azure DevOps. 

    [![Enable-Model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Per altri progetti ed esempi di esempio, vedere i repository di esempio seguenti:

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="package-models"></a>Modelli di pacchetto

In alcuni casi, potrebbe essere necessario creare un'immagine Docker senza distribuire il modello. Ad esempio, quando si prevede di [eseguire la distribuzione nel servizio app Azure](how-to-deploy-app-service.md). In alternativa, è possibile scaricare l'immagine ed eseguirla in un'installazione locale di Docker. Potrebbe anche essere necessario scaricare i file usati per compilare l'immagine, esaminarli, modificarli e compilarli manualmente.

La creazione di pacchetti di modelli consente di eseguire entrambe le operazioni. Consente di creare un pacchetto di tutti gli asset necessari per ospitare un modello come servizio Web e di scaricare un'immagine Docker completamente compilata o i file necessari per crearne uno. Esistono due modi per usare la creazione di pacchetti di modelli:

* __Scaricare il modello in pacchetto__: Scaricare un'immagine Docker contenente il modello e altri file necessari per ospitarla come servizio Web.
* __Genera dockerfile__: È possibile scaricare lo script dockerfile, Model, entry e gli altri asset necessari per creare un'immagine docker. È quindi possibile ispezionare i file o apportare modifiche prima di compilare l'immagine localmente.

Entrambi i pacchetti possono essere usati per ottenere un'immagine Docker locale. 

> [!TIP]
> La creazione di un pacchetto è simile alla distribuzione di un modello, in quanto usa un modello registrato e una configurazione di inferenza.

> [!IMPORTANT]
> Funzionalità come il download di un'immagine completamente compilata o la compilazione di un'immagine localmente richiedono un'installazione di [Docker](https://www.docker.com) funzionante nell'ambiente di sviluppo.

### <a name="download-a-packaged-model"></a>Scaricare un modello di pacchetto

L'esempio seguente illustra come compilare un'immagine, che viene registrata nel Container Registry di Azure per l'area di lavoro:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Dopo aver creato un pacchetto, è possibile `package.pull()` usare per eseguire il pull dell'immagine nell'ambiente Docker locale. L'output di questo comando visualizzerà il nome dell'immagine. Ad esempio `Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. Al termine del download, `docker images` utilizzare il comando per elencare le immagini locali:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Per avviare un contenitore locale usando questa immagine, usare il comando seguente per avviare un contenitore denominato dalla shell o dalla riga di comando. Sostituire il `<imageid>` valore con l'ID immagine restituito `docker images` dal comando:

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Questo comando avvia la versione più recente dell'immagine denominata `myimage`. Esegue il mapping della porta locale di 6789 alla porta nel contenitore su cui il servizio Web è in ascolto (5001). Assegna anche il nome `mycontainer` al contenitore, che rende più semplice l'arresto. Una volta avviata, è possibile inviare `http://localhost:6789/score`richieste a.

### <a name="generate-dockerfile-and-dependencies"></a>Genera dockerfile e dipendenze

Nell'esempio seguente viene illustrato come scaricare il dockerfile, il modello e gli altri asset necessari per compilare l'immagine localmente. Il `generate_dockerfile=True` parametro indica che si desidera che i file non siano un'immagine completamente compilata:

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package
package.save("./imagefiles")
# Get the Azure Container Registry that the model/dockerfile uses
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Questo codice Scarica i file necessari per compilare l'immagine `imagefiles` nella directory. Il dockerfile incluso nei file di salvataggio fa riferimento a un'immagine di base archiviata in un Container Registry di Azure. Quando si compila l'immagine nell'installazione Docker locale, è necessario usare l'indirizzo, il nome utente e la password per l'autenticazione a questo registro. Usare la procedura seguente per compilare l'immagine usando un'installazione locale di Docker:

1. Da una shell o da una sessione della riga di comando, usare il comando seguente per autenticare Docker con il Container Registry di Azure. Sostituire `<address>`, `<username>` `package.get_container_registry()`e conivalorirecuperatiutilizzando`<password>` :

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Per compilare l'immagine, usare il comando seguente. Sostituire `<imagefiles>` con il percorso della directory in cui `package.save()` sono stati salvati i file:

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Questo comando imposta il nome dell'immagine `myimage`su.

Per verificare che l'immagine sia stata compilata, `docker images` usare il comando. L' `myimage` immagine dovrebbe essere visualizzata nell'elenco:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Per avviare un nuovo contenitore basato su questa immagine, usare il comando seguente:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Questo comando avvia la versione più recente dell'immagine denominata `myimage`. Esegue il mapping della porta locale di 6789 alla porta nel contenitore su cui il servizio Web è in ascolto (5001). Assegna anche il nome `mycontainer` al contenitore, che rende più semplice l'arresto. Una volta avviata, è possibile inviare `http://localhost:6789/score`richieste a.

### <a name="example-client-to-test-the-local-container"></a>Client di esempio per il test del contenitore locale

Il codice seguente è un esempio di un client Python che può essere usato con il contenitore:

```python
import requests
import json

# URL for the web service
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Per altri client di esempio in altri linguaggi di programmazione, vedere [utilizzo di modelli distribuiti come servizi Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Arrestare il contenitore Docker

Per arrestare il contenitore, usare il comando seguente da una shell o riga di comando diversa:

```bash
docker kill mycontainer
```

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

