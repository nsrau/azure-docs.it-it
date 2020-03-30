---
title: Come e dove distribuire i modelli
titleSuffix: Azure Machine Learning
description: Informazioni su come e dove distribuire i modelli di Azure Machine Learning, incluse le istanze di Azure Container, il servizio Azure Kubernetes, azure IoT Edge e le matrici di gate programmabili sul campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 96d9a0722ae04dc150b639dced34fa290da93630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159414"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Distribuire modelli con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come distribuire il modello di machine learning come servizio Web nel cloud di Azure o nei dispositivi Azure IoT Edge.Learn how to deploy your machine learning model as a web service in the Azure cloud or to Azure IoT Edge devices.

Il flusso di lavoro è simile indipendentemente dalla [posizione in cui si distribuisce](#target) il modello:The workflow is similar to matter where you deploy your model:

1. Registrare il modello.
1. Preparare la distribuzione (specificare asset, utilizzo, destinazione di calcolo).
1. Distribuire il modello nella destinazione di calcolo.
1. Testare il modello distribuito, denominato anche servizio Web.

Per altre informazioni sui concetti coinvolti nel flusso di lavoro di distribuzione, vedere Gestire, distribuire e monitorare i modelli con Azure Machine Learning.For more information on the concepts involved in the deployment workflow, see [Manage, deploy, and monitor models with Azure Machine Learning.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area](how-to-manage-workspace.md)di lavoro di Azure Machine Learning.For more information, see Create an Azure Machine Learning workspace .

- Un modello. Se non si dispone di un modello sottoposto a training, è possibile utilizzare i file di modello e di dipendenza forniti in [questa esercitazione.](https://aka.ms/azml-deploy-cloud)

- [L'estensione dell'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando di Azure per il servizio Machine Learning, [l'SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)di Azure Machine Learning per Python o l'estensione Visual Studio Code di [Azure Machine Learning.](tutorial-setup-vscode-extension.md)

## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

Il codice seguente mostra come connettersi a un'area di lavoro di Azure Machine Learning usando le informazioni memorizzate nella cache dell'ambiente di sviluppo locale:The following code shows how to connect to an Azure Machine Learning workspace by using information cached to the local development environment:

+ **Uso dell'SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Per altre informazioni sull'uso dell'SDK per connettersi a un'area di lavoro, vedere la documentazione di Azure Machine Learning SDK per Python.For more information on using the SDK to connect to a [workspace,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) see the Azure Machine Learning SDK for Python documentation.

+ **Uso dell'interfaccia della riga di comando**

   Quando si utilizza l'interfaccia della riga di comando, utilizzare il `-w` parametro o `--workspace-name` per specificare l'area di lavoro per il comando.

+ **Uso di Visual Studio Code**

   Quando si utilizza Visual Studio Code, si seleziona l'area di lavoro utilizzando un'interfaccia grafica. Per altre informazioni, vedere Distribuire e gestire modelli nella documentazione dell'estensione del codice di Visual Studio.For more information, see [Deploy and manage models](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) in the Visual Studio Code extension documentation.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrare il modello

Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Ad esempio, se si dispone di un modello archiviato in più file, è possibile registrarli come un singolo modello nell'area di lavoro. Dopo aver registrato i file, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

> [!TIP]
> Quando si registra un modello, si fornisce il percorso di una posizione cloud (da un'esecuzione di training) o di una directory locale. Questo percorso è solo per individuare i file da caricare come parte del processo di registrazione. Non è necessario che corrisponda al percorso utilizzato nello script di immissione. Per ulteriori informazioni, consultate Individuare i file di [modello nello script di immissione.](#load-model-files-in-your-entry-script)

I modelli di Apprendimento automatico vengono registrati nell'area di lavoro di Azure Machine Learning.Machine learning models are registered in your Azure Machine Learning workspace. Il modello può provenire da Azure Machine Learning o da un'altra posizione. Quando si registra un modello, è possibile fornire facoltativamente metadati sul modello. I `tags` `properties` dizionari e applicati a una registrazione del modello possono quindi essere utilizzati per filtrare i modelli.

Negli esempi seguenti viene illustrato come registrare un modello.

### <a name="register-a-model-from-an-experiment-run"></a>Registrare un modello da un'esecuzione di un esperimentoRegister a model from an experiment run

I frammenti di codice in questa sezione illustrano come registrare un modello da un'esecuzione di training:The code snippets in this section demonstrate how to register a model from a training run:

> [!IMPORTANT]
> Per usare questi frammenti, è necessario aver eseguito in precedenza `Run` un'esecuzione di training ed è necessario avere accesso all'oggetto (esempio SDK) o al valore dell'ID di esecuzione (esempio CLI). Per ulteriori informazioni sui modelli di training, vedere [Impostare destinazioni](how-to-set-up-training-targets.md)di calcolo per il training del modello.

+ **Uso dell'SDK**

  Quando si usa l'SDK per eseguire il training di un modello, è possibile ricevere un oggetto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) o [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) a seconda di come è stato eseguito il training del modello. Ogni oggetto può essere utilizzato per registrare un modello creato da un'esecuzione dell'esperimento.

  + Registrare un `azureml.core.Run` modello da un oggetto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Il `model_path` parametro fa riferimento alla posizione cloud del modello. In questo esempio viene utilizzato il percorso di un singolo file. Per includere più file nella `model_path` registrazione del modello, impostare il percorso di una cartella che contiene i file. Per altre informazioni, vedere la documentazione [run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registrare un `azureml.train.automl.run.AutoMLRun` modello da un oggetto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In questo esempio, i `metric` parametri e `iteration` non sono specificati, pertanto verrà registrata l'iterazione con la metrica primaria migliore. Il `model_id` valore restituito dall'esecuzione viene utilizzato al posto del nome di un modello.

    Per altre informazioni, vedere la documentazione di [AutoMLRun.register_model.For](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) more information, see the AutoMLRun.register_model documentation.

+ **Uso dell'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  Il `--asset-path` parametro fa riferimento alla posizione cloud del modello. In questo esempio viene utilizzato il percorso di un singolo file. Per includere più file nella `--asset-path` registrazione del modello, impostare il percorso di una cartella che contiene i file.

+ **Uso di Visual Studio Code**

  Registrare i modelli usando qualsiasi file di modello o cartelle usando l'estensione codice di Visual Studio.Register models using any model files or folders by using [the Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) extension.

### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file localeRegister a model from a local file

È possibile registrare un modello fornendo il percorso locale del modello. È possibile specificare il percorso di una cartella o di un singolo file. È possibile usare questo metodo per registrare i modelli sottoposti a training con Azure Machine Learning e quindi scaricati. È anche possibile usare questo metodo per registrare modelli sottoposti a training all'esterno di Azure Machine Learning.You can also use this method to register models trained outside of Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Utilizzo di SDK e ONNX**

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

  Per includere più file nella `model_path` registrazione del modello, impostare il percorso di una cartella che contiene i file.

+ **Uso dell'interfaccia della riga di comando**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Per includere più file nella `-p` registrazione del modello, impostare il percorso di una cartella che contiene i file.

**Stima del tempo**: Circa 10 secondi.

Per ulteriori informazioni, vedere la documentazione relativa alla [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Per altre informazioni sull'uso di modelli sottoposti a training all'esterno di Azure Machine Learning, vedere [Come distribuire un modello esistente.](how-to-deploy-existing-model.md)

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Endpoint singoli o multimodello
Azure ML supporta la distribuzione di uno o più modelli dietro un singolo endpoint.

Gli endpoint multimodello usano un contenitore condiviso per ospitare più modelli. Ciò consente di ridurre i costi generali, migliorare l'utilizzo e concatenare i moduli in insiemi. I modelli specificati nello script di distribuzione vengono montati e resi disponibili sul disco del contenitore di servizio: è possibile caricarli in memoria su richiesta e assegnare un punteggio in base al modello specifico richiesto in fase di assegnazione del punteggio.

Per un esempio E2E, che mostra come usare più modelli dietro un singolo endpoint con contenitore, vedere [questo esempioFor](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model) an E2E example, which shows how to use multiple models behind a single containerized endpoint, see this example

## <a name="prepare-to-deploy"></a>Preparare la distribuzione

Per distribuire il modello come servizio, sono necessari i componenti seguenti:To deploy the model as a service, you need the following components:

* **Definire l'ambiente di inferenza**. Questo ambiente incapsula le dipendenze necessarie per eseguire il modello per l'inferenza.
* Definire il codice di **assegnazione del punteggio**. Questo script accetta le richieste, ottiene un punteggio delle richieste utilizzando il modello e restituisce i risultati.
* **Definire la configurazione dell'inferenza**. La configurazione dell'inferenza specifica la configurazione dell'ambiente, lo script di voce e altri componenti necessari per eseguire il modello come servizio.

Dopo aver creato i componenti necessari, è possibile profilare il servizio che verrà creato in seguito alla distribuzione del modello per comprenderne i requisiti di CPU e memoria.

### <a name="1-define-inference-environment"></a>1. Definire l'ambiente di inferenza

Una configurazione di inferenza descrive come configurare il servizio Web contenente il modello. Viene usato in un secondo momento, quando si distribuisce il modello.

La configurazione dell'inferenza usa gli ambienti di Azure Machine Learning per definire le dipendenze software necessarie per la distribuzione. Gli ambienti consentono di creare, gestire e riutilizzare le dipendenze software necessarie per la formazione e la distribuzione. È possibile creare un ambiente da file di dipendenze personalizzati o usare uno degli ambienti di Azure Machine Learning curati. Il seguente YAML è un esempio di un file di dipendenze Conda per l'inferenza. Si noti che è necessario indicare i valori predefiniti di azureml con verion >1.0.45 come dipendenza pip, perché contiene la funzionalità necessaria per ospitare il modello come servizio Web.Note that you must indicate azureml-defaults with verion >- 1.0.45 as a pip dependency, because it contains the functionality needed to host the model as a web service. Se si desidera utilizzare la generazione automatica dello `inference-schema` schema, lo script di ingresso deve importare anche i pacchetti.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Se la dipendenza è disponibile tramite Conda e pip (da PyPi), Microsoft consiglia di utilizzare la versione Conda, poiché i pacchetti Conda in genere sono dotati di file binari predefiniti che rendono l'installazione più affidabile.
>
> Per ulteriori informazioni, consultate [Informazioni su Conda e Pip.](https://www.anaconda.com/understanding-conda-and-pip/)
>
> Per verificare se la dipendenza è disponibile `conda search <package-name>` tramite Conda, utilizzare [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) il [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)comando oppure gli indici del pacchetto in e .

È possibile utilizzare il file delle dipendenze per creare un oggetto ambiente e salvarlo nell'area di lavoro per un utilizzo futuro:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Definire il codice di punteggio

Lo script di avvio riceve i dati inviati a un servizio Web distribuito e li passa al modello. Riceve quindi la risposta restituita dal modello e la restituisce al client. *Lo script è specifico del modello.* Deve comprendere i dati che il modello si aspetta e restituisce.

Lo script contiene due funzioni per il caricamento e l'esecuzione del modello:

* `init()`: in genere, questa funzione carica il modello in un oggetto globale. Questa funzione viene eseguita una sola volta, quando viene avviato il contenitore Docker per il servizio Web.

* `run(input_data)`: questa funzione utilizza il modello per stimare un valore in base ai dati di input. Gli input e gli output dell'esecuzione usano in genere JSON per la serializzazione e la deserializzazione. È anche possibile usare dati binari non elaborati. È possibile trasformare i dati prima dell'invio al modello o prima della restituzione al client.

#### <a name="load-model-files-in-your-entry-script"></a>Caricare i file di modello nello script di immissione

Esistono due modi per individuare i modelli nello script di immissione:
* `AZUREML_MODEL_DIR`: variabile di ambiente contenente il percorso della posizione del modello.
* `Model.get_model_path`: UN'API che restituisce il percorso del file del modello utilizzando il nome del modello registrato.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR è una variabile di ambiente creata durante la distribuzione del servizio. È possibile utilizzare questa variabile di ambiente per trovare la posizione dei modelli distribuiti.

Nella tabella seguente viene descritto il valore di AZUREML_MODEL_DIR a seconda del numero di modelli distribuiti:

| Distribuzione | Valore della variabile di ambiente |
| ----- | ----- |
| Modello singolo | Percorso della cartella contenente il modello. |
| Modelli multipli | Percorso della cartella contenente tutti i modelli. I modelli si trovano in base`$MODEL_NAME/$VERSION`al nome e alla versione in questa cartella ( ) |

Durante la registrazione e la distribuzione del modello, i modelli vengono posizionati nel percorso AZUREML_MODEL_DIR e i relativi nomi di file originali vengono mantenuti.

Per ottenere il percorso di un file di modello nello script di immissione, combinare la variabile di ambiente con il percorso del file che si sta cercando.

**Esempio di modello singolo**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Esempio di modello multiplo**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Quando si registra un modello, si fornisce un nome di modello che viene utilizzato per la gestione del modello nel Registro di sistema. Questo nome viene utilizzato con il metodo [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) per recuperare il percorso del file o dei file del file system locale. Se si registra una cartella o una raccolta di file, questa API restituisce il percorso della directory che contiene tali file.

Quando si registra un modello, gli si assegna un nome. Il nome corrisponde alla posizione del modello, localmente o durante la distribuzione del servizio.

#### <a name="optional-define-model-web-service-schema"></a>(Facoltativo) Definire lo schema del servizio Web modelloDefine model web service schema

Per generare automaticamente uno schema per il servizio Web, fornire un esempio dell'input e/o dell'output nel costruttore per uno degli oggetti di tipo definito. Il tipo e l'esempio vengono utilizzati per creare automaticamente lo schema. Azure Machine Learning crea quindi una specifica [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) per il servizio Web durante la distribuzione.

Questi tipi sono attualmente supportati:

* `pandas`
* `numpy`
* `pyspark`
* Oggetto Python standard

Per usare la generazione dello `inference-schema` schema, includere il pacchetto open source nel file delle dipendenze. Per ulteriori informazioni su [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)questo pacchetto, vedere . Definire i formati di esempio `input_sample` `output_sample` di input e output nelle variabili e , che rappresentano i formati di richiesta e risposta per il servizio Web. Usare questi esempi negli elementi Decorator `run()` delle funzioni di input e output nella funzione. Nell'esempio di apprendimento di scikit seguente viene utilizzata la generazione dello schema.

##### <a name="example-entry-script"></a>Script di immissione di esempio

L'esempio seguente illustra come accettare e restituire dati JSON:The following example demonstrates how to accept and return JSON data:

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

Nell'esempio seguente viene illustrato come `<key: value>` definire i dati di input come dizionario utilizzando un Frame di dati. Questo metodo è supportato per l'utilizzo del servizio Web distribuito da Power BI. Per[altre informazioni su come usare il servizio Web da Power BI,](https://docs.microsoft.com/power-bi/service-machine-learning-integration)vedere Usare il servizio Web.

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

Per altri esempi, vedere gli script seguenti:For more examples, see the following scripts:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Dati binari](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Definire la configurazione dell'inferenza
    
Nell'esempio seguente viene illustrato il caricamento di un ambiente dall'area di lavoro e l'utilizzo con la configurazione di inferenza:The following example demonstrates loading an environment from your workspace and then using it with the inference configuration:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Per ulteriori informazioni sugli ambienti, vedere Creare e gestire ambienti per la formazione e la [distribuzione.](how-to-use-environments.md)

Per altre informazioni sulla configurazione dell'inferenza, vedere la documentazione relativa alla classe [InferenceConfig.For](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) more information on inference configuration, see the InferenceConfig class documentation.

Per informazioni sull'utilizzo di un'immagine Docker personalizzata con una configurazione di inferenza, vedere [Come distribuire un modello utilizzando un'immagine Docker personalizzata.](how-to-deploy-custom-docker-image.md)

#### <a name="cli-example-of-inferenceconfig"></a>Esempio di CLI di InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Il comando seguente illustra come distribuire un modello utilizzando l'interfaccia della riga di comando:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In questo esempio, la configurazione specifica le seguenti impostazioni:

* Che il modello richiede Python.
* Script [di immissione](#script), utilizzato per gestire le richieste Web inviate al servizio distribuito.
* Il file Conda che descrive i pacchetti Python necessari per l'inferenza.

Per informazioni sull'utilizzo di un'immagine Docker personalizzata con una configurazione di inferenza, vedere [Come distribuire un modello utilizzando un'immagine Docker personalizzata.](how-to-deploy-custom-docker-image.md)

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Facoltativo) Profilare il modello per determinare l'utilizzo delle risorse

Dopo aver registrato il modello e preparato gli altri componenti necessari per la distribuzione, è possibile determinare la CPU e la memoria necessarie per il servizio distribuito. La profilatura verifica il servizio che esegue il modello e restituisce informazioni quali l'utilizzo della CPU, l'utilizzo della memoria e la latenza di risposta. Fornisce inoltre una raccomandazione per la CPU e la memoria in base all'utilizzo delle risorse.

Per profilare il modello, è necessario:
* Un modello registrato.
* Una configurazione di inferenza basata sullo script di ingresso e sulla definizione dell'ambiente di inferenza.
* Un set di dati tabulare a colonna singola, in cui ogni riga contiene una stringa che rappresenta i dati della richiesta di esempio.

> [!IMPORTANT]
> A questo punto è supportata solo la profilatura dei servizi che prevedono che i dati della richiesta siano una stringa, ad esempio: json serializzato di stringa, testo, immagine serializzata in base alla stringa e così via. Il contenuto di ogni riga del set di dati (stringa) verrà inserito nel corpo della richiesta HTTP e inviato al servizio incapsulando il modello per il punteggio.

Di seguito è riportato un esempio di come è possibile creare un set di dati di input per profilare un servizio che prevede che i dati della richiesta in ingresso contengano json serializzato. In questo caso, è stato creato un set di dati basato su cento istanze dello stesso contenuto di dati della richiesta. Negli scenari reali si consiglia di usare set di dati più grandi contenenti vari input, soprattutto se l'utilizzo/comportamento delle risorse del modello è dipendente dall'input.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Quando il set di dati contiene i dati di richiesta di esempio è pronto, creare una configurazione di inferenza. La configurazione dell'inferenza si basa sul score.py e sulla definizione dell'ambiente. Nell'esempio seguente viene illustrato come creare la configurazione di inferenza ed eseguire la profilatura:The following example demonstrates how to create the inference configuration and run profiling:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

Il comando seguente illustra come profilare un modello utilizzando l'interfaccia della riga di comando:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Per rendere persistenti le informazioni restituite dalla profilatura, utilizzare tag o proprietà per il modello. L'utilizzo di tag o proprietà archivia i dati con il modello nel Registro di sistema del modello. Negli esempi seguenti viene illustrata `requestedCpu` `requestedMemoryInGb` l'aggiunta di un nuovo tag contenente le informazioni e:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Distribuisci nella destinazione

La distribuzione usa la configurazione di distribuzione della configurazione di inferenza per distribuire i modelli. Il processo di distribuzione è simile indipendentemente dalla destinazione di calcolo. La distribuzione in AKS è leggermente diversa perché è necessario fornire un riferimento al cluster AKS.

### <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo

È possibile usare le destinazioni di calcolo seguenti o le risorse di calcolo per ospitare la distribuzione del servizio Web:You can use the following compute targets, or compute resources, to host your web service deployment:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Definire la configurazione di distribuzioneDefine your deployment configuration

Prima di distribuire il modello, è necessario definire la configurazione di distribuzione. *La configurazione di distribuzione è specifica della destinazione di calcolo che ospiterà il servizio Web.The deployment configuration is specific to the compute target that will host the web service.* Ad esempio, quando si distribuisce un modello in locale, è necessario specificare la porta in cui il servizio accetta le richieste. La configurazione di distribuzione non fa parte dello script di immissione. Viene utilizzato per definire le caratteristiche della destinazione di calcolo che ospiterà il modello e lo script di voce.

Potrebbe anche essere necessario creare la risorsa di calcolo, se, ad esempio, non si dispone già di un'istanza del servizio Azure Kubernetes (AKS) associata all'area di lavoro.

Nella tabella seguente viene fornito un esempio di creazione di una configurazione di distribuzione per ogni destinazione di calcolo:The following table provides an example of creating a deployment configuration for each compute target:

| Destinazione del calcolo | Esempio di configurazione della distribuzione |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Istanze di Azure Container | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Servizio Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Le classi per i servizi Web locali, istanze del `azureml.core.webservice`contenitore di Azure e AKS possono essere importate da :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-ssl"></a>Protezione delle distribuzioni con SSL

Per ulteriori informazioni su come proteggere la distribuzione di un servizio Web, vedere [Utilizzare SSL per proteggere un servizio Web.](how-to-secure-web-service.md#enable)

### <a name="local-deployment"></a><a id="local"></a>Distribuzione locale

Per distribuire un modello in locale, è necessario che Docker sia installato nel computer locale.

#### <a name="using-the-sdk"></a>Uso dell'SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per ulteriori informazioni, vedere la documentazione relativa a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per distribuire un modello tramite l'interfaccia della riga di comando, utilizzare il comando seguente. Sostituire `mymodel:1` con il nome e la versione del modello registrato:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Per altre informazioni, vedere la documentazione relativa alla distribuzione del [modello az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="understanding-service-state"></a>Informazioni sullo stato del servizioUnderstanding service state

Durante la distribuzione del modello, è possibile che lo stato del servizio venga modificato durante la distribuzione completa.

Nella tabella seguente vengono descritti i diversi stati del servizio:The following table describes the different service states:

| Stato del servizio Web | Descrizione | Lo stato finale?
| ----- | ----- | ----- |
| Transizione | Il servizio è in fase di distribuzione. | No |
| Non integro | Il servizio è stato distribuito ma non è attualmente raggiungibile.  | No |
| Non pianificabile | Il servizio non può essere distribuito in questo momento a causa della mancanza di risorse. | No |
| Operazione non riuscita | La distribuzione del servizio non è riuscita a causa di un errore o di un arresto anomalo. | Sì |
| Healthy | Il servizio è integro e l'endpoint è disponibile. | Sì |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Servizio Web dell'istanza di calcolo (sviluppo/test)Compute instance web service (dev/test)

Vedere [Distribuire un modello in un'istanza](how-to-deploy-local-container-notebook-vm.md)di calcolo di Azure Machine Learning.See Deploy a model to Azure Machine Learning compute instance .

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Istanze del contenitore di Azure (sviluppo/test)Azure Container Instances (dev/test)

Vedere [Distribuire in istanze del contenitore di Azure.See Deploy to Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Servizio Azure Kubernetes (sviluppo/test e produzione)Azure Kubernetes Service (dev/testing and production)

Vedere [Distribuire al servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Test A/B (implementazione controllata)
Per altre informazioni, [vedere Implementazione controllata dei modelli ML.](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)

## <a name="consume-web-services"></a>Utilizzare i servizi Web

Ogni servizio Web distribuito fornisce un endpoint REST, in modo da poter creare applicazioni client in qualsiasi linguaggio di programmazione.
Se è stata abilitata l'autenticazione basata su chiave per il servizio, è necessario fornire una chiave del servizio come token nell'intestazione della richiesta.
Se è stata abilitata l'autenticazione basata su token per il servizio, è necessario fornire un token Web JSON di Azure Machine Learning (JWT) come token di connessione nell'intestazione della richiesta. 

La differenza principale è che **le chiavi sono statiche e possono essere rigenerate manualmente**e i token devono essere **aggiornati alla scadenza**. L'autenticazione basata su chiave è supportata per i servizi Web distribuiti per l'istanza del contenitore di Azure e il servizio Azure Kubernetes e l'autenticazione basata su token è disponibile **solo** per le distribuzioni del servizio Azure Kubernetes.Key-based auth is supported for Azure Container Instance and Azure Kubernetes Service deployed web-services, and token-based auth is only available for Azure Kubernetes Service deployments. Per altre informazioni ed esempi di codice specifici, vedere la [procedura](how-to-setup-authentication.md#web-service-authentication) per l'autenticazione.

> [!TIP]
> È possibile recuperare il documento JSON dello schema dopo aver distribuito il servizio. Utilizzare la [proprietà swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) dal servizio Web `service.swagger_uri`distribuito, ad esempio , per ottenere l'URI al file Swagger del servizio Web locale.

### <a name="request-response-consumption"></a>Consumo richiesta-risposta

Ecco un esempio di come richiamare il servizio in Python:Here's an example of how to invoke your service in Python:
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

Per ulteriori informazioni, consultate [Creare applicazioni client per l'utilizzo](how-to-consume-web-service.md)dei servizi Web.

### <a name="web-service-schema-openapi-specification"></a>Schema del servizio Web (specifica OpenAPI)

Se è stata utilizzata la generazione automatica dello schema con la distribuzione, è possibile ottenere l'indirizzo della specifica OpenAPI per il servizio utilizzando la [proprietà swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Ad esempio, `print(service.swagger_uri)`.) Utilizzare una richiesta GET o aprire l'URI in un browser per recuperare la specifica.

Il documento JSON seguente è un esempio di schema (specifica OpenAPI) generato per una distribuzione:The following JSON document is an example of a schema (OpenAPI specification) generated for a deployment:

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

Per ulteriori informazioni, vedere [Specifica OpenAPI](https://swagger.io/specification/).

Per un'utilità in grado di creare librerie client dalla specifica, vedere [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Inferenza batch
Azure Machine Learning Compute targets are created and managed by Azure Machine Learning. Possono essere usati per la stima batch dalle pipeline di Azure Machine Learning.They can be used for batch prediction from Azure Machine Learning pipelines.

Per una procedura dettagliata dell'inferenza di batch con il calcolo di Azure Machine Learning, vedere [Come eseguire stime batch.](tutorial-pipeline-batch-scoring-classification.md)

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Inferenza dell'edge IoT
Il supporto per la distribuzione nel perimetro è in anteprima. Per altre informazioni, vedere [Distribuire Azure Machine Learning come modulo IoT Edge.](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)


## <a name="update-web-services"></a><a id="update"></a>Aggiornare i servizi Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Distribuzione continua dei modelli

È possibile distribuire continuamente i modelli usando l'estensione Machine Learning per DevOps di [Azure.](https://azure.microsoft.com/services/devops/) È possibile usare l'estensione Machine Learning per Azure DevOps per attivare una pipeline di distribuzione quando viene registrato un nuovo modello di apprendimento automatico in un'area di lavoro di Azure Machine Learning.You can use the Machine Learning extension for Azure DevOps to trigger a deployment pipeline when a new machine learning model is registered in an Azure Machine Learning workspace.

1. Iscriversi a [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), che rende possibile l'integrazione continua e la distribuzione dell'applicazione a qualsiasi piattaforma o cloud. (Si noti che le pipeline di Azure non sono uguali alle pipeline di [Machine Learning.)](concept-ml-pipelines.md#compare)

1. [Creare un progetto DevOps di Azure.Create an Azure DevOps project.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installare [l'estensione di Machine Learning per le pipeline](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)di Azure.Install the Machine Learning extension for Azure Pipelines .

1. Usare le connessioni di servizio per configurare una connessione dell'entità servizio all'area di lavoro di Azure Machine Learning in modo da poter accedere agli elementi. Passare alle impostazioni del progetto, selezionare Connessioni servizio e quindi **Azure Resource Manager:Go**to project settings, select **Service connections**, and then select Azure Resource Manager :

    [![Selezionare Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Nell'elenco **Livello ambito** selezionare **AzureMLWorkspace**e quindi immettere gli altri valori:

    ![Selezionare AzureMLWorkspaceSelect AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Per distribuire continuamente il modello di apprendimento automatico tramite Pipeline di Azure, in Pipeline selezionare **versione**. Aggiungere un nuovo elemento e quindi selezionare l'elemento del modello **AzureML** e la connessione al servizio creata in precedenza. Selezionare il modello e la versione per attivare una distribuzione:Select the model and version to trigger a deployment:

    [![Selezionare il modello di AzureMLSelect AzureML Model](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Abilitare il trigger del modello nell'elemento del modello. Quando si attiva il trigger, ogni volta che la versione specificata (ovvero la versione più recente) di tale modello viene registrata nell'area di lavoro, viene attivata una pipeline di rilascio DevOps di Azure.When you turn on the trigger, every time the specified version (that is, the newest version) of that model is registered in your workspace, an Azure DevOps release pipeline is triggered.

    [![Abilitare il trigger del modelloEnable the model trigger](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Per altri progetti ed esempi di esempio, vedere questi repository di esempio in GitHub:For more sample projects and examples, see these sample repos in GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Scarica un modello
Se si desidera scaricare il modello per utilizzarlo nel proprio ambiente di esecuzione, è possibile farlo con i seguenti comandi SDK / CLI:

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

Interfaccia della riga di comando:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Anteprima) Distribuzione del modello senza codice

La distribuzione del modello senza codice è attualmente in anteprima e supporta i framework di Machine Learning seguenti:No-code model deployment is currently in preview and supports the following machine learning frameworks:

### <a name="tensorflow-savedmodel-format"></a>Formato Ortoflusso salvatoModello
I modelli Tensorflow devono essere registrati in **formato SavedModel** per funzionare con la distribuzione di modelli senza codice.

Vedere [questo collegamento](https://www.tensorflow.org/guide/saved_model) per informazioni su come creare un SavedModel.See this link for information on how to create a SavedModel.

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

La registrazione e la distribuzione del modello ONNX sono supportate per qualsiasi grafico di inferenza ONNX. I passaggi di pre-elaborazione e post-elaborazione non sono attualmente supportati.

Di seguito è riportato un esempio di come registrare e distribuire un modello MNIST ONNX:Here is an example of how to register and deploy an MNIST ONNX model:

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

Se si utilizza Pytorch, [l'esportazione di modelli da PyTorch a ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) contiene i dettagli sulla conversione e le limitazioni. 

### <a name="scikit-learn-models"></a>Modelli Scikit-learn

Nessuna distribuzione del modello di codice è supportata per tutti i tipi di modello scikit-learn incorporati.

Ecco un esempio di come registrare e distribuire un modello sklearn senza codice aggiuntivo:Here is an example of how to register and deploy a sklearn model with no extra code:

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

NOTA: queste dipendenze sono incluse nel contenitore di inferenza sklearn predefinito:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modelli di pacchetti

In alcuni casi, è possibile creare un'immagine Docker senza distribuire il modello (se, ad esempio, si prevede di eseguire la [distribuzione nel servizio app](how-to-deploy-app-service.md)di Azure ). In alternativa, è possibile scaricare l'immagine ed eseguirla su un'installazione Docker locale. Potresti anche voler scaricare i file utilizzati per creare l'immagine, esaminarli, modificarli e compilare l'immagine manualmente.

L'imballaggio del modello consente di eseguire queste operazioni. Crea un pacchetto di tutte le risorse necessarie per ospitare un modello come servizio Web e consente di scaricare un'immagine Docker completamente compilata o i file necessari per compilarne uno. Esistono due modi per utilizzare l'imballaggio del modello:

**Scaricare un modello in pacchetto:Download a packaged model:** Scaricare un'immagine Docker che contiene il modello e altri file necessari per ospitarla come servizio Web.Download a Docker image that contains the model and other files needed to host it as a web service.

**Generare un Dockerfile:** Scaricare il Dockerfile, il modello, lo script di ingresso e altre risorse necessarie per creare un'immagine Docker. È quindi possibile esaminare i file o apportare modifiche prima di compilare l'immagine in locale.

Entrambi i pacchetti possono essere utilizzati per ottenere un'immagine Docker locale.

> [!TIP]
> La creazione di un pacchetto è simile alla distribuzione di un modello. Utilizzare un modello registrato e una configurazione di inferenza.

> [!IMPORTANT]
> Per scaricare un'immagine completamente compilata o creare un'immagine in locale, è necessario che [Docker](https://www.docker.com) sia installato nell'ambiente di sviluppo.

### <a name="download-a-packaged-model"></a>Scaricare un modello in pacchettoDownload a packaged model

L'esempio seguente crea un'immagine registrata nel Registro di sistema del contenitore di Azure per l'area di lavoro:The following example builds an image, which is registered in the Azure container registry for your workspace:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Dopo aver creato un pacchetto, è possibile usare `package.pull()` per eseguire il pull dell'immagine nell'ambiente Docker locale. L'output di questo comando verrà visualizzato il nome dell'immagine. Ad esempio: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Dopo aver scaricato il `docker images` modello, utilizzare il comando per elencare le immagini locali:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Per avviare un contenitore locale basato su questa immagine, utilizzare il comando seguente per avviare un contenitore denominato dalla shell o dalla riga di comando. Sostituire `<imageid>` il valore con l'ID `docker images` immagine restituito dal comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Questo comando avvia la versione `myimage`più recente dell'immagine denominata . Esegue il mapping della porta locale 6789 alla porta nel contenitore su cui il servizio Web è in ascolto (5001). Assegna inoltre il `mycontainer` nome al contenitore, semplificando l'arresto del contenitore. Dopo l'avvio del contenitore, è possibile inviare richieste a `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Generare un Dockerfile e le dipendenzeGenerate a Dockerfile and dependencies

L'esempio seguente mostra come scaricare il Dockerfile, il modello e altre risorse necessarie per creare un'immagine in locale. Il `generate_dockerfile=True` parametro indica che si desidera che i file, non un'immagine completamente compilata.

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

Questo codice scarica i file necessari `imagefiles` per compilare l'immagine nella directory. Il Dockerfile incluso nei file salvati fa riferimento a un'immagine di base archiviata in un registro contenitori di Azure.The Dockerfile included in the saved files references a base image stored in an Azure container registry. Quando si compila l'immagine nell'installazione locale di Docker, è necessario utilizzare l'indirizzo, il nome utente e la password per eseguire l'autenticazione nel Registro di sistema. Utilizzare la procedura seguente per compilare l'immagine utilizzando un'installazione Docker locale:

1. Da una shell o da una sessione della riga di comando, usare il comando seguente per autenticare Docker con il Registro di sistema del contenitore di Azure.From a shell or command-line session, use the following command to authenticate Docker with the Azure container registry. Sostituire `<address>` `<username>`, `<password>` e con i `package.get_container_registry()`valori recuperati da .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Per compilare l'immagine, utilizzare il comando seguente. Sostituire `<imagefiles>` con il percorso `package.save()` della directory in cui sono stati salvati i file.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Questo comando imposta il `myimage`nome dell'immagine su .

Per verificare che l'immagine `docker images` sia compilata, utilizzare il comando . Dovresti vedere `myimage` l'immagine nell'elenco:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Per avviare un nuovo contenitore basato su questa immagine, utilizzare il comando seguente:To start a new container based on this image, use the following command:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Questo comando avvia la versione `myimage`più recente dell'immagine denominata . Esegue il mapping della porta locale 6789 alla porta nel contenitore su cui il servizio Web è in ascolto (5001). Assegna inoltre il `mycontainer` nome al contenitore, semplificando l'arresto del contenitore. Dopo l'avvio del contenitore, è possibile inviare richieste a `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Client di esempio per testare il contenitore localeExample client to test the local container

Il codice seguente è un esempio di un client Python che può essere usato con il contenitore:The following code is an example of a Python client that can be used with the container:

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

Per esempi client in altri linguaggi di programmazione, vedere [Utilizzare modelli distribuiti come servizi Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Arrestare il contenitore Docker

Per arrestare il contenitore, utilizzare il comando seguente da una shell o da una riga di comando diversa:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare un servizio Web distribuito, usare `service.delete()`.
Per eliminare un modello registrato, usare `model.delete()`.

Per ulteriori informazioni, consultate la documentazione relativa a [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Creazione avanzata di script di immissione

<a id="binary"></a>

### <a name="binary-data"></a>Dati binari

Se il modello accetta dati binari, ad esempio `score.py` un'immagine, è necessario modificare il file utilizzato per la distribuzione per accettare richieste HTTP non elaborate. Per accettare dati non `AMLRequest` elaborati, usare la `@rawhttp` classe nello `run()` script di immissione e aggiungere l'elemento Decorator alla funzione.

Di seguito è riportato un esempio di un che accetta dati binari:Here's an example of a `score.py` that accepts binary data:

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
> La classe `AMLRequest` si trova nello spazio dei nomi `azureml.contrib`. Le entità in questo spazio dei nomi cambiano frequentemente mentre si lavora per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere considerato un'anteprima non completamente supportata da Microsoft.Anything in this namespace should be considered a preview that's not fully supported by Microsoft.
>
> Se è necessario testarlo nell'ambiente di sviluppo locale, è possibile installare i componenti utilizzando il comando seguente:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Condivisione delle risorse tra origini (CORS)

La condivisione di risorse tra origini consente di richiedere risorse in una pagina Web da un altro dominio. CORS funziona tramite intestazioni HTTP inviate con la richiesta del client e restituite con la risposta del servizio. Per ulteriori informazioni su CORS e intestazioni valide, vedere [Condivisione](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) di risorse tra origini in Wikipedia.

Per configurare la distribuzione del modello `AMLResponse` per supportare CORS, utilizzare la classe nello script di immissione. Questa classe consente di impostare le intestazioni sull'oggetto risposta.

L'esempio seguente `Access-Control-Allow-Origin` imposta l'intestazione per la risposta dallo script di voce:

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
> La classe `AMLResponse` si trova nello spazio dei nomi `azureml.contrib`. Le entità in questo spazio dei nomi cambiano frequentemente mentre si lavora per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere considerato un'anteprima non completamente supportata da Microsoft.Anything in this namespace should be considered a preview that's not fully supported by Microsoft.
>
> Se è necessario testarlo nell'ambiente di sviluppo locale, è possibile installare i componenti utilizzando il comando seguente:
>
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello usando un'immagine Docker personalizzataHow to deploy a model using a custom Docker image](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Come usare un modello di Azure Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application InsightsMonitor your Azure Machine Learning models with Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Creare avvisi di eventi e trigger per le distribuzioni di modelliCreate event alerts and triggers for model deployments](how-to-use-event-grid.md)

