---
title: Distribuire i servizi Web nelle Istanze di contenitore di Azure (ACI) - Azure Machine Learning
description: Informazioni su come distribuire un modello con training come servizio Web nelle Istanze di contenitore di Azure (ACI) con il servizio di Azure Machine Learning. Questo articolo illustra tre diversi modi per distribuire un modello su ACI. Si differenziano nel numero di righe di codice e di controllo nelle parti di denominazione della distribuzione.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: b004abb3959bbfe36fc200bf762114f88f3d2ead
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345047"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Distribuire servizi Web nelle Istanze di contenitore di Azure 

È possibile distribuire il modello di training come servizio Web in [Istanze di contenitore di Azure](https://azure.microsoft.com/services/container-instances/) (ACI), [servizio Kubernetes di Azure](https://azure.microsoft.com/services/kubernetes-service/) (AKS), dispositivi IoT edge, o [FPGA (field programmable gate arrays)](concept-accelerate-with-fpgas.md) 

ACI è in genere più conveniente rispetto al servizio Kubernetes di Azure e può essere impostato in 4-6 righe di codice. ACI è l'opzione ideale per le distribuzioni dei test. Successivamente, quando si è pronti a usare i modelli e i servizi Web per scopi di produzione su vasta scala, è possibile [distribuirli in AKS](how-to-deploy-to-aks.md).

Questo articolo illustra tre diversi modi per distribuire un modello su ACI. Si differenziano nel numero di righe di codice e di controllo nelle parti di denominazione della distribuzione. Dal metodo con la quantità minima di codice e controllo al metodo con la maggior parte di codice e controllo, le opzioni ACI sono:

* Distribuire da file di modello tramite `Webservice.deploy()` 
* Distribuire da modelli registrati tramite `Webservice.deploy_from_model()`
* Distribuire il modello registrato dall'immagine tramite `Webservice.deploy_from_image()`

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro del servizio di Azure Machine Learning e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando la [Guida introduttiva di Azure Machine Learning](quickstart-get-started.md).

- Oggetto dell'area di lavoro del servizio di Azure Machine Learning

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- Un modello da distribuire. Gli esempi in questo documento usano il modello creato quando si seguono le esercitazioni "[Eseguire il training di un modello](tutorial-train-models-with-aml.md)". Se non si utilizza questo modello, modificare i passaggi per fare riferimento al proprio nome di modello.  È inoltre necessario scrivere lo script di assegnazione dei punteggi per l'esecuzione del modello.


## <a name="configure-an-image"></a>Configurare un'immagine

Configurare l'immagine Docker che consente di archiviare tutti i file di modello.
1. Creare uno script di assegnazione dei punteggi (score.py) [usando queste istruzioni](tutorial-deploy-models-with-aml.md#create-scoring-script).

    > [!IMPORTANT]
    > Lo script di assegnazione dei punteggi riceve i dati inviati dai client e li passa al modello per l'assegnazione dei punteggi. Documentare la struttura dei data prevista dallo script e dal modello. Questa documentazione semplifica alcune operazioni durante la compilazione di un client che usa il servizio web.

1. Creare un file di ambiente (myenv.yml) [usando queste istruzioni](tutorial-deploy-models-with-aml.md#create-environment-file).

1. Usare questi due file per configurare l'immagine Docker in Python usando il SDK nel modo seguente:

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>Configurare il contenitore ACI

Configurare il contenitore ACI specificando il numero di CPU e gigabyte di RAM necessari per il contenitore ACI. Il valore predefinito di un core e 1 GB di RAM sono sufficienti per molti modelli. Se si ritiene di averne bisogno di aggiuntivi, ricreare l'immagine e ridistribuire il servizio.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>Registrare un modello

> Ignorare questo prerequisito se si [distribuisce da un file di modello](#deploy-from-model-file) (`Webservice.deploy()`).

Registrare un modello per usare [Webservice.deploy_from_model](#deploy-from-registered-model) o [Webservice.deploy_from_image](#deploy-from-image). O se si dispone già di un modello registrato, recuperarlo adesso.

### <a name="retrieve-a-registered-model"></a>Recuperare un modello registrato
Se Azure Machine Learning è stato usato per il training del modello, questo potrebbe essere già registrato nell'area di lavoro.  L'ultimo passaggio dell'esercitazione [eseguire il training di un modello](tutorial-train-models-with-aml.md) ad esempio ha registrato il modello.  È quindi possibile recuperare il modello registrato per la distribuzione.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>Registrare un file di modello

Se il modello è stato creato in un'altra posizione, è comunque possibile registrarlo nell'area di lavoro.  Per registrare un modello, il file di modello (`sklearn_mnist_model.pkl` in questo esempio) deve trovarsi nella directory di lavoro in uso. Registrare quindi tale file come modello chiamato `sklearn_mnist` nell'area di lavoro con `Model.register()`.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```

<a name='deploy-from-model-file'/>
## <a name="option-1-deploy-from-model-file"></a>Opzione 1: distribuire dal file di modello

L'opzione per distribuire da un file di modello richiede la quantità minima di scrittura del codice, ma offre anche la quantità minima di controllo sulla denominazione dei componenti. Questa opzione inizia con un file di modello e lo registra nell'area di lavoro per l'utente.  Tuttavia, non è possibile denominare il modello o associare i tag o una descrizione.  

Questa opzione usa il metodo SDK, Webservice.deploy().  

**Tempo stimato**: la distribuzione richiede circa 6-7 minuti.

1. Assicurarsi che il file di modello sia nella directory di lavoro locale.

1. Aprire il file di modello dei prerequisiti, score.py e modificare la sezione `init()` in:

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. Distribuire il file di modello.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. È ora possibile [testare il servizio Web](#test-web-service).

<a name='deploy-from-registered-model'/>
## <a name="option-2-deploy-from-registered-model"></a>Opzione 2: distribuire da modello registrato

L'opzione di distribuzione di un file di modello registrato richiede ulteriori righe di codice e consente un buon controllo sulla denominazione degli output. Questa opzione è un modo pratico per distribuire un modello registrato che già si possiede.  Tuttavia, non è possibile assegnare un nome all'immagine Docker.  

Questa opzione usa il metodo SDK, Webservice.deploy_from_model().

**Tempo stimato**: la distribuzione con questa opzione richiede circa 8 minuti.

1. Eseguire il codice per configurare il contenitore Docker e il contenitore ACI e specificare il modello registrato.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. È ora possibile [testare il servizio Web](#test-web-service).

<a name='deploy-from-image'/>
## <a name="option-3-deploy-from-image"></a>Opzione 3: distribuire da immagine

Distribuire un modello registrato (`model`) tramite `Webservice.deploy_from_image()`. Questo metodo consente di creare l'immagine Docker separatamente e quindi distribuire a partire da tale immagine.

1. Compilare e registrare l'immagine Docker sotto l'area di lavoro utilizzando `ContainerImage.create()`

    Questo metodo offre un maggiore controllo sull'immagine, creandola in un passaggio separato.  Il modello registrato (`model`) è incluso nell'immagine.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**Tempo stimato**: circa 3 minuti.

1. Distribuire l'immagine Docker come servizio usando `Webservice.deploy_from_image()`

    Distribuire ora l'immagine ad ACI.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**Tempo stimato**: circa 3 minuti.

Questo metodo offre il massimo controllo sulla creazione e la denominazione dei componenti nella distribuzione.

È ora possibile testare il servizio Web.

## <a name="a-nametest-web-servicetest-the-web-service"></a><a name='test-web-service'/>Testare il servizio Web

Il servizio Web è lo stesso, indipendentemente dal metodo usato.  Per ottenere le stime, usare il metodo `run` del servizio.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende usare questo servizio Web, eliminarlo in modo da non incorrere in alcun addebito.

```python
service.delete()
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [usare un modello di ML distribuito come un servizio Web](how-to-consume-web-service.md).
* Informazioni su come [distribuire nel servizio Kubernetes di Azure](how-to-deploy-to-aks.md) per una distribuzione con maggiore scalabilità. 
