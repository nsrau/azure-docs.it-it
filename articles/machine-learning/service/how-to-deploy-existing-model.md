---
title: Come usare un modello esistente
titleSuffix: Azure Machine Learning service
description: Informazioni su come è possibile usare il servizio di Azure Machine Learning con i modelli che sono stati sottoposti a training all'esterno del servizio. È possibile registrare i modelli creati all'esterno del servizio di Azure Machine Learning e quindi distribuirli come un servizio web o il modulo Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453684"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Come usare un modello esistente con il servizio di Azure Machine Learning

Informazioni su come usare un modello con il servizio Azure Machine Learning esistente di apprendimento.

Se si dispone di un modello di machine learning che è stato eseguito il training all'esterno del servizio Azure Machine Learning, è comunque possibile usare il servizio per distribuire il modello come servizio web o a un dispositivo IoT Edge. 

> [!TIP]
> Questo articolo fornisce informazioni di base sulla registrazione e la distribuzione di un modello esistente. Una volta distribuita, servizio Azure Machine Learning fornisce il monitoraggio per il modello. Consente inoltre di archiviare i dati di input inviati per la distribuzione, che può essere usata per i dati sfasamento analisi o la formazione nuove versioni del modello.
>
> Per altre informazioni sui concetti e termini usati in questo caso, vedere [gestire, distribuire e monitorare i modelli di machine learning](concept-model-management-and-deployment.md).
>
> Per informazioni generali sul processo di distribuzione, vedere [distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro](setup-create-workspace.md).

    > [!TIP]
    > Gli esempi di Python in questo articolo presuppongono che il `ws` variabile è impostata per l'area di lavoro del servizio di Azure Machine Learning.
    >
    > Negli esempi dell'interfaccia della riga viene usato un segnaposto del `myworkspace` e `myresourcegroup`. Sostituirli con il nome dell'area di lavoro e il gruppo di risorse che lo contiene.

* Azure Machine Learning SDK. Per altre informazioni, vedere la sezione di Python SDK dei [creare un'area di lavoro](setup-create-workspace.md#sdk).

* Il [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e [estensione dell'interfaccia della riga di Machine Learning](reference-azure-machine-learning-cli.md).

* Un modello con training. Il modello deve essere mantenuto in uno o più file nell'ambiente di sviluppo.

    > [!NOTE]
    > Per illustrare la registrazione di un modello sottoposto a training di fuori del servizio di Azure Machine Learning, i frammenti di codice riportato in questo articolo usano i modelli creati dal progetto di analisi del sentiment Twitter di Paolo Ripamonti: [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrazione dei modelli

Registrazione di un modello consente di archiviare, versione e tenere traccia dei metadati relativi ai modelli nell'area di lavoro. Negli esempi seguenti Python e della riga di comando, il `models` directory contiene il `model.h5`, `model.w2v`, `encoder.pkl`, e `tokenizer.pkl` file. In questo esempio carica i file contenuti nel `models` directory come una nuova registrazione di modello denominata `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Per altre informazioni, vedere la [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) riferimento.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Per altre informazioni, vedere la [registrazione di modello ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) riferimento.


Per ulteriori informazioni sulla registrazione del modello in generale, vedere [gestire, distribuire e monitorare i modelli di machine learning](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definire la configurazione di inferenza

La configurazione di inferenza definisce l'ambiente usato per eseguire il modello distribuito. La configurazione di inferenza fa riferimento ai file seguenti che consentono di eseguire il modello durante la distribuzione:

* Il runtime. Attualmente l'unico valore valido per il runtime è Python.
* Script. Questo file (denominato `score.py`) carica il modello all'avvio del servizio distribuito. È anche responsabile della ricezione di dati, passandolo al modello e quindi restituire una risposta.
* Un file di ambiente conda. Questo file definisce i pacchetti Python necessari per eseguire lo script di modello e voce. 

Nell'esempio seguente mostra una configurazione di base inferenza usando Python SDK:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

Per altre informazioni, vedere la [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) riferimento.

L'interfaccia della riga di comando Carica la configurazione di inferenza dei tipi da un file YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Per altre informazioni sulla configurazione di inferenza dei tipi, vedere [distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Script di ingresso

Lo script di ingresso ha solo due funzioni necessarie `init()` e `run(data)`. Queste funzioni vengono usate per inizializzare il servizio all'avvio ed eseguire il modello usando i dati della richiesta passati da un client. Il resto dello script gestisce il caricamento e l'esecuzione dei modelli.

> [!IMPORTANT]
> Non è disponibile uno script di ingresso generico che funziona per tutti i modelli. È sempre specifico per il modello utilizzato. È necessario comprendere come caricare il modello, il formato di dati che si aspetta che il modello e su come assegnare un punteggio con il modello di dati.

Il codice Python seguente è un esempio di script di ingresso (`score.py`):

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

Per altre informazioni sugli script di ingresso, vedere [distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Ambiente conda

Il codice YAML seguente vengono descritti l'ambiente conda necessario per eseguire lo script di modello e voce:

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

Per altre informazioni, vedere [distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definire la distribuzione

Il [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) pacchetto contiene le classi utilizzate per la distribuzione. La classe utilizzata determina dove viene distribuito il modello. Ad esempio, per distribuire come servizio web in Azure Kubernetes Service, usare [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) per creare la configurazione della distribuzione.

Il codice Python seguente definisce una configurazione di distribuzione per una distribuzione locale. Questa configurazione consente di distribuire il modello come servizio web nel computer locale.

> [!IMPORTANT]
> Una distribuzione locale richiede un'installazione funzionante di [Docker](https://www.docker.com/) nel computer locale:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Per altre informazioni, vedere la [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) riferimento.

L'interfaccia della riga di comando Carica la configurazione della distribuzione da un file YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

La distribuzione in una destinazione di calcolo diversi, ad esempio Azure Kubernetes Service nel cloud di Azure, è facile come modificare la configurazione della distribuzione. Per altre informazioni, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Distribuire il modello

Nell'esempio seguente carica le informazioni sul modello registrato denominato `sentiment`e quindi di distribuirla come servizio denominato `sentiment`. Durante la distribuzione, la configurazione di inferenza e configurazione della distribuzione vengono utilizzati per creare e configurare l'ambiente del servizio:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Per altre informazioni, vedere la [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) riferimento.

Per distribuire il modello dal comando, usare il comando seguente. Questo comando consente di distribuire la versione 1 del modello registrato (`sentiment:1`) usando la configurazione di distribuzione e l'inferenza archiviata nel `inferenceConfig.json` e `deploymentConfig.json` file:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Per altre informazioni, vedere la [Distribuisci modello ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) riferimento.

Per altre informazioni sulla distribuzione, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Consumo di richiesta-risposta

Dopo la distribuzione, viene visualizzato l'URI di assegnazione dei punteggi. Questo URI può essere utilizzato dai client per inviare richieste al servizio. L'esempio seguente è un client Python di base che invia dati al servizio e Visualizza la risposta:

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

Per altre informazioni su come usare il servizio distribuito, vedere [creare un client](how-to-consume-web-service.md).

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Come creare un client per un modello distribuito](how-to-consume-web-service.md)