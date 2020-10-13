---
title: Script di immissione autore per scenari avanzati
titleSuffix: Azure Machine Learning entry script authoring
description: Informazioni su come scrivere Azure Machine Learning script di immissione per la pre-e post-elaborazione durante la distribuzione.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.openlocfilehash: 3af681f561bc190ae7c0354afb7fc3591fa82b5c
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940073"
---
# <a name="advanced-entry-script-authoring"></a>Creazione avanzata di script di immissione

Questo articolo illustra come scrivere script di immissione per i casi d'uso specializzati.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia già un modello di apprendimento automatico con training che si intende distribuire con Azure Machine Learning. Per ulteriori informazioni sulla distribuzione di modelli, vedere [questa esercitazione](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Genera automaticamente uno schema di spavalderia

Per generare automaticamente uno schema per il servizio Web, fornire un esempio di input e/o output nel costruttore per uno degli oggetti tipo definiti. Il tipo e l'esempio vengono utilizzati per creare automaticamente lo schema. Azure Machine Learning quindi crea una specifica [openapi](https://swagger.io/docs/specification/about/) (spavalderia) per il servizio Web durante la distribuzione.

Questi tipi sono attualmente supportati:

* `pandas`
* `numpy`
* `pyspark`
* Oggetto Python standard

Per usare la generazione di schemi, includere il pacchetto open source `inference-schema` versione 1.1.0 o successiva nel file delle dipendenze. Per ulteriori informazioni su questo pacchetto, vedere [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Per generare il consumo di servizi web automatizzato di spavalderia, il punteggio della funzione Esegui script () deve avere la forma API:
* Primo parametro di tipo "StandardPythonParameterType", denominato Inputs, annidato contenente PandasDataframeParameterTypes.
* Secondo parametro facoltativo di tipo "StandardPythonParameterType", denominato le, che non è annidato.
* Restituisce un dizionario di tipo "StandardPythonParameterType", che probabilmente nidificato contenente PandasDataFrameParameterTypes.
Definire i formati di esempio di input e output `input_sample` nelle `output_sample` variabili e, che rappresentano i formati di richiesta e risposta per il servizio Web. Usare questi esempi negli elementi Decorator della funzione di input e output della `run()` funzione. Nell'esempio Scikit-learn seguente viene utilizzata la generazione dello schema.



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
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')

    # If your model were stored in the same directory as your score.py, you could also use the following:
    # model_path = os.path.abspath(os.path.join(os.path.dirname(__file_), 'sklearn_mnist_model.pkl')

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

## <a name="power-bi-compatible-endpoint"></a>Endpoint compatibile con Power BI 

Nell'esempio seguente viene illustrato come definire una forma API in base alle istruzioni precedenti. Questo metodo è supportato per l'utilizzo del servizio Web distribuito da Power BI. [Per ulteriori informazioni su come utilizzare il servizio Web da Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration), vedere.

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)

# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                            'input2': pandas_sample_input, 
                                            'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) #this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])

@input_schema('inputs', sample_input)
@input_schema('global_parameters', sample_global_parameters) #this is optional
@output_schema(sample_output)
def run(inputs, global_parameters):
    try:
        data = inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Dati binari (ad esempio immagine)

Se il modello accetta dati binari, ad esempio un'immagine, è necessario modificare il `score.py` file usato per la distribuzione per accettare richieste HTTP non elaborate. Per accettare dati non elaborati, usare la `AMLRequest` classe nello script di immissione e aggiungere l' `@rawhttp` elemento Decorator alla `run()` funzione.

Di seguito è riportato un esempio di un oggetto `score.py` che accetta dati binari:

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

La `AMLRequest` classe consente solo di accedere ai dati pubblicati non elaborati in Score.py, non è presente alcun componente lato client. Da un client, i dati vengono pubblicati normalmente. Il codice Python seguente, ad esempio, legge un file di immagine e inserisce i dati:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>Condivisione di risorse tra le origini (CORS)

