---
title: Guida alla risoluzione dei problemi di distribuzione
titleSuffix: Azure Machine Learning
description: Informazioni su come risolvere, risolvere e risolvere gli errori di distribuzione di Docker comuni con il servizio Azure Kubernetes e le istanze del contenitore di Azure usando Azure Machine Learning.Learn how to work, solve, and troubleshoot the common Docker deployment errors with Azure Kubernetes Service and Azure Container Instances using Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399676"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Risoluzione dei problemi relativi al servizio Azure Machine Learning Azure Kubernetes E alla distribuzione delle istanze del contenitore di AzureTroubleshooting Azure Machine Learning Azure Kubernetes Service and Azure Container Instances deployment

Informazioni su come risolvere o risolvere errori comuni di distribuzione di Docker con le istanze del contenitore di Azure (ACI) e il servizio Azure Kubernetes (AKS) con Azure Machine Learning.Learn how to work or solve common Docker deployment errors with Azure Container Instances (ACI) and Azure Kubernetes Service (AKS) using Azure Machine Learning.

Quando si distribuisce un modello in Azure Machine Learning, il sistema esegue una serie di attività.

L'approccio consigliato e più aggiornato per la distribuzione del modello è tramite l'API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) usando un oggetto [Environment](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) come parametro di input. In questo caso il nostro servizio creerà automaticamente un'immagine docker di base durante la fase di distribuzione e monterà i modelli richiesti in un'unica chiamata. Le attività di distribuzione di base sono:The basic deployment tasks are:

1. Registrare il modello nel registro dei modelli dell'area di lavoro.

2. Definire la configurazione dell'inferenza:
    1. Creare un oggetto [Environment](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) in base alle dipendenze specificate nel file yaml dell'ambiente o utilizzare uno degli ambienti acquistati.
    2. Creare una configurazione di inferenza (oggetto InferenceConfig) in base all'ambiente e allo script di assegnazione dei punteggi.

3. Distribuire il modello nel servizio Istanza contenitore di Azure o nel servizio Azure Kubernetes (AKS).

