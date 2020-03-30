---
title: Usare e distribuire modelli esistenti
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning con modelli sottoposti a training all'esterno del servizio. È possibile registrare i modelli creati all'esterno di Azure Machine Learning e quindi distribuirli come servizio Web o modulo Azure IoT Edge.You can register models created outside Azure Machine Learning, and then deploy them as a web service or Azure IoT Edge module.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472376"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Usare un modello esistente con Azure Machine LearningUse an existing model with Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come usare un modello di apprendimento automatico esistente con Azure Machine Learning.Learn how to use an existing machine learning model with Azure Machine Learning.

Se si dispone di un modello di machine learning di cui è stato eseguito il training all'esterno di Azure Machine Learning, è comunque possibile usare il servizio per distribuire il modello come servizio Web o in un dispositivo IoT Edge.If you have a machine learning model that was trained outside Azure Machine Learning, you can still use the service to deploy the model as a web service or to an IoT Edge device. 

> [!TIP]
> In questo articolo vengono fornite informazioni di base sulla registrazione e la distribuzione di un modello esistente. Una volta distribuito, Azure Machine Learning fornisce il monitoraggio per il modello. Consente inoltre di archiviare i dati di input inviati alla distribuzione, che possono essere utilizzati per l'analisi della deriva dei dati o il training di nuove versioni del modello.
>
> Per altre informazioni sui concetti e i termini usati qui, vedere [Gestire, distribuire e monitorare](concept-model-management-and-deployment.md)i modelli di Machine Learning.
>
> Per informazioni generali sul processo di distribuzione, vedere [Distribuire modelli con Azure Machine Learning.](how-to-deploy-and-where.md)

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per ulteriori informazioni, consultate [Creare un'area di lavoro.](how-to-manage-workspace.md)

    > [!TIP]
    > Gli esempi Python in questo `ws` articolo presuppongono che la variabile sia impostata sull'area di lavoro di Azure Machine Learning.The Python examples in this article assume that the variable is set to your Azure Machine Learning workspace.
    >
    > Negli esempi dell'interfaccia `myworkspace` `myresourcegroup`della riga di comando viene utilizzato un segnaposto di e . Sostituirli con il nome dell'area di lavoro e il gruppo di risorse che la contiene.

* Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* [L'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e [l'estensione dell'interfaccia della riga](reference-azure-machine-learning-cli.md)di comando di Machine Learning .

* Un modello con training. Il modello deve essere reso persistente in uno o più file nell'ambiente di sviluppo.

    > [!NOTE]
    > Per illustrare la registrazione di un modello di cui è stato eseguito il training all'esterno di Azure [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)Machine Learning, i frammenti di codice di esempio in questo articolo usano i modelli creati dal progetto di analisi del sentiment di Twitter di Paolo Ripamonti: .

## <a name="register-the-models"></a>Registrare i modelli

La registrazione di un modello consente di archiviare, versione e tenere traccia dei metadati relativi ai modelli nell'area di lavoro. Negli esempi Python e CLI `models` seguenti, `model.h5` `model.w2v`la `encoder.pkl`directory `tokenizer.pkl` contiene i file , , e . In questo esempio vengono caricati i file contenuti nella `models` directory come nuova registrazione del modello denominata `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Per ulteriori informazioni, consultate il riferimento [Model.register().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> È inoltre possibile `tags` `properties` impostare gli oggetti di aggiunta e dizionario nel modello registrato. Questi valori possono essere utilizzati in un secondo momento per identificare un modello specifico. Ad esempio, il framework utilizzato, i parametri di formazione e così via.

Per altre informazioni, vedere il riferimento al [registro dei modelli az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)


Per altre informazioni sulla registrazione dei modelli in generale, vedere [Gestire, distribuire e monitorare](concept-model-management-and-deployment.md)i modelli di Apprendimento automatico.

## <a name="define-inference-configuration"></a>Definire la configurazione dell'inferenza

La configurazione di inferenza definisce l'ambiente utilizzato per eseguire il modello distribuito. La configurazione di inferenza fa riferimento alle entità seguenti, che vengono utilizzate per eseguire il modello quando viene distribuito:The inference configuration references the following entities, which are used to run the model when it's deployed:

* Uno script di avvio. Questo file `score.py`(denominato ) carica il modello all'avvio del servizio distribuito. È inoltre responsabile della ricezione dei dati, del passaggio al modello e della restituzione di una risposta.
* Un [ambiente](how-to-use-environments.md)Azure Machine Learning . Un ambiente definisce le dipendenze software necessarie per eseguire il modello e lo script di immissione.

L'esempio seguente mostra come usare l'SDK per creare un ambiente e quindi utilizzarlo con una configurazione di inferenza:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Per altre informazioni, vedere gli articoli seguenti:

+ [Come utilizzare gli ambienti](how-to-use-environments.md).
+ [Riferimento inferenceConfig.InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) reference.


L'interfaccia della riga di comando carica la configurazione di inferenza da un file YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Con l'interfaccia della riga di `myenv.yml` comando, l'ambiente conda viene definito nel file a cui fa riferimento la configurazione di inferenza. Il seguente YAML è il contenuto di questo file:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Per altre informazioni sulla configurazione dell'inferenza, vedere Distribuire modelli con Azure Machine Learning.For more information on inference configuration, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

### <a name="entry-script"></a>Script di immissione

Lo script di immissione `init()` ha `run(data)`solo due funzioni obbligatorie e . Queste funzioni vengono utilizzate per inizializzare il servizio all'avvio ed eseguire il modello utilizzando i dati delle richieste passati da un client. Il resto dello script gestisce il caricamento e l'esecuzione dei modelli.

> [!IMPORTANT]
> Non esiste uno script di immissione generico che funziona per tutti i modelli. È sempre specifico per il modello utilizzato. È necessario comprendere come caricare il modello, il formato dati previsto dal modello e come assegnare un punteggio ai dati utilizzando il modello.

Il codice Python seguente è`score.py`uno script di voce di esempio ( ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Per altre informazioni sugli script di immissione, vedere Distribuire modelli con Azure Machine Learning.For more information on entry scripts, see [Deploy models with Azure Machine Learning.](how-to-deploy-and-where.md)

## <a name="define-deployment"></a>Definire la distribuzioneDefine deployment

Il pacchetto [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) contiene le classi utilizzate per la distribuzione. La classe utilizzata determina dove viene distribuito il modello. Ad esempio, per eseguire la distribuzione come servizio Web nel servizio Azure Kubernetes, usare [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) per creare la configurazione di distribuzione.

Il codice Python seguente definisce una configurazione di distribuzione per una distribuzione locale. Questa configurazione distribuisce il modello come servizio Web nel computer locale.

> [!IMPORTANT]
> Una distribuzione locale richiede un'installazione funzionante di Docker nel computer locale:A local deployment requires a working installation of [Docker](https://www.docker.com/) on your local computer:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Per ulteriori informazioni, consultate il riferimento [LocalWebservice.deploy_configuration().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)

L'interfaccia della riga di comando carica la configurazione di distribuzione da un file YAML:The CLI loads the deployment configuration from a YAML file:

```YAML
{
    "computeType": "LOCAL"
}
```

La distribuzione in una destinazione di calcolo diversa, ad esempio Il servizio Azure Kubernetes nel cloud di Azure, è semplice come modificare la configurazione di distribuzione. Per ulteriori informazioni, vedere [Come e dove distribuire](how-to-deploy-and-where.md)i modelli .

## <a name="deploy-the-model"></a>Distribuire il modello

Nell'esempio riportato di seguito `sentiment`vengono caricate informazioni sul `sentiment`modello registrato denominato , quindi vengono distribuite come servizio denominato . Durante la distribuzione, la configurazione dell'inferenza e della distribuzione viene utilizzata per creare e configurare l'ambiente del servizio:During deployment, the inference configuration and deployment configuration are used to create and configure the service environment:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Per ulteriori informazioni, consultate le informazioni di riferimento su [Model.deploy().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Per distribuire il modello dall'interfaccia della riga di comando, utilizzare il comando seguente. Questo comando distribuisce la versione`sentiment:1`1 del modello registrato ( ) `inferenceConfig.json` utilizzando `deploymentConfig.json` l'inferenza e la configurazione di distribuzione archiviate nei file e :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Per altre informazioni, vedere il riferimento alla distribuzione del [modello az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

Per ulteriori informazioni sulla distribuzione, vedere [Come e dove distribuire](how-to-deploy-and-where.md)i modelli .

## <a name="request-response-consumption"></a>Consumo richiesta-risposta

Dopo la distribuzione, viene visualizzato l'URI del punteggio. Questo URI può essere utilizzato dai client per inviare richieste al servizio. L'esempio seguente è un client Python di base che invia dati al servizio e visualizza la risposta:The following example is a basic Python client that submits data to the service and displays the response:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Per ulteriori informazioni su come utilizzare il servizio distribuito, vedere [Creare un client](how-to-consume-web-service.md).

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i modelli di Azure Machine Learning con Application InsightsMonitor your Azure Machine Learning models with Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Come creare un client per un modello distribuitoHow to create a client for a deployed model](how-to-consume-web-service.md)
