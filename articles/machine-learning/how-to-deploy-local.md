---
title: Come eseguire e distribuire localmente
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire i modelli sottoposti a training nel computer locale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: fcea7c162e5c978a7c8ff1b42e09ffe0afb98f3c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550097"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>Eseguire la distribuzione nei modelli di computer locali con training con Azure Machine Learning

Questo articolo illustra come usare il computer locale come destinazione per il training o la distribuzione di modelli creati in Azure Machine Learning. La flessibilità di Azure Machine Learning consente di lavorare con la maggior parte dei framework di Machine Learning Python. Le soluzioni di Machine Learning presentano in genere dipendenze complesse che possono essere difficili da duplicare. In questo articolo viene illustrato come ridurre il controllo totale rispetto alla semplicità d'uso.

Di seguito sono riportati alcuni scenari per la distribuzione locale:

* Iterazione rapida di dati, script e modelli all'inizio di un progetto
* Debug e risoluzione dei problemi nelle fasi successive
* Distribuzione finale nell'hardware gestito dall'utente

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md)
- Un modello e un ambiente. Se non si dispone di un modello sottoposto a training, è possibile utilizzare i file di modello e di dipendenza forniti in [questa esercitazione](tutorial-train-models-with-aml.md)
- [Software Development Kit di Azure Machine Learning (SDK) per Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
- Una gestione conda, ad esempio Anaconda o miniconda, se si desidera eseguire il mirroring delle dipendenze dei pacchetti di Azure Machine Learning
- Docker, se si vuole usare una versione in contenitori dell'ambiente Azure Machine Learning

## <a name="prepare-your-local-machine"></a>Preparare il computer locale

Il modo più affidabile per eseguire localmente un modello di Azure Machine Learning è con un'immagine docker. Un'immagine Docker fornisce un'esperienza isolata e in contenitori che duplica, ad eccezione dei problemi hardware, dell'ambiente di esecuzione di Azure. Per altre informazioni sull'installazione e la configurazione di Docker per gli scenari di sviluppo, vedere [Panoramica dello sviluppo remoto Docker in Windows](/windows/dev-environment/docker/overview).

Sebbene sia possibile collegare un debugger a un processo in esecuzione in Docker (vedere [connettersi a un contenitore in esecuzione](https://code.visualstudio.com/docs/remote/attach-container)), è preferibile eseguire il debug e l'iterazione del codice Python senza coinvolgere docker. In questo scenario è importante che il computer locale usi le stesse librerie usate quando si esegue l'esperimento in Azure Machine Learning. Per gestire le dipendenze di Python, Azure usa [conda](https://docs.conda.io/). Sebbene sia possibile ricreare l'ambiente utilizzando altri gestori di pacchetti, l'installazione e la configurazione di conda nel computer locale è il modo più semplice per eseguire la sincronizzazione. 

## <a name="prepare-your-entry-script"></a>Preparare lo script di immissione

Anche se si usa Docker per gestire il modello e le dipendenze, lo script di assegnazione dei punteggi Python deve essere locale. Lo script deve disporre di due metodi:

- `init()`Metodo che non accetta argomenti e non restituisce alcun valore 
- `run()`Metodo che accetta una stringa in formato JSON e restituisce un oggetto serializzabile in JSON.

L'argomento del `run()` metodo avrà il formato seguente: 

```json
{
    "data": <model-specific-data-structure>
}
```

L'oggetto restituito dal `run()` metodo deve implementare `toJSON() -> string` .

Nell'esempio seguente viene illustrato come caricare un modello Scikit-learn registrato e assegnare un punteggio con i dati numpy. Questo esempio è basato sul modello e sulle dipendenze di [questa esercitazione](tutorial-train-models-with-aml.md):

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

Per esempi più avanzati, tra cui la generazione automatica dello schema di spavalderia e il Punteggio dei dati binari (ad esempio, immagini), vedere [creazione di script di immissione avanzati](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-using-docker"></a>Distribuire come servizio Web locale con Docker

Il modo più semplice per replicare l'ambiente usato da Azure Machine Learning consiste nel distribuire un servizio Web con Docker. Con Docker in esecuzione nel computer locale, sarà necessario:

1. Connettersi all'area di lavoro Azure Machine Learning in cui è registrato il modello
1. Creare un `Model` oggetto che rappresenta il modello
1. Creare un `Environment` oggetto che contiene le dipendenze e definisce l'ambiente software in cui il codice viene eseguito
1. Creare un `InferenceConfig` oggetto che associ lo script di ingresso e il `Environment`
1. Creare un `DeploymentConfiguration` oggetto della sottoclasse `LocalWebserviceDeploymentConfiguration`
1. Utilizzare `Model.deploy()` per creare un `Webservice` oggetto. Questo metodo Scarica l'immagine Docker e la associa a `Model` , `InferenceConfig` e `DeploymentConfiguration`
1. Attivare `Webservice` con `Webservice.wait_for_deployment()`

Il codice seguente illustra questi passaggi:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

La chiamata a `Model.deploy()` può richiedere alcuni minuti. Dopo aver distribuito inizialmente, è più efficiente usare il `update()` metodo anziché iniziare da zero. Vedere [aggiornare un servizio Web distribuito](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Testare la distribuzione locale

Quando si esegue lo script di distribuzione precedente, viene restituito l'URI a cui è possibile inviare i dati per il Punteggio (ad esempio, `http://localhost:6789/score` ). Nell'esempio seguente viene illustrato uno script che consente di punteggiare i dati di esempio con il `"sklearn-mnist-local"` modello distribuito localmente. Il modello, se correttamente sottoposto a training, deduce che `normalized_pixel_values` deve essere interpretato come "2". 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Scarica ed Esegui direttamente il tuo modello

Quando si usa Docker per distribuire il modello come servizio Web è l'opzione più comune, è anche possibile eseguire il codice direttamente con gli script Python locali. Sono necessari due elementi importanti: 

- Il modello stesso
- Dipendenze su cui si basa il modello 

Il download del modello può essere eseguito:  

- Dal portale scegliere la scheda **modelli** , selezionare il modello desiderato e nella pagina dei **Dettagli** scegliere **Scarica** .
- Dalla riga di comando, usando `az ml model download` (vedere il [riferimento al download del modello](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false))
- Con Python SDK, usando il `Model.download()` Metodo (vedere le informazioni di [riferimento sull'API del modello](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false))

Un modello di Azure è uno o più oggetti Python serializzati, inclusi in un pacchetto di file Python Pickle (estensione **PKL** ). Il contenuto del file pickle dipende dalla libreria ML o dalla tecnica utilizzata per eseguire il training del modello. Ad esempio, con il modello dell'esercitazione, è possibile caricare il modello con:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Le dipendenze sono sempre difficili da ottenere, soprattutto con machine learning, in cui spesso è possibile che si verifichino i requisiti di versione specifici. È possibile ricreare un ambiente di Azure Machine Learning nel computer locale come ambiente conda completo o come immagine Docker usando il `Environment` metodo della classe `build_local()` . 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda env
```

Se si imposta `build_local()` l'argomento di `useDocker` su `True` , la funzione creerà un'immagine Docker anziché un ambiente conda. Se si desidera un maggiore controllo, è possibile utilizzare `Environment` il `save_to_directory()` metodo, che scrive **conda_dependencies. yml** e **azureml_environment.jsnei** file di definizione che è possibile ottimizzare e utilizzare come base per l'estensione. 

La `Environment` classe ha diversi altri metodi per sincronizzare gli ambienti tra l'hardware di calcolo, l'area di lavoro di Azure e le immagini docker. Per ulteriori informazioni, vedere le informazioni di [ `Environment` riferimento sulle API](/python/api/azureml-core/azureml.core.environment(class)).

Dopo aver scaricato il modello e risolto le relative dipendenze, non sono previste restrizioni definite da Azure per la modalità di assegnazione dei punteggi, l'ottimizzazione del modello, l'uso del trasferimento di formazione e così via. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Caricare un modello di cui è stato ripetuto il training per Azure Machine Learning

Se si dispone di un modello sottoposto a training o di cui è stato eseguito il training in locale, è possibile registrarlo con Azure. Una volta effettuata la registrazione, è possibile continuare a ottimizzarla usando calcolo di Azure o distribuirla usando le funzionalità di Azure, ad esempio il [servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) o il [server di inferenza Triton (anteprima)](how-to-deploy-with-triton.md).

Per essere usato con Python SDK di Azure Machine Learning, un modello deve essere archiviato come oggetto Python serializzato in formato Pickle (un file **PKL** ) e deve implementare un `predict(data)` metodo che restituisce un oggetto serializzabile in JSON. Ad esempio, è possibile archiviare un modello di diabete Scikit-learn con training locale con: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Per rendere il modello disponibile in Azure, è possibile usare il `register()` metodo della `Model` classe:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

È quindi possibile trovare il modello appena registrato nella scheda **modello** di Azure Machine Learning:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Screenshot della scheda modello di Azure Machine Learning, che mostra il modello caricato.":::

Per altre informazioni sul caricamento e l'aggiornamento di modelli e ambienti, vedere [registrare il modello e distribuire localmente con utilizzi avanzati](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla gestione degli ambienti, vedere [creare & usare gli ambienti software in Azure Machine Learning](how-to-use-environments.md)
- Per informazioni sull'accesso ai dati dall'archivio dati, vedere [connettersi ai servizi di archiviazione in Azure](how-to-access-data.md) .