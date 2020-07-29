---
title: Risoluzione dei problemi di distribuzione di Docker
titleSuffix: Azure Machine Learning
description: Informazioni su come risolvere, risolvere e risolvere i problemi relativi agli errori di distribuzione comuni di Docker con il servizio Azure Kubernetes e le istanze di contenitore di Azure con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, tracking-python
ms.openlocfilehash: dcb2a50a91bec70dfe5d9adda7518f3510a8c973
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373199"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Risolvere i problemi di distribuzione Docker dei modelli con il servizio Azure Kubernetes e le istanze di contenitore di Azure 

Informazioni su come risolvere e risolvere gli errori comuni di distribuzione Docker con istanze di contenitore di Azure (ACI) e Azure Kubernetes Service (AKS) con Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non se ne possiede una, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Per eseguire il debug localmente, è necessario disporre di un'installazione Docker funzionante nel sistema locale.

    Per verificare l'installazione Docker, usare il comando `docker run hello-world` da un terminale o da un prompt dei comandi. Per informazioni sull'installazione Docker o sulla risoluzione dei problemi relativi agli errori Docker, vedere la [Documentazione di Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Passaggi per la distribuzione di Docker dei modelli di Machine Learning

Durante la distribuzione di un modello in Azure Machine Learning, il sistema esegue una serie di attività.

L'approccio consigliato per la distribuzione del modello è tramite l'API [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) usando un oggetto [Environment](how-to-use-environments.md) come parametro di input. In questo caso, il servizio crea un'immagine Docker di base durante la fase di distribuzione e monta i modelli richiesti in una sola chiamata. Le attività di distribuzione di base sono le seguenti:

1. Registrare il modello nel registro dei modelli dell'area di lavoro.

2. Definire la configurazione dell'inferenza:
    1. Creare un oggetto [Ambiente](how-to-use-environments.md) in base alle dipendenze specificate nel file YAML dell'ambiente o usare uno degli ambienti di approvvigionamento.
    2. Creare una configurazione dell'inferenza (oggetto InferenceConfig) in base all'ambiente e allo script di assegnazione dei punteggi.

3. Distribuire il modello nel servizio Istanze di Azure Container (ACI) o nel servizio Azure Kubernetes (AKS).

Per altre informazioni su questa procedura, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Prima di iniziare

Se si verifica un problema, la prima cosa da fare è suddividere l'attività di distribuzione (descritta in precedenza) in singoli passaggi per isolare il problema.

Supponendo che si stia usando il metodo di distribuzione nuovo/consigliato tramite l'API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) con un oggetto [Ambiente](how-to-use-environments.md) come parametro di input, il codice può essere suddiviso in tre passaggi principali:

1. Registrare il modello. Di seguito è riportato il codice di esempio:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definire la configurazione dell'inferenza per la distribuzione:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Distribuire il modello usando la configurazione dell'inferenza creata nel passaggio precedente:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Una volta suddiviso il processo di distribuzione in singole attività, è possibile esaminare alcuni degli errori più comuni.

## <a name="debug-locally"></a>Eseguire il debug in locale

Se si verificano problemi durante la distribuzione di un modello in ACI o AKS, provare a distribuirlo come servizio Web locale. L'utilizzo di un servizio Web locale rende più semplice la risoluzione dei problemi. L'immagine Docker contenente il modello viene scaricata e avviata nel sistema locale.

Per esplorare un esempio eseguibile, è possibile trovare un [notebook di distribuzione locale](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) di esempio nel repository [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) .

> [!WARNING]
> Le distribuzioni di servizi Web locali non sono supportate per gli scenari di produzione.

Per eseguire la distribuzione localmente, modificare il codice per usare `LocalWebservice.deploy_configuration()` per creare una configurazione della distribuzione. Usare quindi `Model.deploy()` per distribuire il servizio. Nell'esempio seguente viene distribuito un modello (contenuto nella variabile del modello) come servizio Web locale:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
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

Se si definisce YAML specifiche conda, è necessario elencare azureml-defaults con Version >= 1.0.45 come dipendenza PIP. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web.

A questo punto, è possibile usare il servizio come di consueto. Ad esempio, il codice seguente dimostra l'invio di dati al servizio:

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

Per altre informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aggiornare il servizio

Durante i test locali, potrebbe essere necessario aggiornare il file `score.py` per aggiungere la registrazione o tentare di risolvere eventuali problemi individuati. Per ricaricare le modifiche apportate al file di `score.py`, usare `reload()`. Il codice seguente, ad esempio, consente di ricaricare lo script per il servizio e quindi di inviarvi dati. Il punteggio dei dati viene assegnato tramite il file `score.py` aggiornato:

> [!IMPORTANT]
> Il metodo `reload` è disponibile solo per le distribuzioni locali. Per informazioni sull'aggiornamento di una distribuzione in un'altra destinazione di calcolo, vedere la sezione relativa all'aggiornamento di [Distribuire modelli](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Lo script viene ricaricato dalla posizione specificata dall'oggetto `InferenceConfig` usato dal servizio.

Per modificare il modello, le dipendenze Conda o la configurazione della distribuzione, usare [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Nell'esempio seguente viene aggiornato il modello usato dal servizio:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminare il servizio

Per eliminare il servizio, usare [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Esaminare il log Docker

È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio. È possibile visualizzare il log per ACI, AKS e distribuzioni locali. L'esempio seguente illustra come stampare i log.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Se la riga si `Booting worker with pid: <pid>` verifica più volte nei log, significa che non è disponibile memoria sufficiente per avviare il thread di lavoro.
È possibile risolvere l'errore aumentando il valore di `memory_gb` in`deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>Non è possibile pianificare il contenitore

Quando si distribuisce un servizio in una destinazione di calcolo del servizio Kubernetes di Azure, Azure Machine Learning tenterà di pianificare il servizio con la quantità di risorse richiesta. Se dopo 5 minuti non sono disponibili nodi nel cluster con la quantità appropriata di risorse disponibili, la distribuzione avrà esito negativo con il messaggio `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Per risolvere questo errore, è possibile aggiungere più nodi, modificare lo SKU dei nodi o modificare i requisiti di risorse del servizio. 

Il messaggio di errore indicherà in genere la risorsa necessaria. ad esempio, se viene visualizzato un messaggio di errore `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` che indica che il servizio richiede GPU e sono presenti tre nodi nel cluster che non hanno GPU disponibili. È possibile risolvere questo problema aggiungendo altri nodi se si usa uno SKU GPU, passando a uno SKU abilitato per GPU in caso contrario, o modificando l'ambiente in modo da non richiedere GPU.  

## <a name="service-launch-fails"></a>Errore di avvio del servizio

Dopo aver creato correttamente l'immagine, il sistema tenta di avviare un contenitore usando la configurazione della distribuzione. Nell'ambito del processo di avvio del contenitore, il sistema richiama la funzione `init()` nello script di assegnazione dei punteggi. Se la funzione `init()` contiene eccezioni non rilevate, nel messaggio di errore potrebbe essere visualizzato l'errore **CrashLoopBackOff**.

Usare le informazioni nella sezione [Esaminare il log di Docker](#dockerlog) per controllare i log.

## <a name="function-fails-get_model_path"></a>Errore della funzione: get_model_path()

Nella funzione `init()` dello script di assegnazione dei punteggi viene spesso chiamata la funzione [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) per individuare un file di modello o una cartella di file di modello nel contenitore. Se non è possibile trovare il file o la cartella di modello, la funzione ha esito negativo. Il modo più semplice per eseguire il debug di questo errore consiste nell'eseguire il codice Python seguente nella shell del contenitore:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Questo esempio consente di visualizzare il percorso locale (relativo a `/var/azureml-app`) nel contenitore in cui si prevede che lo script di assegnazione dei punteggi trovi il file o la cartella di modello. È quindi possibile verificare se il file o la cartella si trova effettivamente dove dovrebbe essere.

L'impostazione del livello di registrazione su DEBUG potrebbe causare la registrazione di informazioni aggiuntive che potrebbero essere utili per identificare l'errore.

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

**Nota**: la restituzione di messaggi di errore dalla chiamata a `run(input_data)` dovrebbe essere eseguita solo a scopo di debug. Per motivi di sicurezza, non si dovrebbe restituire i messaggi di errore in questo modo in un ambiente di produzione.

## <a name="http-status-code-502"></a>Codice di stato HTTP 502

Un codice di stato 502 indica che il servizio ha generato un'eccezione o si è arrestato in modo anomalo nel metodo `run()` del file score.py. Usare le informazioni presenti in questo articolo per eseguire il debug del file.

## <a name="http-status-code-503"></a>Codice di stato HTTP 503

Le distribuzioni del servizio Azure Kubernetes supportano il ridimensionamento automatico, che consente di aggiungere repliche per supportare un carico aggiuntivo. Tuttavia, il ridimensionamento automatico è progettato per gestire modifiche **graduali** nel carico. Se si ricevono picchi elevati di richieste al secondo, i client potrebbero ricevere un codice di stato HTTP 503.

Esistono due elementi che consentono di prevenire codici di stato 503:

* Modificare il livello di utilizzo a cui il ridimensionamento automatico crea nuove repliche.
    
    Per impostazione predefinita, l'uso della destinazione per il ridimensionamento automatico è impostato su 70%, che significa che il servizio è in grado di gestire picchi di richieste al secondo (RPS) fino al 30%. È possibile regolare la destinazione di utilizzo impostando `autoscale_target_utilization` su un valore inferiore.

    > [!IMPORTANT]
    > Questa modifica non comporta la creazione di repliche *più velocemente*. Vengono invece create con una soglia di utilizzo inferiore. Anziché attendere fino al 70% di utilizzo del servizio, la modifica del valore su 30% comporta la creazione di repliche quando si verifica l'utilizzo al 30%.
    
    Se il servizio Web sta già usando le repliche massime correnti e vengono ancora visualizzati codici di stato 503, aumentare il valore di `autoscale_max_replicas` per aumentare il numero massimo di repliche.

* Modificare il numero minimo di repliche. L'aumento delle repliche minime offre un pool più grande per gestire i picchi in ingresso.

    Per aumentare il numero minimo di repliche, impostare `autoscale_min_replicas` su un valore più elevato. È possibile calcolare le repliche richieste usando il codice seguente, sostituendo i valori con valori specifici del progetto:

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
    > Se si ricevono picchi di richiesta di dimensioni maggiori di quelle che possono essere gestite dalle nuove repliche minime, si potrebbero ricevere nuovamente codici di stato 503. Ad esempio, mano a mano che il traffico verso il servizio aumenta, potrebbe essere necessario aumentare le repliche minime.

Per altre informazioni sull'impostazione di `autoscale_target_utilization`, `autoscale_max_replicas` e `autoscale_min_replicas`, vedere il riferimento al modulo [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py).

## <a name="http-status-code-504"></a>Codice di stato HTTP 504

Un codice di stato 504 indica che si è verificato il timeout della richiesta. Il timeout predefinito è 1 minuto.

È possibile aumentare il timeout o provare ad accelerare il servizio modificando il file score.py per rimuovere le chiamate non necessarie. Se queste azioni non consentono di risolvere il problema, usare le informazioni in questo articolo per eseguire il debug del file score.py. Il codice potrebbe trovarsi in uno stato non reattivo o in un ciclo infinito.

## <a name="advanced-debugging"></a>Debug avanzato

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python contenuto nella distribuzione del modello. Ad esempio, se lo script di immissione ha esito negativo e il motivo non può essere determinato da una registrazione aggiuntiva. Usando Visual Studio Code e Python Tools for Visual Studio (PTVSD), è possibile connettersi al codice in esecuzione all'interno del contenitore Docker.

> [!IMPORTANT]
> Questo metodo di debug non funziona quando si usano `Model.deploy()` e `LocalWebservice.deploy_configuration` per distribuire un modello localmente. Al contrario, è necessario creare un'immagine usando il metodo [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-).

Per le distribuzioni di servizi Web locali è necessaria un'installazione Docker funzionante nel sistema locale. Per altre informazioni sull'uso di Docker, vedere la [Documentazione di Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare Python Tools for Visual Studio (PTVSD) nell'ambiente di sviluppo VS Code locale, usare il comando seguente:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Per altre informazioni sull'uso di PTVSD con VS Code, vedere [Debug remoto](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Per configurare VS Code per la comunicazione con l'immagine Docker, creare una nuova configurazione di debug:

    1. Da VS Code selezionare il menu __Debug__, quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __launch.json__.

    1. Nel file __launch.json__ trovare la riga che contiene `"configurations": [` e inserire il testo seguente dopo di essa:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
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
        > Se nella sezione Configurazioni sono già presenti altre voci, aggiungere una virgola (,) dopo il codice inserito.

        Questa sezione si connette al contenitore Docker usando la porta 5678.

    1. Salvare il file __launch.json__.

### <a name="create-an-image-that-includes-ptvsd"></a>Creare un'immagine che includa PTVSD

1. Modificare l'ambiente Conda per la distribuzione, in modo che includa PTVSD. L'esempio seguente illustra come aggiungerlo usando il parametro `pip_packages`:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Per avviare PTVSD e attendere la connessione all'avvio del servizio, aggiungere quanto segue all'inizio del file `score.py`:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Creare un'immagine in base alla definizione dell'ambiente ed eseguire il pull dell'immagine nel Registro di sistema locale. Durante il debug, potrebbe essere necessario apportare modifiche ai file dell'immagine senza doverla ricreare. Per installare un editor di testo (Vim) nell'immagine Docker, usare le proprietà `Environment.docker.base_image` e `Environment.docker.base_dockerfile`:

    > [!NOTE]
    > In questo esempio si presuppone che `ws` punti all'area di lavoro di Azure Machine Learning e che `model` sia il modello distribuito. Il file `myenv.yml` contiene le dipendenze Conda create nel passaggio 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Una volta che l'immagine è stata creata e scaricata, il percorso dell'immagine (inclusi repository, nome e tag, che in questo caso è anche il codice hash) viene visualizzato in un messaggio simile al seguente:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Per semplificare le operazioni con l'immagine, usare il comando seguente per aggiungere un tag. Sostituire `myimagepath` con il valore della posizione del passaggio precedente.

    ```bash
    docker tag myimagepath debug:1
    ```

    Per i passaggi rimanenti, è possibile fare riferimento all'immagine locale come `debug:1` anziché al valore del percorso dell'immagine completa.

### <a name="debug-the-service"></a>Eseguire il debug del servizio

> [!TIP]
> Se si imposta un timeout per la connessione PTVSD nel file `score.py`, è necessario connettere VS Code alla sessione di debug prima della scadenza del timeout. Avviare VS Code, aprire la copia locale di `score.py`, impostare un punto di interruzione e fare in modo che sia pronto prima di usare i passaggi descritti in questa sezione.
>
> Per altre informazioni sul debug e sull'impostazione di punti di interruzione, vedere [Debug](https://code.visualstudio.com/Docs/editor/debugging).

1. Per avviare un contenitore Docker usando l'immagine, usare il comando seguente:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Per aggiungere VS Code a PTVSD all'interno del contenitore, aprire VS Code e premere il tasto F5 oppure selezionare __Debug__. Quando richiesto, selezionare la configurazione __Azure Machine Learning: Debug di Docker__. È anche possibile selezionare l'icona Debug dalla barra laterale, la voce __Azure Machine Learning: Debug di Docker__ dal menu a discesa Debug, quindi usare la freccia verde per collegare il debugger.

    ![Icona Debug, pulsante Avvia debug e selettore della configurazione](./media/how-to-troubleshoot-deployment/start-debugging.png)

A questo punto, VS Code si connette a PTVSD all'interno del contenitore Docker e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile scorrere il codice durante l'esecuzione, visualizzare le variabili e così via.

Per altre informazioni sull'uso di VS Code per eseguire il debug di Python, vedere [Eseguire il debug del codice Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificare i file contenitore

Per apportare modifiche ai file nell'immagine, è possibile connettersi al contenitore in esecuzione ed eseguire una shell Bash. Da qui è possibile usare Vim per modificare i file:

1. Per connettersi al contenitore in esecuzione e avviare una shell Bash nel contenitore, usare il comando seguente:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Per trovare i file usati dal servizio, usare il comando seguente dalla shell Bash nel contenitore se la directory predefinita è diversa da `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    Da qui è possibile usare Vim per modificare il file `score.py`. Per altre informazioni sull'uso di Vim, vedere [Using the Vim editor](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html) (Uso dell'editor Vim).

1. Le modifiche apportate a un contenitore in genere non vengono rese persistenti. Per salvare le modifiche apportate, usare il comando seguente prima di uscire dalla shell avviata nel passaggio precedente, ovvero in un'altra shell:

    ```bash
    docker commit debug debug:2
    ```

    Questo comando crea una nuova immagine denominata `debug:2`, che contiene le modifiche.

    > [!TIP]
    > Per rendere effettive le modifiche, sarà necessario arrestare il contenitore corrente e iniziare a usare la nuova versione.

1. Assicurarsi di mantenere le modifiche apportate ai file nel contenitore sincronizzate con i file locali usati da VS Code. In caso contrario, il debugger non funzionerà come previsto.

### <a name="stop-the-container"></a>Arrestare il contenitore

Per arrestare il contenitore, usare il comando seguente:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: Eseguire il training e la distribuzione di modelli](tutorial-train-models-with-aml.md)
