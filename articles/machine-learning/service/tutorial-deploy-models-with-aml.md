---
title: 'Esercitazione sulla classificazione di immagini: Distribuire i modelli'
titleSuffix: Azure Machine Learning service
description: Questa esercitazione mostra come usare il servizio Azure Machine Learning per distribuire un modello di classificazione delle immagini con scikit-learn in un notebook Jupyter per Python. Questa esercitazione è la seconda di una serie in due parti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 01/29/2019
ms.custom: seodec18
ms.openlocfilehash: 0f596f40cdea095ea152785e656c44eaa062e28c
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564035"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Esercitazione: Distribuire un modello di classificazione delle immagini in Istanze di Azure Container

Questa esercitazione è la **seconda di una serie in due parti**. Nell'[esercitazione precedente](tutorial-train-models-with-aml.md), è stato eseguito il training di modelli di machine learning e quindi registrato un modello nell'area di lavoro sul cloud.  

Si è ora pronti per distribuire il modello come servizio Web in [Istanze di Azure Container](https://docs.microsoft.com/azure/container-instances/). Un servizio Web è un'immagine, in questo caso un'immagine Docker. Incapsula la logica di assegnazione dei punteggi e il modello stesso. 

In questa parte dell'esercitazione si usa il servizio Azure Machine Learning per le attività seguenti:

> [!div class="checklist"]
> * Configurare l'ambiente di test.
> * Recuperare il modello dall'area di lavoro.
> * Testare il modello in locale.
> * Distribuire il modello in Istanze di Container.
> * Testare il modello distribuito.

Istanze di Container è un'ottima soluzione per testare e comprendere il flusso di lavoro. Per le distribuzioni di produzione scalabili, è consigliabile usare il servizio Azure Kubernetes. Per altre informazioni, vedere l'articolo su [come e dove eseguire la distribuzione](how-to-deploy-and-where.md).

>[!NOTE]
> Il codice di questo articolo è stato testato con Azure Machine Learning SDK versione 1.0.8.

## <a name="prerequisites"></a>Prerequisiti
Andare alla sezione [Configurare l'ambiente di sviluppo](#start) per leggere la procedura relativa al notebook.  

Per eseguire il notebook, completare innanzitutto il training del modello in [Esercitazione (parte 1): Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md).   Quindi eseguire il notebook **tutorials/img-classification-part2-deploy.ipynb** usando lo stesso server notebook.


## <a name="start"></a>Configurare l'ambiente

Iniziare configurando un ambiente di test.

### <a name="import-packages"></a>Importare pacchetti

Importare i pacchetti Python necessari per questa esercitazione:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Recuperare il modello

Un modello è già stato registrato nell'area di lavoro nell'esercitazione precedente. Caricare ora questa area di lavoro e scaricare il modello nella directory locale:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os 
ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Testare il modello in locale

Prima della distribuzione, assicurarsi che il modello funzioni in locale:
* Caricare i dati di test.
* Stimare i dati di test.
* Esaminare la matrice di confusione.

### <a name="load-test-data"></a>Dati del test di carico

Caricare i dati di test dalla directory **./data/** creata durante l'esercitazione del training:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Stimare i dati di test

Per ottenere le previsioni, inserire il set di dati di test nel modello:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load( os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Esaminare la matrice di confusione

Generare una matrice di confusione per visualizzare quanti campioni dal set di test sono classificati correttamente. Rilevare il valore classificato erroneamente per le stime non corrette: 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

L'output mostra la matrice di confusione:

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
   

Usare `matplotlib` per visualizzare la matrice di confusione sotto forma di grafico. In questo grafico, l'asse x mostra i valori effettivi e l'asse y mostra i valori stimati. Il colore in ogni griglia mostra la percentuale di errore. Più chiaro è il colore, maggiore sarà la frequenza degli errori. Ad esempio, molti 5 sono erroneamente classificati come 3. Viene quindi visualizzata una griglia luminosa in corrispondenza di (5,3):

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
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

## <a name="deploy-as-a-web-service"></a>Distribuire come servizio Web

Dopo aver testato il modello e aver ottenuto risultati soddisfacenti, distribuire il modello come servizio Web ospitato in Istanze di Container. 

Per creare l'ambiente corretto per Istanze di Container, fornire i componenti seguenti:
* Uno script di assegnazione dei punteggi per mostrare come usare il modello.
* Un file di ambiente per mostrare quali pacchetti devono essere installati.
* Un file di configurazione per creare l'istanza di contenitore.
* Il modello su cui è stato eseguito il training in precedenza.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Creare lo script di assegnazione dei punteggi

Creare lo script di assegnazione dei punteggi con il nome **score.py**. Questo script viene usato dalla chiamata al servizio Web per mostrare come usare il modello.

Includere queste due funzioni obbligatorie nello script di assegnazione dei punteggi:
* La funzione `init()` che carica in genere il modello in un oggetto globale. Questa funzione viene eseguita una sola volta all'avvio del contenitore Docker. 

* La funzione `run(input_data)` usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON, ma sono supportati anche altri formati.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Creare un file di ambiente

Successivamente, creare un file di ambiente, con il nome **myenv.yml**, che specifica tutte le dipendenze del pacchetto dello script. Questo file viene usato per assicurarsi che nell'immagine Docker siano installate tutte le dipendenze. Questo modello richiede `scikit-learn` e `azureml-sdk`:

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Rivedere il contenuto del file `myenv.yml`:

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Creare un file di configurazione

Creare un file di configurazione della distribuzione. Specificare il numero di CPU e gigabyte di RAM necessari per il contenitore di Istanze di Container. Anche se dipende dal modello, l'impostazione predefinita è di un core e 1 GB di RAM ed è in genere sufficiente per molti modelli. Se in un secondo momento si ha bisogno di maggiore capacità, ricreare l'immagine e ridistribuire il servizio.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Distribuire in Istanze di Container
Il tempo stimato per completare la distribuzione è di **circa sette o otto minuti**.

Configurare l'immagine e distribuire. Il codice seguente esegue questi passaggi:

1. Creare un'immagine usando questi file:
   * Il file di assegnazione dei punteggi, `score.py`.
   * Il file di ambiente, `myenv.yml`.
   * file di modello.
1. Registrare l'immagine nell'area di lavoro. 
1. Inviare l'immagine nel contenitore di Istanze di Container.
1. Avviare un contenitore in Istanze di Container usando l'immagine.
1. Ottenere l'endpoint HTTP del servizio Web.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Ottenere l'endpoint HTTP del servizio Web di assegnazione dei punteggi, che accetta le chiamate del client REST. È possibile condividere questo endpoint con tutti coloro che vogliono testare il servizio Web o integrarlo in un'applicazione: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Testare il servizio distribuito

In precedenza è stato assegnato un punteggio a tutti i dati di test con la versione locale del modello. È ora possibile testare il modello distribuito con un campione casuale di 30 immagini ottenute dai dati di test.  

Il codice seguente esegue questi passaggi:
1. Inviare i dati come matrice JSON per il servizio Web ospitato in Istanze di Container. 

1. Usare l'API `run` di SDK per chiamare il servizio. È anche possibile eseguire chiamate non elaborate usando qualsiasi strumento HTTP, ad esempio **curl**.

1. Stampare le stime restituite e tracciarle con le immagini di input. Per evidenziare gli esempi di classificazioni non corrette vengono usati un carattere rosso e un'immagine con colori invertiti (bianco su sfondo nero). 

Poiché l'accuratezza del modello è elevata, può essere necessario eseguire il codice seguente più volte prima di poter vedere un esempio di classificazione non corretta:

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
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Questo risultato è stato ricavato da un campione casuale di immagini di test:

![Immagine che mostra i risultati](./media/tutorial-deploy-models-with-aml/results.png)

È anche possibile inviare una richiesta HTTP non elaborata per testare il servizio Web:

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

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

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Passaggi successivi

+ Informazioni su tutte le [opzioni di distribuzione per il servizio Azure Machine Learning](how-to-deploy-and-where.md).
+ Informazioni su come [creare i client per il servizio Web](how-to-consume-web-service.md).
+  [Eseguire stime su grandi quantità di dati](how-to-run-batch-predictions.md) in modo asincrono.
+ Monitorare i modelli di Azure Machine Learning con [Application Insights](how-to-enable-app-insights.md).
+ Provare l'esercitazione relativa alla [selezione automatica degli algoritmi](tutorial-auto-train-models.md). 