La condivisione delle risorse tra le origini è un modo per consentire la richiesta di risorse in una pagina Web da un altro dominio. CORS funziona tramite le intestazioni HTTP inviate con la richiesta del client e restituite con la risposta del servizio. Per altre informazioni su CORS e sulle intestazioni valide, vedere [condivisione di risorse tra le origini](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in Wikipedia.

Per configurare la distribuzione del modello per il supporto di CORS, usare la `AMLResponse` classe nello script di immissione. Questa classe consente di impostare le intestazioni nell'oggetto risposta.

Nell'esempio seguente viene impostata l' `Access-Control-Allow-Origin` intestazione per la risposta dallo script di immissione:

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


> [!WARNING]
> Azure Machine Learning instraderà solo le richieste POST e GET ai contenitori che eseguono il servizio di assegnazione dei punteggi. Questo può causare errori a causa di browser che usano richieste di opzioni per eseguire il pre-flight delle richieste CORS.
> 


## <a name="load-registered-models"></a>Caricare modelli registrati

Esistono due modi per individuare i modelli nello script di immissione:
* `AZUREML_MODEL_DIR`: Variabile di ambiente contenente il percorso della posizione del modello.
* `Model.get_model_path`: Un'API che restituisce il percorso del file di modello utilizzando il nome del modello registrato.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR è una variabile di ambiente creata durante la distribuzione del servizio. È possibile usare questa variabile di ambiente per trovare il percorso dei modelli distribuiti.

Nella tabella seguente viene descritto il valore di AZUREML_MODEL_DIR in base al numero di modelli distribuiti:

| Distribuzione | Valore della variabile di ambiente |
| ----- | ----- |
| Modello singolo | Percorso della cartella che contiene il modello. |
| Più modelli | Percorso della cartella contenente tutti i modelli. I modelli si trovano in base al nome e alla versione in questa cartella ( `$MODEL_NAME/$VERSION` ) |

Durante la registrazione e la distribuzione del modello, i modelli vengono inseriti nel percorso AZUREML_MODEL_DIR e i relativi nomi file originali vengono conservati.

Per ottenere il percorso di un file di modello nello script di immissione, combinare la variabile di ambiente con il percorso del file che si sta cercando.

**Esempio di modello singolo**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Esempio di più modelli**

In questo scenario, due modelli sono registrati con l'area di lavoro:

* `my_first_model`: Contiene un solo file ( `my_first_model.pkl` ) ed è presente una sola versione ( `1` ).
* `my_second_model`: Contiene un file ( `my_second_model.pkl` ) e sono disponibili due versioni, `1` e `2` .

Quando il servizio è stato distribuito, durante l'operazione di distribuzione vengono forniti entrambi i modelli:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

Nell'immagine Docker che ospita il servizio, la `AZUREML_MODEL_DIR` variabile di ambiente contiene la directory in cui si trovano i modelli.
In questa directory ognuno dei modelli si trova in un percorso di directory `MODEL_NAME/VERSION` . Dove `MODEL_NAME` è il nome del modello registrato e `VERSION` è la versione del modello. I file che compongono il modello registrato vengono archiviati in tali directory.

In questo esempio, i percorsi sarebbero `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` e `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Quando si registra un modello, è necessario specificare un nome di modello utilizzato per la gestione del modello nel registro di sistema. Usare questo nome con il metodo [Model.get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) per recuperare il percorso del file del modello o dei file nel file system locale. Se si registra una cartella o una raccolta di file, questa API restituisce il percorso della directory che contiene tali file.

Quando si registra un modello, è necessario assegnargli un nome. Il nome corrisponde alla posizione in cui viene inserito il modello, localmente o durante la distribuzione del servizio.

## <a name="framework-specific-examples"></a>Esempi specifici del Framework

Altri esempi di script di immissione per i casi d'uso di Machine Learning specifici sono disponibili di seguito:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.yml)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi relativi a una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Creazione di applicazioni client per l'utilizzo di servizi Web](how-to-consume-web-service.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Creare avvisi e trigger per gli eventi per le distribuzioni di modelli](how-to-use-event-grid.md)