Per altre informazioni su questa procedura, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure.** Se non si dispone di uno, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [L'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)di Azure .
* [L'estensione CLI per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Per eseguire il debug in locale, è necessario disporre di un'installazione Docker funzionante nel sistema locale.

    Per verificare l'installazione di `docker run hello-world` Docker, utilizzare il comando da un terminale o da un prompt dei comandi. Per informazioni sull'installazione di Docker o sulla risoluzione dei problemi relativi agli errori Docker, vedere la documentazione di [Docker](https://docs.docker.com/).

## <a name="before-you-begin"></a>Prima di iniziare

Se si verifica un problema, la prima cosa da fare è suddividere l'attività di distribuzione (descritta in precedenza) in singoli passaggi per isolare il problema.

Supponendo che si stia utilizzando il metodo di distribuzione nuovo/consigliato tramite l'API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) con un oggetto [Environment](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) come parametro di input, il codice può essere suddiviso in tre passaggi principali:

1. Registrare il modello. Di seguito è riportato un codice di esempio:Here is some sample code:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definire la configurazione di inferenza per la distribuzione:Define inference configuration for deployment:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Distribuire il modello usando la configurazione di inferenza creata nel passaggio precedente:Deploy the model using the inference configuration created in the previous step:

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

Se si verificano problemi durante la distribuzione di un modello in ACI o AKS, provare a distribuirlo come servizio Web locale. L'utilizzo di un servizio Web locale semplifica la risoluzione dei problemi. L'immagine Docker contenente il modello viene scaricata e avviata nel sistema locale.

> [!WARNING]
> Le distribuzioni di servizi Web locali non sono supportate per gli scenari di produzione.

Per eseguire la distribuzione in `LocalWebservice.deploy_configuration()` locale, modificare il codice da utilizzare per creare una configurazione di distribuzione. Utilizzare `Model.deploy()` quindi per distribuire il servizio. L'esempio seguente distribuisce un modello (contenuto nella variabile di modello) come servizio Web locale:The following example deploys a model (contained in the model variable) as a local web service:

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

Si noti che se si sta definendo la propria specifica conda YAML, è necessario elencare i valori predefiniti di azureml con la versione >- 1.0.45 come dipendenza pip. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web.

A questo punto, è possibile lavorare con il servizio come di consueto. Ad esempio, il codice seguente illustra l'invio di dati al servizio:For example, the following code demonstrates sending data to the service:

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

Per ulteriori informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per la formazione e](how-to-use-environments.md)la distribuzione . 

### <a name="update-the-service"></a>Aggiornare il servizio

Durante i test locali, potrebbe `score.py` essere necessario aggiornare il file per aggiungere la registrazione o tentare di risolvere eventuali problemi rilevati. Per ricaricare le `score.py` modifiche `reload()`apportate al file, utilizzare . Ad esempio, il codice seguente ricarica lo script per il servizio e quindi vi invia i dati. I dati vengono valutati `score.py` utilizzando il file aggiornato:

> [!IMPORTANT]
> Il `reload` metodo è disponibile solo per le distribuzioni locali. Per informazioni sull'aggiornamento di una distribuzione a un'altra destinazione di calcolo, vedere la sezione relativa all'aggiornamento di [Deploy models](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Lo script viene ricaricato dal `InferenceConfig` percorso specificato dall'oggetto utilizzato dal servizio.

Per modificare il modello, le dipendenze Conda o la configurazione della distribuzione, utilizzate [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). L'esempio seguente aggiorna il modello utilizzato dal servizio:The following example updates the model used by the service:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminare il servizio

Per eliminare il servizio, utilizzare [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Esaminare il registro Docker

È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio. È possibile visualizzare il registro per le distribuzioni ACI, AKS e Local. Nell'esempio seguente viene illustrato come stampare i log.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Errore di avvio del servizio

Dopo che l'immagine è stata compilata correttamente, il sistema tenta di avviare un contenitore utilizzando la configurazione di distribuzione. Nell'ambito del processo di avvio del contenitore, il sistema richiama la funzione `init()` nello script di assegnazione dei punteggi. Se la funzione `init()` contiene eccezioni non rilevate, nel messaggio di errore potrebbe essere visualizzato l'errore **CrashLoopBackOff**.

Usare le informazioni nella sezione [Esaminare il registro Docker](#dockerlog) per controllare i log.

## <a name="function-fails-get_model_path"></a>Errore della funzione: get_model_path()

Spesso, nella `init()` funzione nello script di assegnazione dei punteggi, la funzione [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) viene chiamata per individuare un file di modello o una cartella di file di modello nel contenitore. Se non è possibile trovare il file o la cartella del modello, la funzione ha esito negativo. Il modo più semplice per eseguire il debug di questo errore consiste nell'eseguire il codice Python seguente nella shell del contenitore:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

In questo esempio viene stampato `/var/azureml-app`il percorso locale (relativo a ) nel contenitore in cui lo script di assegnazione del punteggio prevede di trovare il file o la cartella del modello. È quindi possibile verificare se il file o la cartella si trova effettivamente dove dovrebbe essere.

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

**Nota**: la restituzione di messaggi di errore dalla chiamata a `run(input_data)` dovrebbe essere eseguita solo a scopo di debug. Per motivi di sicurezza, è consigliabile non restituire messaggi di errore in questo modo in un ambiente di produzione.

## <a name="http-status-code-502"></a>Codice di stato HTTP 502

Un codice di stato 502 indica che il servizio `run()` ha generato un'eccezione o si è arrestato in modo anomalo nel metodo del file di score.py. Utilizzare le informazioni contenute in questo articolo per eseguire il debug del file.

## <a name="http-status-code-503"></a>Codice di stato HTTP 503

Le distribuzioni del servizio Azure Kubernetes supportano la scalabilità automatica, che consente l'aggiunta di repliche per supportare un carico aggiuntivo. Tuttavia, il ridimensionamento automatico è progettato per gestire le modifiche **graduali** nel carico. Se si ricevono picchi di richieste di grandi dimensioni al secondo, i client potrebbero ricevere un codice di stato HTTP 503.If you receive large spikes in requests per second, clients may receive an HTTP status code 503.

Ci sono due cose che possono aiutare a prevenire i codici di stato 503:

* Modificare il livello di utilizzo in corrispondenza del quale la scalabilità automatica crea nuove repliche.
    
    Per impostazione predefinita, la scalabilità automatica dell'utilizzo della destinazione è impostata su 70%, il che significa che il servizio è in grado di gestire picchi di richieste al secondo (RPS) fino al 30%. È possibile regolare l'obiettivo di utilizzo impostando il `autoscale_target_utilization` valore su un valore inferiore.

    > [!IMPORTANT]
    > Questa modifica non determina la creazione *più rapida delle*repliche. Al contrario, vengono creati a una soglia di utilizzo inferiore. Invece di attendere che il servizio sia utilizzato al 70%, la modifica del valore in 30% causa la creazione di repliche quando si verifica un utilizzo del 30%.
    
    Se il servizio Web sta già utilizzando le repliche max correnti e `autoscale_max_replicas` vengono ancora visualizzato codici di stato 503, aumentare il valore per aumentare il numero massimo di repliche.

* Modificare il numero minimo di repliche. L'aumento delle repliche minime fornisce un pool più grande per gestire i picchi in ingresso.

    Per aumentare il numero minimo `autoscale_min_replicas` di repliche, impostare su un valore superiore. È possibile calcolare le repliche necessarie utilizzando il codice seguente, sostituendo i valori con valori specifici per il progetto:You can calculate the required replicas by using the following code, placing values with values specific to your project:

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
    > Se si ricevono picchi di richiesta superiori a quelli che le nuove repliche minime sono in grado di gestire, è possibile ricevere di nuovo 503s. Ad esempio, con l'aumento del traffico verso il servizio, potrebbe essere necessario aumentare le repliche minime.

Per ulteriori informazioni `autoscale_target_utilization` `autoscale_max_replicas`sull'impostazione di , , e `autoscale_min_replicas` per, vedere le informazioni di riferimento sul modulo [AksWebservice.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)

## <a name="http-status-code-504"></a>Codice di stato HTTP 504

Un codice di stato 504 indica che la richiesta è scaduta. Il timeout predefinito è 1 minuto.

È possibile aumentare il timeout o tentare di velocizzare il servizio modificando il score.py per rimuovere le chiamate non necessarie. Se queste azioni non risolvano il problema, utilizzare le informazioni contenute in questo articolo per eseguire il debug del file di score.py. Il codice può essere in uno stato bloccato o un ciclo infinito.

## <a name="advanced-debugging"></a>Debug avanzato

In alcuni casi, potrebbe essere necessario eseguire il debug interattivo del codice Python contenuto nella distribuzione del modello. Ad esempio, se lo script di immissione non riesce e il motivo non può essere determinato da una registrazione aggiuntiva. Utilizzando Visual Studio Code e Python Tools per Visual Studio (PTVSD), è possibile connettersi al codice in esecuzione all'interno del contenitore Docker.

> [!IMPORTANT]
> Questo metodo di debug non `Model.deploy()` `LocalWebservice.deploy_configuration` funziona quando si utilizza e per distribuire un modello in locale. Al contrario, è necessario creare un'immagine utilizzando il metodo [Model.package().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Le distribuzioni dei servizi Web locali richiedono un'installazione di Docker funzionante nel sistema locale. Per ulteriori informazioni sull'utilizzo di Docker, vedere la documentazione di [Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

1. Per installare Python Tools per Visual Studio (PTVSD) nell'ambiente di sviluppo del codice VS locale, utilizzare il comando seguente:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Per ulteriori informazioni sull'utilizzo di PTVSD con codice VS, vedere [Debug remoto](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Per configurare il codice VS per comunicare con l'immagine Docker, creare una nuova configurazione di debug:To configure VS Code to communicate with the Docker image, create a new debug configuration:

    1. Da Codice VS, selezionare il menu __Debug__ e quindi selezionare __Apri configurazioni__. Viene aperto un file denominato __launch.json.__

    1. Nel file __launch.json__ individuare la `"configurations": [`riga che contiene e inserire il testo seguente dopo di esso:

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
        > Se sono già presenti altre voci nella sezione delle configurazioni, aggiungere una virgola (,) dopo il codice inserito.

        Questa sezione si collega al contenitore Docker utilizzando la porta 5678.

    1. Salvare il file __launch.json.__

### <a name="create-an-image-that-includes-ptvsd"></a>Creare un'immagine che include PTVSD

1. Modificare l'ambiente conda per la distribuzione in modo che includa PTVSD. Nell'esempio seguente viene `pip_packages` illustrata l'aggiunta utilizzando il parametro :

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

1. Per avviare PTVSD e attendere una connessione all'avvio del `score.py` servizio, aggiungere quanto segue all'inizio del file:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Creare un'immagine in base alla definizione dell'ambiente ed eseguire il pull dell'immagine nel Registro di sistema locale. Durante il debug, è possibile apportare modifiche ai file nell'immagine senza doverla ricreare. Per installare un editor di testo (vim) `Environment.docker.base_image` nell'immagine Docker, utilizzare le proprietà e: `Environment.docker.base_dockerfile`

    > [!NOTE]
    > Questo esempio presuppone `ws` che punti all'area di `model` lavoro di Azure Machine Learning e che sia il modello distribuito. Il `myenv.yml` file contiene le dipendenze conda create nel passaggio 1.The file contains the conda dependencies created in step 1.

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

    Una volta che l'immagine è stata creata e scaricata, il percorso dell'immagine (include repository, nome e tag, che in questo caso è anche il suo digest) viene visualizzato in un messaggio simile al seguente:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Per semplificare l'utilizzo dell'immagine, utilizzare il comando seguente per aggiungere un tag. Sostituire `myimagepath` con il valore di posizione del passaggio precedente.

    ```bash
    docker tag myimagepath debug:1
    ```

    Per il resto dei passaggi, è possibile `debug:1` fare riferimento all'immagine locale come anziché il valore del percorso completo dell'immagine.

### <a name="debug-the-service"></a>Eseguire il debug del servizioDebug the service

> [!TIP]
> Se si imposta un timeout per la `score.py` connessione PTVSD nel file, è necessario connettere VS Code alla sessione di debug prima della scadenza del timeout. Avviare il codice VS, `score.py`aprire la copia locale di , impostare un punto di interruzione e renderlo pronto per l'uso prima di eseguire la procedura descritta in questa sezione.
>
> Per ulteriori informazioni sul debug e sull'impostazione dei punti di interruzione, vedere [Debug](https://code.visualstudio.com/Docs/editor/debugging).

1. Per avviare un contenitore Docker utilizzando l'immagine, utilizzare il comando seguente:To start a Docker container using the image, use the following command:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Per collegare il codice VS a PTVSD all'interno del contenitore, aprire il codice VS e utilizzare il tasto F5 o selezionare __Debug__. Quando richiesto, selezionare la configurazione di __Azure Machine Learning: Docker Debug.When__ prompted, select the Azure Machine Learning: Docker Debug configuration. È anche possibile selezionare l'icona di debug dalla barra laterale, la voce __Azure Machine Learning: Docker Debug__ dal menu a discesa Debug e quindi usare la freccia verde per connettere il debugger.

    ![L'icona di debug, il pulsante di avvio del debug e il selettore di configurazione](./media/how-to-troubleshoot-deployment/start-debugging.png)

A questo punto, il codice VS si connette a PTVSD all'interno del contenitore Docker e si arresta in corrispondenza del punto di interruzione impostato in precedenza. È ora possibile eseguire il codice un'istruzione alla volta durante l'esecuzione, visualizzare le variabili e così via.

Per altre informazioni sull'uso del codice VS per eseguire il debug di Python, vedere [Eseguire il debug del codice Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificare i file contenitore

Per apportare modifiche ai file nell'immagine, è possibile collegarsi al contenitore in esecuzione ed eseguire una shell bash. Da lì, è possibile utilizzare vim per modificare i file:

1. Per connettersi al contenitore in esecuzione e avviare una shell bash nel contenitore, utilizzare il comando seguente:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Per trovare i file utilizzati dal servizio, utilizzare il seguente comando dalla shell `/var/azureml-app`bash nel contenitore se la directory predefinita è diversa da:

    ```bash
    cd /var/azureml-app
    ```

    Da qui, è possibile utilizzare `score.py` vim per modificare il file. Per ulteriori informazioni sull'utilizzo di vim, consultate [Utilizzo dell'editor Vim.](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)

1. Le modifiche apportate a un contenitore non vengono in genere rese persistenti. Per salvare le modifiche apportate, utilizzare il comando seguente, prima di uscire dalla shell avviata nel passaggio precedente, ovvero in un'altra shell:

    ```bash
    docker commit debug debug:2
    ```

    Questo comando crea una `debug:2` nuova immagine denominata che contiene le modifiche.

    > [!TIP]
    > È necessario arrestare il contenitore corrente e iniziare a utilizzare la nuova versione prima che le modifiche abbiano effetto.

1. Assicurarsi di mantenere sincronizzate le modifiche apportate ai file nel contenitore con i file locali utilizzati da VS Code. In caso contrario, l'esperienza del debugger non funzionerà come previsto.

### <a name="stop-the-container"></a>Arrestare il contenitore

Per arrestare il contenitore, utilizzare il comando seguente:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: Eseguire il & distribuire i modelliTutorial: Train & deploy models](tutorial-train-models-with-aml.md)
