---
title: 'Esercitazione sulla classificazione di immagini: Distribuire i modelli'
titleSuffix: Azure Machine Learning
description: Questa esercitazione, la seconda di una serie in due parti, mostra come usare Azure Machine Learning per distribuire un modello di classificazione delle immagini con scikit-learn in un notebook Jupyter per Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 680b6ec17b65cd9452dd3bd5c0c470e395688cb8
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86025676"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Esercitazione: Distribuire un modello di classificazione delle immagini in Istanze di Azure Container
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questa esercitazione è la **seconda di una serie in due parti**. Nell'[esercitazione precedente](tutorial-train-models-with-aml.md), è stato eseguito il training di modelli di machine learning e quindi registrato un modello nell'area di lavoro sul cloud.  A questo punto, si è pronti per distribuire il modello come servizio Web. Un servizio Web è un'immagine, in questo caso un'immagine Docker. Incapsula la logica di assegnazione dei punteggi e il modello stesso. 

In questa parte dell'esercitazione si usa Azure Machine Learning per le attività seguenti:

> [!div class="checklist"]
> * Configurare l'ambiente di test.
> * Recuperare il modello dall'area di lavoro.
> * Distribuire il modello in Istanze di Container.
> * Testare il modello distribuito.

Istanze di Container è un'ottima soluzione per testare e comprendere il flusso di lavoro. Per le distribuzioni di produzione scalabili, è consigliabile usare il servizio Azure Kubernetes. Per altre informazioni, vedere l'articolo su [come e dove eseguire la distribuzione](how-to-deploy-and-where.md).

>[!NOTE]
> Il codice di questo articolo è stato testato con Azure Machine Learning SDK versione 1.0.83.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire il notebook, completare innanzitutto il training del modello in [Esercitazione (parte 1): Eseguire il training di un modello di classificazione delle immagini](tutorial-train-models-with-aml.md).   Aprire quindi il notebook *img-classification-part2-deploy.ipynb* nella cartella *tutorials/image-classification-mnist-data* clonata.

