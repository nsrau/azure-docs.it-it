---
title: Usare e distribuire modelli esistenti
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare Azure Machine Learning servizio con i modelli di cui è stato eseguito il training all'esterno del servizio. È possibile registrare i modelli creati all'esterno del servizio Azure Machine Learning e quindi distribuirli come servizio Web o modulo di Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: f30ac3d5e20b3f797e083972ac179fd29f6b1475
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182533"
---
# <a name="use-an-existing-model-with-azure-machine-learning-service"></a>Usare un modello esistente con il servizio Azure Machine Learning

Informazioni su come usare un modello di apprendimento automatico esistente con il servizio Azure Machine Learning.

Se è stato eseguito il training di un modello di machine learning all'esterno del servizio Azure Machine Learning, è comunque possibile usare il servizio per distribuire il modello come servizio Web o in un dispositivo IoT Edge. 

> [!TIP]
> Questo articolo fornisce informazioni di base sulla registrazione e sulla distribuzione di un modello esistente. Una volta distribuito, Azure Machine Learning servizio fornisce il monitoraggio per il modello. Consente inoltre di archiviare i dati di input inviati alla distribuzione, che possono essere utilizzati per l'analisi della deriva dei dati o per il training di nuove versioni del modello.
>
> Per altre informazioni sui concetti e i termini usati in questo articolo, vedere [gestire, distribuire e monitorare i modelli di Machine Learning](concept-model-management-and-deployment.md).
>
> Per informazioni generali sul processo di distribuzione, vedere [distribuire modelli con Azure Machine Learning servizio](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro](how-to-manage-workspace.md).

    > [!TIP]
    > Gli esempi di Python in questo articolo presuppongono che la variabile sia impostata sull' `ws` area di lavoro del servizio Azure Machine Learning.
    >
    > Gli esempi dell'interfaccia della riga di `myworkspace` comando `myresourcegroup`usano un segnaposto di e. Sostituirli con il nome dell'area di lavoro e il gruppo di risorse che lo contiene.

