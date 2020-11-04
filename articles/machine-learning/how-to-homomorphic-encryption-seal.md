---
title: Distribuire un servizio di inferenza crittografato (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Microsoft SEAL per distribuire un servizio di stima crittografato per la classificazione delle immagini
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 3509530994b07a16fb1f2780fffc6fd27cf8aa7c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325521"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Come distribuire un servizio Web di inferenza crittografato (anteprima)

Questa guida illustra come distribuire un modello di classificazione delle immagini come servizio Web di inferenza crittografato in [Istanze di Azure Container](../container-instances/index.yml). Il servizio Web è un'immagine del contenitore Docker che contiene il modello e la logica di assegnazione del punteggio.

In questa guida si usa il servizio Azure Machine Learning per:

> [!div class="checklist"]
> * Configurare gli ambienti
> * Distribuire il servizio Web di inferenza crittografato
> * Preparare i dati di test
> * Eseguire stime crittografate
> * Pulire le risorse

Istanze di Container è un'ottima soluzione per testare e comprendere il flusso di lavoro di distribuzione modello. Per le distribuzioni di produzione scalabili, è consigliabile usare il servizio Azure Kubernetes. Per altre informazioni, vedere l'articolo su [come e dove eseguire la distribuzione](./how-to-deploy-and-where.md).

Il metodo di crittografia usato in questo esempio è la [crittografia omomorfica](https://github.com/Microsoft/SEAL#homomorphic-encryption). La crittografia omomorfica consente di eseguire calcoli su dati crittografati senza richiedere l'accesso a una chiave privata o di decrittografia. I risultati dei calcoli sono crittografati e possono essere resi noti solo dal proprietario della chiave privata. 

## <a name="prerequisites"></a>Prerequisiti

Questa guida presuppone che si abbia un modello di classificazione delle immagini registrato in Azure Machine Learning. In caso contrario, registrare il modello usando un [modello predefinito](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) o crearne uno personalizzato completando l'esercitazione [Eseguire il training di modelli di classificazione delle immagini con dati MNIST e scikit-learn](tutorial-train-models-with-aml.md).

## <a name="configure-local-environment"></a>Configurare l'ambiente locale

In un notebook di Jupyter

1. Importare i pacchetti Python necessari per questo esempio.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Installare la libreria di crittografia omomorfica per l'inferenza sicura.

    > [!NOTE]
    > Il pacchetto `encrypted-inference` è attualmente in anteprima.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) è una libreria che contiene i binding per l'inferenza crittografata basata su [Microsoft SEAL](https://github.com/Microsoft/SEAL).

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>Configurare l'ambiente di inferenza

Creare un ambiente per l'inferenza e aggiungere il pacchetto `encrypted-inference` come dipendenza conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Distribuire il servizio Web di inferenza crittografato

Distribuire il modello come servizio Web ospitato in ACI.

Per compilare l'ambiente corretto per l'ACI, fornire i seguenti elementi:

* Uno script di assegnazione dei punteggi per mostrare come usare il modello
* Un file di configurazione per compilare l'ACI
* Un modello con training

### <a name="create-scoring-script"></a>Creare lo script di assegnazione dei punteggi

Creare lo script di punteggio `score.py` usato dal servizio Web per l'inferenza.

È necessario includere due funzioni necessarie nello script di assegnazione dei punteggi:

* La funzione `init()` che carica in genere il modello in un oggetto globale. Questa funzione viene eseguita una sola volta all'avvio del contenitore Docker.
* La funzione `run(input_data)` usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON, ma sono supportati anche altri formati. La funzione recupera le chiavi pubbliche basate sulla crittografia omomorfica caricate dal chiamante del servizio.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Creare il file di configurazione

Configurare il file di configurazione della distribuzione e specificare il numero di CPU e gigabyte di RAM necessari per il contenitore ACI. Anche se dipende dal modello, l'impostazione predefinita è di 1 core e 1 GB di RAM ed è in genere sufficiente per molti modelli. Se si ritiene di averne bisogno di aggiuntivi in seguito, sarà necessario ricreare l'immagine e ridistribuire il servizio.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Eseguire la distribuzione in Istanze di Azure Container

Tempo previsto per il completamento: **circa 2-5 minuti**

Configurare l'immagine e distribuire. Il codice seguente esegue questi passaggi:

1. Creare un oggetto ambiente contenente le dipendenze necessarie per il modello usando il file di ambiente (`myenv.yml`)
1. Creare la configurazione dell'inferenza necessaria per distribuire il modello come servizio Web usando:
   * Il file di assegnazione dei punteggi (`score.py`)
   * L'oggetto ambiente creato nel passaggio precedente
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

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Ottenere l'endpoint HTTP del servizio Web di assegnazione dei punteggi, che accetta le chiamate del client REST. Questo endpoint può essere condiviso con tutti coloro che desiderano testare il servizio Web oppure integrarlo in un'applicazione.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Preparare i dati di test

1. Scaricare i dati di test. In questo caso sono salvati in una directory denominata *data*.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Caricare i dati di test nella directory *data*.

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Eseguire stime crittografate

Usare il set di dati di test con il modello per ottenere le stime.

Per ottenere stime crittografate:

1. Creare un nuovo `EILinearRegressionClient`, un client basato sulla crittografia omomorfica, e le chiavi pubbliche.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Caricare le chiavi pubbliche generate dalla crittografia omomorfica nell'archivio BLOB predefinito dell'area di lavoro. In questo modo si potranno condividere le chiavi con il server di inferenza.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. Crittografare i dati di test

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. Usare l'API `run` dell'SDK per richiamare il servizio e fornire il set di dati di test al modello per ottenere le stime. Sarà necessario inviare la stringa di connessione all'archivio BLOB in cui sono state caricate le chiavi pubbliche.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Usare il client per decrittografare i risultati.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il servizio Web creato in questo esempio:

```python
service.delete()
```

Se non si prevede di usare ancora le risorse di Azure create, eliminarle. In questo modo si evita di dover pagare per risorse inutilizzate ancora in esecuzione. Per altre informazioni su come pulire le risorse, vedere [questa guida](how-to-manage-workspace.md#clean-up-resources).