---
title: Guida alla risoluzione dei problemi di distribuzione
titleSuffix: Azure Machine Learning service
description: Informazioni su come risolvere, risolvere e risolvere i problemi relativi agli errori di distribuzione comuni di Docker con il servizio Azure Kubernetes e le istanze di contenitore di Azure con Azure Machine Learning servizio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2019
ms.custom: seodec18
ms.openlocfilehash: 5ec92e34ffa68718525e9b407dc9e58f4c409975
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183548"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Risoluzione dei problemi relativi al servizio Azure Machine Learning di Azure Kubernetes e alla distribuzione di istanze di contenitore di Azure

Informazioni su come risolvere gli errori comuni di distribuzione di Docker con istanze di contenitore di Azure (ACI) e Azure Kubernetes Service (AKS) usando Azure Machine Learning Service.

Durante la distribuzione di un modello nel servizio Azure Machine Learning, il sistema esegue una serie di attività. Le attività di distribuzione sono le seguenti:

1. Registrare il modello nel registro dei modelli dell'area di lavoro.

2. Creare un'immagine Docker seguendo questa procedura:
    1. Scaricare il modello registrato dal registro. 
    2. Creare un Dockerfile, con un ambiente Python basato sulle dipendenze specificate nel file YAML di ambiente.
    3. Aggiungere i file di modello e lo script di assegnazione dei punteggi fornito nel Dockerfile.
    4. Creare una nuova immagine Docker usando il Dockerfile.
    5. Registrare l'immagine Docker con il Registro Azure Container associato all'area di lavoro.

    > [!IMPORTANT]
    > A seconda del codice, la creazione di immagini viene eseguita automaticamente senza l'input.

3. Distribuire l'immagine Docker al servizio Istanze di Azure Container o al servizio Azure Kubernetes.

4. Avviare uno o più nuovi contenitori in uno di questi due servizi. 

Per altre informazioni su questa procedura, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Prima di iniziare

Se si verifica un problema, la prima cosa da fare è suddividere l'attività di distribuzione (descritta in precedenza) in singoli passaggi per isolare il problema.

Suddividere la distribuzione in attività è utile se si usa l'API [WebService. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) o l'API [WebService. deploy_from_model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) , perché entrambe queste funzioni eseguono i passaggi precedenti come una singola azione. In genere, queste API sono utili, ma consentono di suddividere i passaggi per la risoluzione dei problemi sostituendo tali API con le chiamate API riportate di seguito.

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
                                                      entry_script="score.py",
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

Se non è possibile compilare l'immagine Docker, la chiamata [Image. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) o [Service. wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) ha esito negativo con alcuni messaggi di errore che possono offrire alcuni indizi. Anche nel log di compilazione dell'immagine possono essere disponibili altri dettagli sugli errori. Di seguito è riportato del codice di esempio che mostra come individuare l'URI del log di compilazione dell'immagine.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

L'URI del log è un URL SAS che punta a un file di log archiviato nell'Archivio BLOB di Azure. Basta copiare e incollare l'URI in una finestra del browser per poter scaricare e visualizzare il file di log.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Criteri di accesso Azure Key Vault e modelli di Azure Resource Manager

La compilazione dell'immagine può anche avere esito negativo a causa di un problema con i criteri di accesso in Azure Key Vault. Questa situazione può verificarsi quando si usa un modello di Azure Resource Manager per creare l'area di lavoro e le risorse associate (incluso Azure Key Vault) più volte. Ad esempio, l'uso del modello più volte con gli stessi parametri come parte di una pipeline di integrazione e distribuzione continua.

La maggior parte delle operazioni di creazione di risorse tramite i modelli è idempotente, ma Key Vault Cancella i criteri di accesso ogni volta che viene usato il modello. La cancellazione dei criteri di accesso consente di suddividere l'accesso alla Key Vault per tutte le aree di lavoro esistenti che lo usano. Questa condizione genera errori quando si tenta di creare nuove immagini. Di seguito sono riportati alcuni esempi degli errori che è possibile ricevere:

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