* [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* L' [interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) della riga di comando di Azure e l' [estensione CLI Machine Learning](reference-azure-machine-learning-cli.md).

* Un modello con training. Il modello deve essere salvato in modo permanente in uno o più file nell'ambiente di sviluppo.

    > [!NOTE]
    > Per dimostrare la registrazione di un modello sottoposto a training all'esterno di Azure Machine Learning servizio, i frammenti di codice di esempio in questo articolo usano i modelli creati dal progetto [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)di analisi del sentimento Twitter di Paolo Ripamonti:.

## <a name="register-the-models"></a>Registrare il modello o i modelli

La registrazione di un modello consente di archiviare, eseguire la versione e tenere traccia dei metadati relativi ai modelli nell'area di lavoro. Negli esempi Python e CLI `models` seguenti la directory contiene i `model.h5`file, `model.w2v`, `encoder.pkl`e `tokenizer.pkl` . Questo esempio carica i file contenuti nella `models` directory come nuova registrazione del modello denominata: `sentiment`

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Per ulteriori informazioni, vedere il riferimento a [Model. Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) .

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Per ulteriori informazioni, vedere la pagina relativa al riferimento al [Registro AZ ml Model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) .


Per altre informazioni sulla registrazione dei modelli in generale, vedere [gestire, distribuire e monitorare i modelli di Machine Learning](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definire la configurazione dell'inferenza

La configurazione dell'inferenza definisce l'ambiente utilizzato per eseguire il modello distribuito. La configurazione dell'inferenza fa riferimento alle entità seguenti, che vengono usate per eseguire il modello quando viene distribuito:

* Uno script di immissione. Questo file (denominato `score.py`) carica il modello all'avvio del servizio distribuito. È anche responsabile della ricezione di dati, della relativa trasmissione al modello e della restituzione di una risposta.
* [Ambiente](how-to-use-environments.md)del servizio Azure Machine Learning. Un ambiente definisce le dipendenze software necessarie per eseguire lo script del modello e della voce.

L'esempio seguente illustra come usare l'SDK per creare un ambiente e quindi usarlo con una configurazione di inferenza:

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
conda_dep.add_pip_package("keras")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Per altre informazioni, vedere i seguenti articoli:

+ [Come usare gli ambienti](how-to-use-environments.md).
+ Riferimento [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .


L'interfaccia della riga di comando carica la configurazione dell'inferenza da un file YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Con l'interfaccia della riga di comando, l'ambiente conda `myenv.yml` è definito nel file a cui fa riferimento la configurazione di inferenza. Il seguente YAML è il contenuto di questo file:

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
```

Per altre informazioni sulla configurazione dell'inferenza, vedere [distribuire modelli con Azure Machine Learning servizio](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Script di immissione

Lo script di immissione dispone solo di due funzioni `init()` obbligatorie `run(data)`, e. Queste funzioni vengono usate per inizializzare il servizio all'avvio ed eseguire il modello usando i dati della richiesta passati da un client. Il resto dello script gestisce il caricamento e l'esecuzione del modello o dei modelli.

> [!IMPORTANT]
> Non è disponibile uno script di immissione generico che funziona per tutti i modelli. È sempre specifico del modello usato. Deve comprendere come caricare il modello, il formato dei dati previsto dal modello e come assegnare un punteggio ai dati utilizzando il modello.

Il codice Python seguente è uno script di immissione di`score.py`esempio ():

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

Per ulteriori informazioni sugli script di immissione, vedere la pagina relativa alla [distribuzione di modelli con Azure Machine Learning Service](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definire la distribuzione

Il pacchetto [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) contiene le classi utilizzate per la distribuzione. La classe utilizzata determina la posizione in cui viene distribuito il modello. Ad esempio, per distribuire come servizio Web nel servizio Azure Kubernetes, usare [AksWebService. deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-) per creare la configurazione di distribuzione.

Il codice Python seguente definisce una configurazione di distribuzione per una distribuzione locale. Questa configurazione distribuisce il modello come servizio Web nel computer locale.

> [!IMPORTANT]
> Per una distribuzione locale è necessaria un'installazione funzionante di [Docker](https://www.docker.com/) nel computer locale:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Per ulteriori informazioni, vedere il riferimento [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) .

L'interfaccia della riga di comando carica la configurazione di distribuzione da un file YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

La distribuzione in una destinazione di calcolo diversa, ad esempio il servizio Azure Kubernetes nel cloud di Azure, è semplice come la modifica della configurazione della distribuzione. Per ulteriori informazioni, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Distribuire il modello

Nell'esempio seguente vengono caricate informazioni sul modello registrato `sentiment`denominato, quindi viene distribuito come servizio denominato. `sentiment` Durante la distribuzione, la configurazione dell'inferenza e la configurazione della distribuzione vengono usate per creare e configurare l'ambiente del servizio:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Per ulteriori informazioni, vedere il riferimento a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) .

Per distribuire il modello dall'interfaccia della riga di comando, usare il comando seguente. Questo comando distribuisce la versione 1 del modello registrato (`sentiment:1`) usando la configurazione di inferenza e distribuzione archiviata `deploymentConfig.json` `inferenceConfig.json` nei file e:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Per ulteriori informazioni, vedere il riferimento [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

Per ulteriori informazioni sulla distribuzione, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Consumo richiesta-risposta

Dopo la distribuzione, viene visualizzato l'URI di assegnazione dei punteggi. Questo URI può essere utilizzato dai client per inviare richieste al servizio. L'esempio seguente è un client Python di base che invia i dati al servizio e visualizza la risposta:

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

Per ulteriori informazioni sull'utilizzo del servizio distribuito, vedere la pagina relativa alla [creazione di un client](how-to-consume-web-service.md).

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Come creare un client per un modello distribuito](how-to-consume-web-service.md)