Questa esercitazione è disponibile anche in [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se si vuole usarla nel proprio [ambiente locale](how-to-configure-environment.md#local).  Verificare di aver installato `matplotlib` e `scikit-learn` nell'ambiente. 

> [!Important]
> Il resto di questo articolo contiene lo stesso contenuto visualizzato nel notebook.  
>
> Passare ora al notebook di Jupyter se si desidera leggere durante l'esecuzione del codice.
> Per eseguire una singola cella di codice in un notebook, fare clic sulla cella di codice e premere **MAIUSC + INVIO**. In alternativa, eseguire l'intero notebook scegliendo **Esegui tutto** dalla barra degli strumenti superiore.

## <a name="set-up-the-environment"></a><a name="start"></a>Configurare l'ambiente

Iniziare configurando un ambiente di test.

### <a name="import-packages"></a>Importare pacchetti

Importare i pacchetti Python necessari per questa esercitazione.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Distribuire come servizio Web

Distribuire il modello come servizio Web ospitato in ACI. 

Per compilare l'ambiente corretto per l'ACI, fornire i seguenti elementi:
* Uno script di assegnazione dei punteggi per mostrare come usare il modello
* Un file di configurazione per compilare l'ACI
* Il modello su cui è stato eseguito il training precedentemente

### <a name="create-scoring-script"></a>Creare lo script di assegnazione dei punteggi

Creare lo script di assegnazione dei punteggi, chiamato score.py, usato per la chiamata dal servizio Web per mostrare come usare il modello.

È necessario includere due funzioni necessarie nello script di assegnazione dei punteggi:
* La funzione `init()` che carica in genere il modello in un oggetto globale. Questa funzione viene eseguita una sola volta all'avvio del contenitore Docker. 

* La funzione `run(input_data)` usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON, ma sono supportati anche altri formati.

```python
%%writefile score.py
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

### <a name="create-configuration-file"></a>Creare il file di configurazione

Configurare il file di configurazione della distribuzione e specificare il numero di CPU e gigabyte di RAM necessari per il contenitore ACI. Anche se dipende dal modello, l'impostazione predefinita è di 1 core e 1 GB di RAM ed è in genere sufficiente per molti modelli. Se si ritiene di averne bisogno di aggiuntivi in seguito, sarà necessario ricreare l'immagine e ridistribuire il servizio.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Distribuire in ACI
Tempo previsto per il completamento: **circa 2-5 minuti**

Configurare l'immagine e distribuire. Il codice seguente esegue questi passaggi:

1. Creare un oggetto ambiente contenente le dipendenze necessarie per il modello usando l'ambiente (`tutorial-env`) salvato durante il training.
1. Creare la configurazione dell'inferenza necessaria per distribuire il modello come servizio Web usando:
   * Il file di assegnazione dei punteggi (`score.py`)
   * Oggetto ambiente creato nel passaggio precedente
1. Distribuire il modello nel contenitore ACI.
1. Ottenere l'endpoint HTTP del servizio Web.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Ottenere l'endpoint HTTP del servizio Web di assegnazione dei punteggi, che accetta le chiamate del client REST. Questo endpoint può essere condiviso con tutti coloro che desiderano testare il servizio Web oppure integrarlo in un'applicazione.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>Testare un modello


### <a name="download-test-data"></a>Scaricare i dati di test
Scaricare i dati di test nella directory **./data/**


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Dati del test di carico

Caricare i dati di test dalla directory **./data/** creata durante l'esercitazione del training.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>Stimare i dati di test

Inserire il set di dati del test nel modello per ottenere le previsioni.


Il codice seguente esegue questi passaggi:
1. Inviare i dati come matrice JSON per il servizio Web ospitato in ACI. 

1. Usare l'API `run` di SDK per chiamare il servizio. È anche possibile effettuare chiamate non elaborate usando qualsiasi strumento HTTP, ad esempio curl.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>Esaminare la matrice di confusione

Generare una matrice di confusione per visualizzare quanti campioni dal set di test sono classificati correttamente. Rilevare il valore classificato erroneamente per le stime non corrette.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

L'output mostra la matrice di confusione:

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

Usare `matplotlib` per visualizzare la matrice di confusione sotto forma di grafico. In questo grafico, l'asse X rappresenta i valori effettivi e l'asse Y rappresenta i valori stimati. Il colore in ogni griglia rappresenta la percentuale di errore. Più chiaro è il colore, maggiore sarà la frequenza degli errori. Ad esempio, molti 5 sono erroneamente classificati come 3. Viene quindi visualizzata una griglia luminosa in corrispondenza di (5,3).

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Grafico che mostra la matrice di confusione](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>Mostrare le stime

Testare il modello distribuito con un campione casuale di 30 immagini ottenute dai dati di test.  


1. Stampare le stime restituite e tracciarle con le immagini di input. Viene usato un tipo di carattere rosso e immagine inversa (bianco su sfondo nero)per evidenziare gli esempi di classificazioni non corrette. 

 Poiché l'accuratezza del modello è elevata, potrebbe essere necessario eseguire il codice seguente più volte prima di poter vedere un esempio di classificazioni non corrette.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

È anche possibile inviare una richiesta HTTP non elaborata per testare il servizio Web.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per mantenere il gruppo di risorse e l'area di lavoro per altre esercitazioni ed esplorazioni, è possibile eliminare solo la distribuzione di Istanze di Container tramite questa chiamata API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Passaggi successivi

+ Informazioni su tutte le [opzioni di distribuzione di Azure Machine Learning](how-to-deploy-and-where.md).
+ Informazioni su come [creare i client per il servizio Web](how-to-consume-web-service.md).
+  [Eseguire stime su grandi quantità di dati](how-to-use-parallel-run-step.md) in modo asincrono.
+ Monitorare i modelli di Azure Machine Learning con [Application Insights](how-to-enable-app-insights.md).
+ Provare l'esercitazione relativa alla [selezione automatica degli algoritmi](tutorial-auto-train-models.md). 
