---
title: Guida alla risoluzione dei problemi di distribuzione
titleSuffix: Azure Machine Learning service
description: Informazioni su come risolvere, risolvere e risolvere errori comuni durante la distribuzione Docker con servizio contenitore di AZURE e ACI usando il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 05/02/2018
ms.custom: seodec18
ms.openlocfilehash: 90e85e0030a696dd024dd65d27a0f4dbdc7e3cdc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023666"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Risoluzione dei problemi di distribuzione del servizio Azure Kubernetes e di Istanze di Azure Container con il servizio Azure Machine Learning

Informazioni su come risolvere o a risolvere errori comuni di distribuzione di Docker con Azure Kubernetes Service (AKS) usando il servizio di Azure Machine Learning e le istanze di contenitore di Azure (ACI).

Durante la distribuzione di un modello nel servizio Azure Machine Learning, il sistema esegue una serie di attività. Le attività di distribuzione sono le seguenti:

1. Registrare il modello nel registro dei modelli dell'area di lavoro.

2. Creare un'immagine Docker seguendo questa procedura:
    1. Scaricare il modello registrato dal registro. 
    2. Creare un Dockerfile, con un ambiente Python basato sulle dipendenze specificate nel file YAML di ambiente.
    3. Aggiungere i file di modello e lo script di assegnazione dei punteggi fornito nel Dockerfile.
    4. Creare una nuova immagine Docker usando il Dockerfile.
    5. Registrare l'immagine Docker con il Registro Azure Container associato all'area di lavoro.

    > [!IMPORTANT]
    > A seconda del codice, la creazione dell'immagine eseguita automaticamente senza l'input dell'utente.

3. Distribuire l'immagine Docker al servizio Istanze di Azure Container o al servizio Azure Kubernetes.

4. Avviare uno o più nuovi contenitori in uno di questi due servizi. 

Per altre informazioni su questa procedura, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Prima di iniziare

Se si verifica un problema, la prima cosa da fare è suddividere l'attività di distribuzione (descritta in precedenza) in singoli passaggi per isolare il problema.