Per evitare questo problema, è consigliabile usare uno degli approcci seguenti:

* Non distribuire il modello più di una volta per gli stessi parametri. In alternativa, eliminare le risorse esistenti prima di usare il modello per ricrearle.
* Esaminare i criteri di accesso key Vault e quindi usare questi criteri per impostare `accessPolicies` la proprietà del modello.
* Controllare se la risorsa Key Vault esiste già. In caso contrario, non ricrearlo tramite il modello. Ad esempio, aggiungere un parametro che consente di disabilitare la creazione della risorsa Key Vault se esiste già.

## <a name="debug-locally"></a>Esegui debug localmente

Se si verificano problemi durante la distribuzione di un modello in ACI o AKS, provare a distribuirlo come servizio Web locale. L'utilizzo di un servizio Web locale rende più semplice la risoluzione dei problemi. L'immagine Docker contenente il modello viene scaricata e avviata nel sistema locale.

> [!IMPORTANT]
> Per le distribuzioni di servizi Web locali è necessaria un'installazione Docker funzionante nel sistema locale. Docker deve essere in esecuzione prima di distribuire un servizio Web locale. Per informazioni sull'installazione e sull'uso di Docker [https://www.docker.com/](https://www.docker.com/), vedere.

> [!WARNING]
> Le distribuzioni di servizi Web locali non sono supportate per gli scenari di produzione.

Per eseguire la distribuzione in locale, modificare il `LocalWebservice.deploy_configuration()` codice in modo da usare per creare una configurazione di distribuzione. Usare `Model.deploy()` quindi per distribuire il servizio. Nell'esempio seguente viene distribuito un modello (contenuto nella `model` variabile) come servizio Web locale:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

A questo punto, è possibile usare il servizio come di consueto. Ad esempio, nel codice seguente viene illustrato l'invio di dati al servizio:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Aggiornare il servizio

Durante i test locali, potrebbe essere necessario aggiornare il `score.py` file per aggiungere la registrazione o tentare di risolvere eventuali problemi individuati. Per ricaricare le modifiche `score.py` apportate `reload()`al file, usare. Il codice seguente, ad esempio, consente di ricaricare lo script per il servizio e quindi di inviarvi dati. Il Punteggio dei dati viene eseguito usando il `score.py` file aggiornato:

> [!IMPORTANT]
> Il `reload` metodo è disponibile solo per le distribuzioni locali. Per informazioni sull'aggiornamento di una distribuzione di a un'altra destinazione di calcolo, vedere la sezione relativa all'aggiornamento di [deploy Models](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Lo script viene ricaricato dal percorso specificato dall' `InferenceConfig` oggetto utilizzato dal servizio.

