---
title: Come e dove distribuire i modelli
titleSuffix: Azure Machine Learning
description: Informazioni su come e dove distribuire i modelli di Azure Machine Learning, tra cui istanze di contenitore di Azure, servizio Azure Kubernetes, Azure IoT Edge e matrici di Gate programmabili da campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: bb86d551d83668a3558cf63827a64a481cf87e02
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926989"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Distribuire modelli con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come distribuire un modello di machine learning come servizio Web nel cloud di Azure o per Azure IoT Edge dispositivi.

Il flusso di lavoro è simile indipendentemente [da dove si distribuisce](#target) il modello:

1. Registrare il modello.
1. Preparare la distribuzione. (Specificare asset, utilizzo, destinazione di calcolo.)
1. Distribuire il modello nella destinazione di calcolo.
1. Testare il modello distribuito, detto anche servizio Web.

Per ulteriori informazioni sui concetti relativi al flusso di lavoro di distribuzione, vedere [gestire, distribuire e monitorare i modelli con Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Un modello. Se non si dispone di un modello sottoposto a training, è possibile utilizzare i file di modello e di dipendenza forniti in [questa esercitazione](https://aka.ms/azml-deploy-cloud).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), l' [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o l' [estensione di Visual Studio code Azure Machine Learning](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

Il codice seguente illustra come connettersi a un'area di lavoro di Azure Machine Learning usando le informazioni memorizzate nella cache per l'ambiente di sviluppo locale:

+ **Uso dell'SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Per altre informazioni sull'uso dell'SDK per connettersi a un'area di lavoro, vedere la documentazione di [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Uso dell'interfaccia della riga di comando**

   Quando si usa l'interfaccia della riga di comando, usare il parametro `-w` o `--workspace-name` per specificare l'area di lavoro per il comando.

+ **Utilizzo di VS Code**

   Quando si utilizza VS Code, è possibile selezionare l'area di lavoro utilizzando un'interfaccia grafica. Per ulteriori informazioni, vedere la pagina relativa alla [distribuzione e gestione dei modelli](how-to-vscode-tools.md#deploy-and-manage-models) nella documentazione sull'estensione vs code.

## <a id="registermodel"></a>Registrare il modello

Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro. Dopo aver registrato i file, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

> [!TIP]
> Quando si registra un modello, si specifica il percorso di una posizione cloud (da un'esecuzione di training) o di una directory locale. Questo percorso consente di individuare solo i file da caricare come parte del processo di registrazione. Non è necessario che corrisponda al percorso utilizzato nello script di immissione. Per altre informazioni, vedere [individuare i file di modello nello script di immissione](#locate-model-files-in-your-entry-script).

I modelli di apprendimento automatico vengono registrati nell'area di lavoro Azure Machine Learning. Il modello può provenire da Azure Machine Learning o da un'altra posizione. Negli esempi seguenti viene illustrato come registrare un modello.

### <a name="register-a-model-from-an-experiment-run"></a>Registrare un modello da un'esecuzione dell'esperimento

I frammenti di codice in questa sezione illustrano come registrare un modello da un'esecuzione di training:

> [!IMPORTANT]
> Per usare questi frammenti, è necessario aver eseguito in precedenza un'esecuzione del training ed è necessario avere accesso all'oggetto `Run` (esempio SDK) o al valore dell'ID esecuzione (esempio dell'interfaccia della riga di comando). Per altre informazioni sui modelli di training, vedere [configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md).

+ **Uso dell'SDK**

  Quando si usa l'SDK per eseguire il training di un modello, è possibile ricevere un oggetto Run o un oggetto [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) , a seconda di come è stato [eseguito](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) il training del modello. Ogni oggetto può essere usato per registrare un modello creato da un'esecuzione dell'esperimento.

  + Registrare un modello da un oggetto `azureml.core.Run`:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Il parametro `model_path` si riferisce al percorso cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, impostare `model_path` sul percorso di una cartella che contiene i file. Per ulteriori informazioni, vedere la documentazione di [Run. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) .

  + Registrare un modello da un oggetto `azureml.train.automl.run.AutoMLRun`:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    In questo esempio, i parametri `metric` e `iteration` non sono specificati, quindi l'iterazione con la metrica primaria migliore verrà registrata. Il valore `model_id` restituito dall'esecuzione viene utilizzato al posto di un nome di modello.

    Per ulteriori informazioni, vedere la documentazione di [AutoMLRun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) .

+ **Uso dell'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  Il parametro `--asset-path` si riferisce al percorso cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, impostare `--asset-path` sul percorso di una cartella che contiene i file.

+ **Utilizzo di VS Code**

  Registrare i modelli usando i file o le cartelle del modello usando l'estensione [vs code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file locale

Per registrare un modello, è possibile fornire il percorso locale del modello. È possibile specificare il percorso di una cartella o di un singolo file. È possibile utilizzare questo metodo per registrare i modelli sottoposti a training con Azure Machine Learning e quindi scaricarli. È anche possibile usare questo metodo per registrare i modelli sottoposti a training al di fuori della Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Uso di SDK e ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
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

  Per includere più file nella registrazione del modello, impostare `model_path` sul percorso di una cartella che contiene i file.

+ **Uso dell'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Per includere più file nella registrazione del modello, impostare `-p` sul percorso di una cartella che contiene i file.

**Tempo stimato**: circa 10 secondi.

Per ulteriori informazioni, vedere la documentazione relativa alla [classe del modello](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Per ulteriori informazioni sull'utilizzo di modelli sottoposti a training all'esterno di Azure Machine Learning, vedere [come distribuire un modello esistente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo

Per ospitare la distribuzione del servizio Web, è possibile usare le seguenti destinazioni di calcolo o risorse di calcolo:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Preparare la distribuzione

Per distribuire il modello, sono necessari gli elementi seguenti:

* **Uno script di immissione**. Questo script accetta le richieste, assegna un punteggio alle richieste usando il modello e restituisce i risultati.

    > [!IMPORTANT]
    > * Lo script di immissione è specifico del modello. Deve comprendere il formato dei dati della richiesta in ingresso, il formato dei dati previsti dal modello e il formato dei dati restituiti ai client.
    >
    >   Se i dati della richiesta sono in un formato non utilizzabile dal modello, lo script può trasformarlo in un formato accettabile. Può anche trasformare la risposta prima di restituirla al client.
    >
    > * Il Azure Machine Learning SDK non fornisce un modo per i servizi Web o le distribuzioni IoT Edge per accedere all'archivio dati o ai set di dati. Se il modello distribuito deve accedere ai dati archiviati all'esterno della distribuzione, ad esempio i dati in un account di archiviazione di Azure, è necessario sviluppare una soluzione di codice personalizzata usando l'SDK pertinente. Ad esempio, [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python).
    >
    >   Un'alternativa che può funzionare per lo scenario è la [stima in batch](how-to-run-batch-predictions.md), che fornisce l'accesso agli archivi dati durante il punteggio.

* **Dipendenze**, ad esempio gli script helper o i pacchetti Python/conda necessari per eseguire lo script di immissione o il modello.

* **Configurazione della distribuzione** per la destinazione di calcolo che ospita il modello distribuito. Questa configurazione descrive elementi quali i requisiti di memoria e CPU necessari per eseguire il modello.

Questi elementi vengono incapsulati in una *configurazione di inferenza* e una *configurazione di distribuzione*. La configurazione dell'inferenza fa riferimento allo script di immissione e ad altre dipendenze. Queste configurazioni vengono definite a livello di codice quando si usa l'SDK per eseguire la distribuzione. Vengono definiti nei file JSON quando si usa l'interfaccia della riga di comando.

### <a id="script"></a>1. definire lo script di immissione e le dipendenze

Lo script di immissione riceve i dati inviati a un servizio Web distribuito e li passa al modello. Riceve quindi la risposta restituita dal modello e la restituisce al client. *Lo script è specifico del modello*. Deve comprendere i dati previsti e restituiti dal modello.

Lo script contiene due funzioni che caricano ed eseguono il modello:

* `init()`: in genere, questa funzione carica il modello in un oggetto globale. Questa funzione viene eseguita una sola volta, quando viene avviato il contenitore Docker per il servizio Web.

* `run(input_data)`: questa funzione utilizza il modello per stimare un valore in base ai dati di input. Gli input e gli output dell'esecuzione usano in genere JSON per la serializzazione e la deserializzazione. È anche possibile usare dati binari non elaborati. È possibile trasformare i dati prima di inviarli al modello o prima di restituirli al client.

#### <a name="locate-model-files-in-your-entry-script"></a>Individuare i file del modello nello script di immissione

Esistono due modi per individuare i modelli nello script di immissione:
* `AZUREML_MODEL_DIR`: variabile di ambiente contenente il percorso della posizione del modello.
* `Model.get_model_path`: un'API che restituisce il percorso del file di modello utilizzando il nome del modello registrato.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR è una variabile di ambiente creata durante la distribuzione del servizio. È possibile usare questa variabile di ambiente per trovare il percorso dei modelli distribuiti.

Nella tabella seguente viene descritto il valore di AZUREML_MODEL_DIR in base al numero di modelli distribuiti:

| Distribuzione | Valore della variabile di ambiente |
| ----- | ----- |
| Modello singolo | Percorso della cartella che contiene il modello. |
| Più modelli | Percorso della cartella contenente tutti i modelli. I modelli si trovano in base al nome e alla versione in questa cartella (`$MODEL_NAME/$VERSION`) |

Per ottenere il percorso di un file in un modello, combinare la variabile di ambiente con il nome file che si sta cercando.
I nomi file dei file di modello vengono conservati durante la registrazione e la distribuzione. 

**Esempio di modello singolo**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**Esempio di più modelli**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Quando si registra un modello, è necessario specificare un nome di modello utilizzato per la gestione del modello nel registro di sistema. Usare questo nome con il metodo [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) per recuperare il percorso del file o dei file del modello nel file system locale. Se si registra una cartella o una raccolta di file, questa API restituisce il percorso della directory che contiene tali file.

Quando si registra un modello, è necessario assegnargli un nome. Il nome corrisponde alla posizione in cui viene inserito il modello, localmente o durante la distribuzione del servizio.

> [!IMPORTANT]
> Se è stato usato Machine Learning automatico per eseguire il training di un modello, viene usato un valore `model_id` come nome del modello. Per un esempio di registrazione e distribuzione di un modello con training automatico, vedere [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features) su GitHub.

Nell'esempio seguente viene restituito un percorso a un singolo file denominato `sklearn_mnist_model.pkl` (registrato con il nome `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Opzionale Generazione automatica dello schema

Per generare automaticamente uno schema per il servizio Web, fornire un esempio di input e/o output nel costruttore per uno degli oggetti tipo definiti. Il tipo e l'esempio vengono utilizzati per creare automaticamente lo schema. Azure Machine Learning quindi crea una specifica [openapi](https://swagger.io/docs/specification/about/) (spavalderia) per il servizio Web durante la distribuzione.

Questi tipi sono attualmente supportati:

* `pandas`
* `numpy`
* `pyspark`
* Oggetto Python standard

Per usare la generazione dello schema, includere il pacchetto di `inference-schema` nel file dell'ambiente conda. Per ulteriori informazioni su questo pacchetto, vedere [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema).

##### <a name="example-dependencies-file"></a>File delle dipendenze di esempio

Il seguente YAML è un esempio di file di dipendenze conda per l'inferenza:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
    - azureml-defaults
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Se la dipendenza è disponibile tramite conda e PIP (da PyPi), Microsoft consiglia di usare la versione conda, in quanto i pacchetti conda sono in genere dotati di binari predefiniti che rendono l'installazione più affidabile.
>
> Per ulteriori informazioni, vedere informazioni su [conda e PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Per verificare se la dipendenza è disponibile tramite conda, utilizzare il comando `conda search <package-name>` oppure utilizzare gli indici dei pacchetti in [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) e [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Se si desidera utilizzare la generazione automatica dello schema, è necessario che lo script di immissione importi i pacchetti `inference-schema`.

Definire i formati di esempio di input e output nelle variabili `input_sample` e `output_sample`, che rappresentano i formati di richiesta e risposta per il servizio Web. Usare questi esempi negli elementi Decorator della funzione di input e output nella funzione `run()`. Nell'esempio Scikit-learn seguente viene utilizzata la generazione dello schema.

##### <a name="example-entry-script"></a>Script di immissione di esempio

L'esempio seguente illustra come accettare e restituire i dati JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Nell'esempio seguente viene illustrato come definire i dati di input come dizionario `<key: value>` usando un frame di dati. Questo metodo è supportato per l'utilizzo del servizio Web distribuito da Power BI. [Per ulteriori informazioni su come utilizzare il servizio Web da Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration), vedere.

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
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Per altri esempi, vedere gli script seguenti:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Dati binari

Se il modello accetta dati binari, ad esempio un'immagine, è necessario modificare il file di `score.py` usato per la distribuzione per accettare richieste HTTP non elaborate. Per accettare dati non elaborati, usare la classe `AMLRequest` nello script di immissione e aggiungere l'elemento Decorator `@rawhttp` alla funzione `run()`.

Di seguito è riportato un esempio di `score.py` che accetta dati binari:

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La classe `AMLRequest` si trova nello spazio dei nomi `azureml.contrib`. Le entità in questo spazio dei nomi cambiano spesso quando si lavora per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere considerato un'anteprima che non è completamente supportata da Microsoft.
>
> Se è necessario testarlo nell'ambiente di sviluppo locale, è possibile installare i componenti usando il comando seguente:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Condivisione di risorse tra le origini (CORS)

La condivisione delle risorse tra le origini è un modo per consentire la richiesta di risorse in una pagina Web da un altro dominio. CORS funziona tramite le intestazioni HTTP inviate con la richiesta del client e restituite con la risposta del servizio. Per altre informazioni su CORS e sulle intestazioni valide, vedere [condivisione di risorse tra le origini](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in Wikipedia.

Per configurare la distribuzione del modello per il supporto di CORS, usare la classe `AMLResponse` nello script di immissione. Questa classe consente di impostare le intestazioni nell'oggetto risposta.

Nell'esempio seguente viene impostata l'intestazione `Access-Control-Allow-Origin` per la risposta dallo script di immissione:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La classe `AMLResponse` si trova nello spazio dei nomi `azureml.contrib`. Le entità in questo spazio dei nomi cambiano spesso quando si lavora per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere considerato un'anteprima che non è completamente supportata da Microsoft.
>
> Se è necessario testarlo nell'ambiente di sviluppo locale, è possibile installare i componenti usando il comando seguente:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. definire il InferenceConfig

Nella configurazione dell'inferenza viene descritto come configurare il modello per eseguire stime. Questa configurazione non fa parte dello script di immissione. Fa riferimento allo script di immissione e viene usato per individuare tutte le risorse richieste dalla distribuzione. Viene usato in un secondo momento, quando si distribuisce il modello.

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

Per ulteriori informazioni, vedere la documentazione relativa alla classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Per informazioni sull'uso di un'immagine Docker personalizzata con una configurazione di inferenza, vedere [come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Esempio di interfaccia della riga di comando di InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Il comando seguente illustra come distribuire un modello usando l'interfaccia della riga di comando:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In questo esempio, la configurazione specifica le impostazioni seguenti:

* Che il modello richieda Python.
* [Script di immissione](#script)utilizzato per gestire le richieste Web inviate al servizio distribuito.
* Il file conda che descrive i pacchetti Python necessari per l'inferenza.

Per informazioni sull'uso di un'immagine Docker personalizzata con una configurazione di inferenza, vedere [come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. definire la configurazione della distribuzione

Prima di distribuire il modello, è necessario definire la configurazione della distribuzione. *La configurazione della distribuzione è specifica per la destinazione di calcolo in cui verrà ospitato il servizio Web.* Ad esempio, quando si distribuisce un modello localmente, è necessario specificare la porta in cui il servizio accetta le richieste. La configurazione della distribuzione non fa parte dello script di immissione. Viene usato per definire le caratteristiche della destinazione di calcolo che ospiterà lo script del modello e della voce.

Potrebbe anche essere necessario creare la risorsa di calcolo, se, ad esempio, non si dispone già di un'istanza di Azure Kubernetes Service (AKS) associata all'area di lavoro.

La tabella seguente fornisce un esempio di creazione di una configurazione di distribuzione per ogni destinazione di calcolo:

| Destinazione del calcolo | Esempio di configurazione della distribuzione |
| ----- | ----- |
| Locale | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Istanze di Azure Container | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Servizio Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Le classi per local, istanze di contenitore di Azure e i servizi Web AKS possono essere importate da `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profilatura

Prima di distribuire il modello come servizio, è consigliabile profilarlo per determinare i requisiti di CPU e memoria ottimali. Per profilare il modello, è possibile usare l'SDK o l'interfaccia della riga di comando. Negli esempi seguenti viene illustrato come profilare un modello utilizzando SDK.

> [!IMPORTANT]
> Quando si usa la profilatura, la configurazione di inferenza fornita non può fare riferimento a un ambiente Azure Machine Learning. Definire invece le dipendenze del software utilizzando il parametro `conda_file` dell'oggetto `InferenceConfig`.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

Questo codice visualizza un risultato simile all'output seguente:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

I risultati della profilatura del modello vengono emessi come oggetto `Run`.

Per informazioni sull'uso della profilatura dall'interfaccia della riga di comando, vedere [AZ ml Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Per ulteriori informazioni, vedere i documenti seguenti:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profilo ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Schema del file di configurazione dell'inferenza](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Distribuisci nella destinazione

La distribuzione usa la configurazione di distribuzione per la configurazione dell'inferenza per distribuire i modelli. Il processo di distribuzione è simile indipendentemente dalla destinazione di calcolo. La distribuzione in AKS è leggermente diversa perché è necessario fornire un riferimento al cluster AKS.

### <a name="securing-deployments-with-ssl"></a>Protezione delle distribuzioni con SSL

Per altre informazioni su come proteggere la distribuzione di un servizio Web, vedere [usare SSL per proteggere un servizio Web](how-to-secure-web-service.md#enable).

### <a id="local"></a>Distribuzione locale

Per distribuire un modello localmente, è necessario che Docker sia installato nel computer locale.

#### <a name="using-the-sdk"></a>Uso dell'SDK
ZZS
```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per ulteriori informazioni, vedere la documentazione relativa a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per distribuire un modello usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `mymodel:1` con il nome e la versione del modello registrato:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Per ulteriori informazioni, vedere la documentazione [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Servizio Web notebook VM (sviluppo/test)

Vedere [distribuire un modello in Azure Machine Learning macchina virtuale del notebook](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Istanze di contenitore di Azure (sviluppo/test)

Vedere [eseguire la distribuzione in istanze di contenitore di Azure](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Servizio Azure Kubernetes (sviluppo/test e produzione)

Vedere [eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Utilizzare i servizi Web

Ogni servizio Web distribuito fornisce un'API REST, quindi è possibile creare applicazioni client in un'ampia gamma di linguaggi di programmazione.
Se è stata abilitata l'autenticazione della chiave per il servizio, è necessario specificare una chiave di servizio come token nell'intestazione della richiesta.
Se è stata abilitata l'autenticazione basata su token per il servizio, è necessario fornire un token di Azure Machine Learning JWT come bearer token nell'intestazione della richiesta.

> [!TIP]
> È possibile recuperare il documento JSON dello schema dopo la distribuzione del servizio. Utilizzare la [proprietà swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) dal servizio Web distribuito, ad esempio `service.swagger_uri`, per ottenere l'URI del file di spavalderia del servizio Web locale.

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

Per altre informazioni, vedere [creare applicazioni client per l'utilizzo di servizi Web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schema del servizio Web (specifica OpenAPI)

Se è stata utilizzata la generazione automatica dello schema con la distribuzione, è possibile ottenere l'indirizzo della specifica OpenAPI per il servizio utilizzando la [proprietà swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). Ad esempio, `print(service.swagger_uri)`. Usare una richiesta GET o aprire l'URI in un browser per recuperare la specifica.

Il documento JSON seguente è un esempio di schema (OpenAPI Specification) generato per una distribuzione:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
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

Per ulteriori informazioni, vedere la [specifica openapi](https://swagger.io/specification/).

Per un'utilità che consente di creare librerie client dalla specifica, vedere [spavalderia-codegen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Inferenza batch
Azure Machine Learning le destinazioni di calcolo vengono create e gestite da Azure Machine Learning. Possono essere usati per la stima in batch da Azure Machine Learning pipeline.

Per una procedura dettagliata di inferenza batch con Azure Machine Learning calcolo, vedere [How to Run batch Predictions](tutorial-pipeline-batch-scoring-classification.md).

### <a id="iotedge"></a>Inferenza IoT Edge
Il supporto per la distribuzione in Edge è in anteprima. Per altre informazioni, vedere [Deploy Azure Machine Learning As an IOT Edge Module](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Aggiornare i servizi Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Distribuzione continua di modelli

È possibile distribuire i modelli in modo continuo usando l'estensione Machine Learning per [Azure DevOps](https://azure.microsoft.com/services/devops/). È possibile usare l'estensione Machine Learning per Azure DevOps per attivare una pipeline di distribuzione quando viene registrato un nuovo modello di apprendimento automatico in un'area di lavoro di Azure Machine Learning.

1. Iscriversi a [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), che rende possibile l'integrazione e il recapito continui dell'applicazione in qualsiasi piattaforma o cloud. Si noti che Azure Pipelines non è uguale [Machine Learning pipeline](concept-ml-pipelines.md#compare).

1. [Creare un progetto DevOps di Azure.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installare l' [estensione Machine Learning per Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Usare le connessioni al servizio per configurare una connessione dell'entità servizio all'area di lavoro Azure Machine Learning in modo che sia possibile accedere agli elementi. Passare a Impostazioni progetto, selezionare **connessioni al servizio**e quindi selezionare **Azure Resource Manager**:

    [![selezionare Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Nell'elenco **livello ambito** selezionare **AzureMLWorkspace**e quindi immettere il resto dei valori:

    ![Seleziona AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Per distribuire continuamente il modello di apprendimento automatico usando Azure Pipelines, in pipeline selezionare **versione**. Aggiungere un nuovo elemento, quindi selezionare l'elemento del **modello AzureML** e la connessione del servizio creata in precedenza. Selezionare il modello e la versione per attivare una distribuzione:

    [![selezionare il modello AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Abilitare il trigger del modello nell'elemento del modello. Quando si attiva il trigger, ogni volta che la versione specificata (ovvero la versione più recente) del modello viene registrata nell'area di lavoro, viene attivata una pipeline di rilascio di Azure DevOps.

    [![abilitare il trigger del modello](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Per altri progetti ed esempi di esempio, vedere questi repository di esempio in GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Scaricare un modello
Se si vuole scaricare il modello per usarlo nel proprio ambiente di esecuzione, è possibile eseguire questa operazione con i comandi SDK/CLI seguenti:

SDK di  -
```python
model_path = Model(ws,'mymodel').download()
```

Interfaccia della riga di comando:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Anteprima Distribuzione del modello senza codice

La distribuzione del modello senza codice è attualmente in anteprima e supporta i Framework di apprendimento automatico seguenti:

### <a name="tensorflow-savedmodel-format"></a>Formato Tensorflow SavedModel
I modelli Tensorflow devono essere registrati in **formato SavedModel** per funzionare con la distribuzione del modello senza codice.

Vedere [questo collegamento](https://www.tensorflow.org/guide/saved_model) per informazioni su come creare un SavedModel.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modelli ONNX

La registrazione e la distribuzione del modello ONNX sono supportate per qualsiasi grafo di inferenza di ONNX. I passaggi di pre-elaborazione e postelaborazione non sono attualmente supportati.

Di seguito è riportato un esempio di come registrare e distribuire un modello ONNX di MNIST:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Modelli Scikit-Learn

Nessuna distribuzione del modello di codice è supportata per tutti i tipi di modello Scikit-learn predefiniti.

Di seguito è riportato un esempio di come registrare e distribuire un modello sklearn senza codice aggiuntivo:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Nota: queste dipendenze sono incluse nel contenitore di inferenza sklearn predefinito:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modelli di pacchetto

In alcuni casi, potrebbe essere necessario creare un'immagine Docker senza distribuire il modello (se, ad esempio, si prevede di eseguire la [distribuzione nel servizio app Azure](how-to-deploy-app-service.md)). In alternativa, potrebbe essere necessario scaricare l'immagine ed eseguirla in un'installazione locale di Docker. Potrebbe anche essere necessario scaricare i file usati per compilare l'immagine, esaminarli, modificarli e compilare l'immagine manualmente.

La creazione di pacchetti di modelli consente di eseguire queste operazioni. Inserisce tutti gli asset necessari per ospitare un modello come servizio Web e consente di scaricare un'immagine Docker completamente compilata o i file necessari per crearne uno. Esistono due modi per usare la creazione di pacchetti di modelli:

**Scaricare un modello di pacchetto:** Scaricare un'immagine Docker contenente il modello e gli altri file necessari per ospitarla come servizio Web.

**Generare un Dockerfile:** Scaricare Dockerfile, Model, entry script e altri asset necessari per creare un'immagine docker. È quindi possibile ispezionare i file o apportare modifiche prima di compilare l'immagine localmente.

Entrambi i pacchetti possono essere usati per ottenere un'immagine Docker locale.

> [!TIP]
> La creazione di un pacchetto è simile alla distribuzione di un modello. Si usano un modello registrato e una configurazione di inferenza.

> [!IMPORTANT]
> Per scaricare un'immagine completamente compilata o creare un'immagine localmente, è necessario che [Docker](https://www.docker.com) sia installato nell'ambiente di sviluppo.

### <a name="download-a-packaged-model"></a>Scaricare un modello di pacchetto

Nell'esempio seguente viene compilata un'immagine, registrata nel registro contenitori di Azure per l'area di lavoro:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Dopo aver creato un pacchetto, è possibile usare `package.pull()` per eseguire il pull dell'immagine nell'ambiente Docker locale. L'output di questo comando visualizzerà il nome dell'immagine. ad esempio: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Dopo aver scaricato il modello, utilizzare il comando `docker images` per elencare le immagini locali:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Per avviare un contenitore locale basato su questa immagine, usare il comando seguente per avviare un contenitore denominato dalla shell o dalla riga di comando. Sostituire il valore di `<imageid>` con l'ID immagine restituito dal comando `docker images`.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Questo comando avvia la versione più recente dell'immagine denominata `myimage`. Esegue il mapping della porta locale 6789 alla porta nel contenitore in cui il servizio Web è in ascolto (5001). Assegna anche il nome `mycontainer` al contenitore, rendendo più semplice l'arresto del contenitore. Dopo l'avvio del contenitore è possibile inviare richieste a `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Genera Dockerfile e dipendenze

Nell'esempio seguente viene illustrato come scaricare il Dockerfile, il modello e gli altri asset necessari per compilare un'immagine localmente. Il parametro `generate_dockerfile=True` indica che si desidera che i file non siano un'immagine completamente compilata.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Questo codice Scarica i file necessari per compilare l'immagine nella directory `imagefiles`. Il Dockerfile incluso nei file salvati fa riferimento a un'immagine di base archiviata in un registro contenitori di Azure. Quando si compila l'immagine nell'installazione Docker locale, è necessario usare l'indirizzo, il nome utente e la password per l'autenticazione al registro di sistema. Usare la procedura seguente per compilare l'immagine usando un'installazione locale di Docker:

1. Da una shell o da una sessione della riga di comando, usare il comando seguente per autenticare Docker con il registro contenitori di Azure. Sostituire `<address>`, `<username>`e `<password>` con i valori recuperati da `package.get_container_registry()`.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Per compilare l'immagine, usare il comando seguente. Sostituire `<imagefiles>` con il percorso della directory in cui `package.save()` salvati i file.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Questo comando imposta il nome dell'immagine su `myimage`.

Per verificare che l'immagine sia compilata, usare il comando `docker images`. Verrà visualizzata l'immagine `myimage` nell'elenco:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Per avviare un nuovo contenitore basato su questa immagine, usare il comando seguente:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Questo comando avvia la versione più recente dell'immagine denominata `myimage`. Esegue il mapping della porta locale 6789 alla porta nel contenitore in cui il servizio Web è in ascolto (5001). Assegna anche il nome `mycontainer` al contenitore, rendendo più semplice l'arresto del contenitore. Dopo l'avvio del contenitore è possibile inviare richieste a `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Client di esempio per il test del contenitore locale

Il codice seguente è un esempio di un client Python che può essere usato con il contenitore:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Per i client di esempio in altri linguaggi di programmazione, vedere [utilizzo di modelli distribuiti come servizi Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Arrestare il contenitore Docker

Per arrestare il contenitore, usare il comando seguente da una shell o riga di comando diversa:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare un servizio Web distribuito, usare `service.delete()`.
Per eliminare un modello registrato, usare `model.delete()`.

Per ulteriori informazioni, vedere la documentazione relativa a [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Utilizzare un modello di Azure Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)