La distribuzione di rilievo nelle attività è utile se si usa la [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API, o [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API, come entrambe queste funzioni eseguono i passaggi indicati in precedenza come un singola azione. In genere tali API sono utili, ma è utile per suddividere i passaggi per risolvere il problema sostituendoli con i sotto le chiamate API.

1. Registrare il modello. Ecco del codice di esempio:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Compilare l'immagine. Ecco del codice di esempio:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Distribuire l'immagine come servizio. Ecco del codice di esempio:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Una volta suddiviso il processo di distribuzione in singole attività, è possibile esaminare alcuni degli errori più comuni.

## <a name="image-building-fails"></a>Errore di compilazione dell'immagine

Se non è possibile compilare l'immagine Docker, il [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) oppure [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) chiamata ha esito negativo con alcuni messaggi di errore che possono offrire alcune indicazioni. Anche nel log di compilazione dell'immagine possono essere disponibili altri dettagli sugli errori. Di seguito è riportato del codice di esempio che mostra come individuare l'URI del log di compilazione dell'immagine.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

L'URI del log è un URL SAS che punta a un file di log archiviato nell'Archivio BLOB di Azure. Basta copiare e incollare l'URI in una finestra del browser per poter scaricare e visualizzare il file di log.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>I criteri di accesso di Azure Key Vault e i modelli di Azure Resource Manager

La compilazione dell'immagine può anche non riuscire a causa di un problema con i criteri di accesso in Azure Key Vault. Questa situazione può verificarsi quando si usa un modello Azure Resource Manager per creare l'area di lavoro e le risorse associate, incluso Azure Key Vault, più volte. Ad esempio, usando il modello più volte con gli stessi parametri come parte di una pipeline di distribuzione e integrazione continua.

La maggior parte delle operazioni di creazione di risorse tramite i modelli sono idempotenti, ma Key Vault consente di cancellare i criteri di accesso ogni volta che viene usato il modello. Cancellare l'accesso di interruzioni i criteri di accesso all'insieme di credenziali chiave per qualsiasi area di lavoro esistente che lo usa. Questa condizione genera errori quando si prova a creare nuove immagini. Di seguito è riportati esempi di errori che è possibile ricevere:

__Portale__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__Interfaccia della riga di comando__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Per evitare questo problema, è consigliabile uno degli approcci seguenti:

* Non distribuire il modello più volte per gli stessi parametri. O eliminare le risorse esistenti prima di usare il modello per ricrearle.
* Esaminare i criteri di accesso di Key Vault e quindi usare tali criteri per impostare il `accessPolicies` proprietà del modello.
* Controllare se la risorsa insieme di credenziali delle chiavi esiste già. In caso affermativo, non ricrearla tramite il modello. Ad esempio, aggiungere un parametro che consente di disabilitare la creazione della risorsa insieme di credenziali chiave se esiste già.

## <a name="debug-locally"></a>Eseguire il debug in locale

Se si verificano problemi durante la distribuzione di un modello ACI o servizio contenitore di AZURE, provare a distribuirlo come servizio web locale. Utilizzo di un servizio web locale rende più semplice risolvere i problemi. L'immagine Docker contenente il modello viene scaricato e avviato nel sistema locale.

> [!IMPORTANT]
> Le distribuzioni del servizio web locale richiedono un lavoro di installazione di Docker nel sistema locale. Docker deve essere in esecuzione prima di distribuire un servizio web locale. Per informazioni sull'installazione e l'uso di Docker, vedere [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Le distribuzioni del servizio web locale non sono supportate per gli scenari di produzione.

Per distribuire in locale, modificare il codice per usare `LocalWebservice.deploy_configuration()` per creare una configurazione di distribuzione. Usare quindi `Model.deploy()` per distribuire il servizio. L'esempio seguente distribuisce un modello (contenuti nel `model` variabile) come servizio web locale:

```python
from azureml.core.model import InferenceConfig
from azureml.core.webservice import LocalWebservice

# Create inferencing configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   execution_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

A questo punto, è possibile utilizzare con il servizio come di consueto. Ad esempio, il codice seguente illustra l'invio dei dati al servizio:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Aggiornare il servizio

Durante il test locale, potrebbe essere necessario aggiornare il `score.py` file in cui aggiungere la registrazione o tentare di risolvere eventuali problemi che hanno restituito. Per ricaricare le modifiche per il `score.py` file, usare `reload()`. Ad esempio, il codice seguente Ricarica lo script per il servizio e quindi invia i dati. I dati di punteggio viene calcolati usando aggiornato `score.py` file:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Lo script viene ricaricato dal percorso specificato per il `InferenceConfig` oggetto utilizzato dal servizio.

Per modificare il modello, le dipendenze Conda o configurazione della distribuzione, usare [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). L'esempio seguente aggiorna il modello usato dal servizio:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminare il servizio

Per eliminare il servizio, usare [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Esaminare il registro Docker

È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio. È possibile visualizzare il log per le distribuzioni locali ACI e AKS. Nell'esempio seguente viene illustrato come stampare i log.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Errore di avvio del servizio

Dopo che l'immagine viene creata correttamente, il sistema tenta di avviare un contenitore usando la configurazione della distribuzione. Nell'ambito del processo di avvio del contenitore, il sistema richiama la funzione `init()` nello script di assegnazione dei punteggi. Se la funzione `init()` contiene eccezioni non rilevate, nel messaggio di errore potrebbe essere visualizzato l'errore **CrashLoopBackOff**.

Usare le informazioni nel [controllare il registro Docker](#dockerlog) sezione per controllare i log.

## <a name="function-fails-getmodelpath"></a>Errore della funzione: get_model_path()

Spesso, nelle `init()` funzione nello script di assegnazione dei punteggi [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) funzione viene chiamata per individuare un file di modello o una cartella di file modello nel contenitore. Se il file di modello o cartella non viene trovata, la funzione ha esito negativo. Il modo più semplice per eseguire il debug di questo errore consiste nell'eseguire il codice Python seguente nella shell del contenitore:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

In questo esempio viene stampato il percorso locale (rispetto a `/var/azureml-app`) nel contenitore in cui lo script di assegnazione dei punteggi si aspetta di trovare il file di modello o cartella. È quindi possibile verificare se il file o la cartella si trova effettivamente dove dovrebbe essere.

Impostazione del livello di registrazione di DEBUG può causare ulteriori informazioni da registrare, che possono essere utile per identificare l'errore.

## <a name="function-fails-runinputdata"></a>Errore della funzione: run(input_data)

Se il servizio viene distribuito correttamente, ma si arresta in modo anomalo quando si pubblicano dati nell'endpoint di assegnazione dei punteggi, è possibile aggiungere un'istruzione di rilevamento degli errori nella funzione `run(input_data)` in modo che restituisca il messaggio di errore dettagliato. Ad esempio: 

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Nota**: la restituzione di messaggi di errore dalla chiamata a `run(input_data)` dovrebbe essere eseguita solo a scopo di debug. Per motivi di sicurezza, è necessario non restituire i messaggi di errore in questo modo negli ambienti di produzione.

## <a name="http-status-code-503"></a>Codice di stato HTTP 503

Le distribuzioni di Azure Kubernetes Service supportano la scalabilità automatica, che consente di repliche da aggiungere per supportare carichi aggiuntivi. Tuttavia, il ridimensionamento automatico è progettato per gestire **graduale** le modifiche nel carico. Se si ricevano picchi elevati nelle richieste al secondo, i client potrebbero ricevere un codice di stato HTTP 503.

Esistono due operazioni che possono aiutare a evitare i codici di stato 503:

* Modifica il livello di utilizzo in cui la scalabilità automatica consente di creare nuove repliche.
    
    Per impostazione predefinita, utilizzo di destinazione la scalabilità automatica è impostato al 70%, il che significa che il servizio può gestire i picchi di richieste al secondo (RPS) fino al 30%. È possibile modificare la destinazione di utilizzo impostando la `autoscale_target_utilization` su un valore inferiore.

    > [!IMPORTANT]
    > Questa modifica non causa repliche da creare *più velocemente*. Al contrario, vengono create con una soglia minore utilizzo. Anziché attendere che il servizio è 70% utilizzato, la modifica del valore al 30% fa sì che le repliche da creare quando si verifica 30% di utilizzo.
    
    Se il servizio web sta già usando le repliche correnti max e vengono ancora visualizzati i codici di stato 503, aumentare il `autoscale_max_replicas` valore per aumentare il numero massimo di repliche.

* Modificare il numero minimo di repliche. Aumentare le repliche minima fornisce un pool più ampio per gestire i picchi in ingresso.

    Per aumentare il numero minimo di repliche, impostare `autoscale_min_replicas` su un valore superiore. È possibile calcolare le repliche necessarie usando il codice seguente, sostituendo i valori con valori specifici per il progetto:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Se si ricevono i picchi di richiesta maggiore di quanto possono gestire le nuove repliche minime, si potrebbe ricevere di nuovo 503s. Come il traffico verso l'aumento di servizio, ad esempio, si potrebbe essere necessario aumentare le repliche minime.

Per altre informazioni sull'impostazione `autoscale_target_utilization`, `autoscale_max_replicas`, e `autoscale_min_replicas` for, vedere il [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) riferimento al modulo.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: Eseguire il training e la distribuzione di modelli](tutorial-train-models-with-aml.md)