Per modificare il modello, le dipendenze conda o la configurazione della distribuzione, utilizzare [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Nell'esempio seguente viene aggiornato il modello utilizzato dal servizio:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminare il servizio

Per eliminare il servizio, utilizzare [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a>Esaminare il log di Docker

È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio. È possibile visualizzare il log per ACI, AKS e distribuzioni locali. Nell'esempio seguente viene illustrato come stampare i log.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Errore di avvio del servizio

Dopo che l'immagine è stata compilata correttamente, il sistema tenta di avviare un contenitore usando la configurazione di distribuzione. Nell'ambito del processo di avvio del contenitore, il sistema richiama la funzione `init()` nello script di assegnazione dei punteggi. Se la funzione `init()` contiene eccezioni non rilevate, nel messaggio di errore potrebbe essere visualizzato l'errore **CrashLoopBackOff**.

Usare le informazioni nella sezione [esaminare il registro Docker](#dockerlog) per controllare i log.

## <a name="function-fails-get_model_path"></a>Errore della funzione: get_model_path()

Spesso, nella `init()` funzione nello script di assegnazione dei punteggi, viene chiamata la funzione [Model. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) per individuare un file di modello o una cartella di file di modello nel contenitore. Se non è possibile trovare il file o la cartella del modello, la funzione ha esito negativo. Il modo più semplice per eseguire il debug di questo errore consiste nell'eseguire il codice Python seguente nella shell del contenitore:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Questo esempio Mostra come stampare il percorso locale (relativo `/var/azureml-app`a) nel contenitore in cui lo script di assegnazione dei punteggi è in attesa di trovare il file o la cartella del modello. È quindi possibile verificare se il file o la cartella si trova effettivamente dove dovrebbe essere.

L'impostazione del livello di registrazione su DEBUG può causare la registrazione di informazioni aggiuntive, che possono essere utili per identificare l'errore.

## <a name="function-fails-runinput_data"></a>Errore della funzione: run(input_data)

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

**Nota**: la restituzione di messaggi di errore dalla chiamata a `run(input_data)` dovrebbe essere eseguita solo a scopo di debug. Per motivi di sicurezza, non è necessario restituire messaggi di errore in questo modo in un ambiente di produzione.

## <a name="http-status-code-503"></a>Codice di stato HTTP 503

Le distribuzioni del servizio Azure Kubernetes supportano la scalabilità automatica, che consente di aggiungere le repliche per supportare un carico aggiuntivo. Tuttavia, la scalabilità automatica è progettata per gestire modifiche graduali del carico. Se si ricevono picchi elevati di richieste al secondo, i client possono ricevere un codice di stato HTTP 503.

Esistono due elementi che consentono di prevenire i codici di stato 503:

* Modificare il livello di utilizzo a cui la scalabilità automatica crea nuove repliche.
    
    Per impostazione predefinita, l'utilizzo della destinazione per la scalabilità automatica è impostato su 70%, il che significa che il servizio è in grado di gestire picchi di richieste al secondo (RPS) fino al 30%. È possibile modificare la destinazione di utilizzo impostando `autoscale_target_utilization` su un valore inferiore.

    > [!IMPORTANT]
    > Questa modifica non comporta la creazione di repliche *più veloci*. Vengono invece creati con una soglia di utilizzo inferiore. Anziché attendere fino al 70% del servizio, la modifica del valore sul 30% comporta la creazione di repliche quando si verifica l'utilizzo del 30%.
    
    Se il servizio Web sta già usando le repliche massime correnti e si stanno ancora visualizzando 503 codici di stato, `autoscale_max_replicas` aumentare il valore per aumentare il numero massimo di repliche.

* Modificare il numero minimo di repliche. L'aumento delle repliche minime fornisce un pool più grande per gestire i picchi in ingresso.

    Per aumentare il numero minimo di repliche, impostare `autoscale_min_replicas` su un valore più alto. È possibile calcolare le repliche richieste usando il codice seguente, sostituendo i valori con i valori specifici del progetto:

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
    > Se si ricevono picchi di richiesta di dimensioni maggiori di quelle che possono essere gestite da una nuova replica minima, è possibile che si riceva nuovamente 503S. Ad esempio, quando aumenta il traffico verso il servizio, potrebbe essere necessario aumentare le repliche minime.

Per ulteriori informazioni sull'impostazione `autoscale_target_utilization`di `autoscale_max_replicas`, e `autoscale_min_replicas` per, vedere la Guida di riferimento al modulo [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .


## <a name="advanced-debugging"></a>Debug avanzato

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python contenuto nella distribuzione del modello. Ad esempio, se lo script di ingresso ha esito negativo e il motivo non può essere determinato da una registrazione aggiuntiva. Utilizzando Visual Studio Code e il Python Tools for Visual Studio (PTVSD), è possibile connettersi al codice in esecuzione all'interno del contenitore docker.

> [!IMPORTANT]
> Questo metodo di debug non funziona quando si utilizzano `Model.deploy()` e `LocalWebservice.deploy_configuration` per distribuire un modello localmente. È invece necessario creare un'immagine utilizzando la classe [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) . 
>
> Per le distribuzioni di servizi Web locali è necessaria un'installazione Docker funzionante nel sistema locale. Docker deve essere in esecuzione prima di distribuire un servizio Web locale. Per informazioni sull'installazione e sull'uso di Docker [https://www.docker.com/](https://www.docker.com/), vedere.

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare il Python Tools for Visual Studio (PTVSD) nell'ambiente di sviluppo locale VS Code, utilizzare il comando seguente:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Per ulteriori informazioni sull'utilizzo di PTVSD con VS Code, vedere [Remote Debugging](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Per configurare VS Code per la comunicazione con l'immagine Docker, creare una nuova configurazione di debug:

    1. Da VS Code selezionare il menu __debug__ e quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __Launch. JSON__ .

    1. Nel file __Launch. JSON__ trovare la riga che contiene `"configurations": [`e inserire il testo seguente dopo di esso:

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se nella sezione configurazioni sono già presenti altre voci, aggiungere una virgola (,) dopo il codice inserito.

        Questa sezione si connette al contenitore Docker usando la porta 5678.

    1. Salvare il file __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Creare un'immagine che includa PTVSD

1. Modificare l'ambiente conda per la distribuzione in modo che includa PTVSD. Nell'esempio seguente viene illustrato come aggiungerlo `pip_packages` utilizzando il parametro:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Per avviare PTVSD e attendere la connessione all'avvio del servizio, aggiungere quanto segue all'inizio del `score.py` file:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Durante il debug, potrebbe essere necessario apportare modifiche ai file dell'immagine senza ricrearla. Per installare un editor di testo (VIM) nell'immagine Docker, creare un nuovo file di testo `Dockerfile.steps` denominato e usare il codice seguente come contenuto del file:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Un editor di testo consente di modificare i file all'interno dell'immagine Docker per testare le modifiche senza creare una nuova immagine.

1. Per creare un'immagine che usa il `Dockerfile.steps` file, usare il `docker_file` parametro durante la creazione di un'immagine. Nell'esempio seguente viene illustrato come eseguire questa operazione:

    > [!NOTE]
    > In questo esempio si `ws` presuppone che punti all'area di lavoro Azure Machine Learning `model` e che sia il modello distribuito. Il `myenv.yml` file contiene le dipendenze conda create nel passaggio 1.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

Una volta creata l'immagine, viene visualizzata la posizione dell'immagine nel registro di sistema. Il percorso è simile al testo seguente:

```text
myregistry.azurecr.io/myimage:1
```

In questo esempio di testo, il nome del `myregistry` registro di sistema è e `myimage`l'immagine è denominata. La versione dell'immagine `1`è.

### <a name="download-the-image"></a>Scaricare l'immagine

1. Aprire un prompt dei comandi, un terminale o un'altra shell e usare il comando dell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) seguente per eseguire l'autenticazione alla sottoscrizione di Azure che contiene l'area di lavoro Azure Machine Learning:

    ```azurecli
    az login
    ```

1. Per eseguire l'autenticazione nel Container Registry di Azure (ACR) che contiene l'immagine, usare il comando seguente. Sostituire `myregistry` con quello restituito quando è stata registrata l'immagine:

    ```azurecli
    az acr login --name myregistry
    ```

1. Per scaricare l'immagine nel Docker locale, usare il comando seguente. Sostituire `myimagepath` con il percorso restituito quando è stata registrata l'immagine:

    ```bash
    docker pull myimagepath
    ```

    Il percorso dell'immagine deve essere simile `myregistry.azurecr.io/myimage:1`a. Dove `myregistry` si trova il registro `myimage` di sistema, è l' `1` immagine e è la versione dell'immagine.

    > [!TIP]
    > L'autenticazione dal passaggio precedente non dura per sempre. Se il tempo di attesa è sufficiente tra il comando di autenticazione e il comando pull, si riceverà un errore di autenticazione. In questo caso, ripetere l'autenticazione.

    Il tempo necessario per completare il download dipende dalla velocità della connessione Internet. Durante il processo, viene visualizzato lo stato del download. Al termine del download, è possibile usare il `docker images` comando per verificare che sia stato scaricato.

1. Per semplificare l'utilizzo dell'immagine, utilizzare il comando seguente per aggiungere un tag. Sostituire `myimagepath` con il valore location del passaggio 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    Per il resto dei passaggi, è possibile fare riferimento all'immagine `debug:1` locale anziché al valore del percorso completo dell'immagine.

### <a name="debug-the-service"></a>Eseguire il debug del servizio

> [!TIP]
> Se si imposta un timeout per la connessione PTVSD nel `score.py` file, è necessario connettere vs code alla sessione di debug prima della scadenza del timeout. Avviare vs code, aprire la copia locale di `score.py`, impostare un punto di interruzione e fare in modo che sia possibile procedere prima di usare la procedura descritta in questa sezione.
>
> Per ulteriori informazioni sul debug e sull'impostazione di punti di interruzione, vedere [debug](https://code.visualstudio.com/Docs/editor/debugging).

1. Per avviare un contenitore Docker usando l'immagine, usare il comando seguente:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Per aggiungere VS Code a PTVSD all'interno del contenitore, aprire VS Code e usare il tasto F5 oppure selezionare __debug__. Quando richiesto, selezionare il __servizio Azure Machine Learning: Configurazione di debug__ di Docker. È anche possibile selezionare l'icona debug dalla barra laterale, il __servizio Azure Machine Learning: Voce di debug__ Docker dal menu a discesa debug, quindi usare la freccia verde per collegare il debugger.

    ![Icona debug, pulsante Avvia debug e selettore configurazione](media/how-to-troubleshoot-deployment/start-debugging.png)

A questo punto, VS Code si connette a PTVSD all'interno del contenitore Docker e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile esaminare il codice durante l'esecuzione, visualizzare le variabili e così via.

Per altre informazioni sull'uso di VS Code per eseguire il debug di Python, vedere [eseguire il debug del codice Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificare i file contenitore

Per apportare modifiche ai file nell'immagine, è possibile connettersi al contenitore in esecuzione ed eseguire una shell bash. Da qui è possibile usare VIM per modificare i file:

1. Per connettersi al contenitore in esecuzione e avviare una shell bash nel contenitore, usare il comando seguente:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Per trovare i file usati dal servizio, usare il comando seguente dalla shell bash nel contenitore:

    ```bash
    cd /var/azureml-app
    ```

    Da qui è possibile usare VIM per modificare il `score.py` file. Per altre informazioni sull'uso di vim, vedere [uso dell'editor vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Le modifiche apportate a un contenitore non sono in genere rese permanente. Per salvare le modifiche apportate, usare il comando seguente prima di uscire dalla shell avviata nel passaggio precedente, ovvero in un'altra shell:

    ```bash
    docker commit debug debug:2
    ```

    Questo comando crea una nuova immagine denominata `debug:2` che contiene le modifiche.

    > [!TIP]
    > Per rendere effettive le modifiche, sarà necessario arrestare il contenitore corrente e iniziare a usare la nuova versione.

1. Assicurarsi di rispettare le modifiche apportate ai file nel contenitore sincronizzati con i file locali utilizzati VS Code. In caso contrario, l'esperienza del debugger non funzionerà come previsto.

### <a name="stop-the-container"></a>Arrestare il contenitore

Per arrestare il contenitore, usare il comando seguente:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: Eseguire il training e la distribuzione di modelli](tutorial-train-models-with-aml.md)
