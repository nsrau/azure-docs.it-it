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
ms.date: 08/06/2020
ms.topic: troubleshooting
ms.custom: contperfq4, devx-track-python, deploy
ms.openlocfilehash: 259b5c789d2323dbc797116cf0d09045811a6873
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073343"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Risolvere i problemi di distribuzione Docker dei modelli con il servizio Azure Kubernetes e le istanze di contenitore di Azure 

Informazioni su come risolvere e risolvere gli errori comuni di distribuzione Docker con istanze di contenitore di Azure (ACI) e Azure Kubernetes Service (AKS) con Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).
* [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Per eseguire il debug localmente, è necessario disporre di un'installazione Docker funzionante nel sistema locale.

    Per verificare l'installazione Docker, usare il comando `docker run hello-world` da un terminale o da un prompt dei comandi. Per informazioni sull'installazione Docker o sulla risoluzione dei problemi relativi agli errori Docker, vedere la [Documentazione di Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Passaggi per la distribuzione di Docker dei modelli di Machine Learning

Quando si distribuisce un modello in Azure Machine Learning, si usano l'API [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) e un oggetto [Environment](how-to-use-environments.md) . Il servizio crea un'immagine Docker di base durante la fase di distribuzione e monta i modelli richiesti in una sola chiamata. Le attività di distribuzione di base sono le seguenti:

1. Registrare il modello nel registro dei modelli dell'area di lavoro.

2. Definire la configurazione dell'inferenza:
    1. Creare un oggetto [ambiente](how-to-use-environments.md) . Questo oggetto può usare le dipendenze in un file YAML dell'ambiente, uno degli ambienti curati.
    2. Creare una configurazione dell'inferenza (oggetto InferenceConfig) in base all'ambiente e allo script di assegnazione dei punteggi.

3. Distribuire il modello nel servizio Istanze di Azure Container (ACI) o nel servizio Azure Kubernetes (AKS).

Per altre informazioni su questa procedura, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Prima di iniziare

Se si verifica un problema, la prima cosa da fare è suddividere l'attività di distribuzione (descritta in precedenza) in singoli passaggi per isolare il problema.

Quando si usa [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) con un oggetto [Environment](how-to-use-environments.md) come parametro di input, il codice può essere suddiviso in tre passaggi principali:

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

Suddividere il processo di distribuzione in singole attività consente di identificare più facilmente alcuni degli errori più comuni.

## <a name="debug-locally"></a>Eseguire il debug in locale

Se si verificano problemi durante la distribuzione di un modello in ACI o AKS, distribuirlo come servizio Web locale. L'utilizzo di un servizio Web locale rende più semplice la risoluzione dei problemi.

Per esplorare un esempio eseguibile, è possibile trovare un [notebook di distribuzione locale](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) di esempio nel repository  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) .

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

Se si definisce una specifica conda specifica YAML, List azureml-defaults Version >= 1.0.45 come dipendenza PIP. Questo pacchetto è necessario per ospitare il modello come servizio Web.

A questo punto, è possibile usare il servizio come di consueto. Nel codice seguente viene illustrato l'invio di dati al servizio:

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
> Il metodo `reload` è disponibile solo per le distribuzioni locali. Per informazioni sull'aggiornamento di una distribuzione di a un'altra destinazione di calcolo, vedere [How to Update your webservice](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Lo script viene ricaricato dalla posizione specificata dall'oggetto `InferenceConfig` usato dal servizio.

Per modificare il modello, le dipendenze Conda o la configurazione della distribuzione, usare [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueupdate--args-). Nell'esempio seguente viene aggiornato il modello usato dal servizio:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminare il servizio

Per eliminare il servizio, usare [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Esaminare il log Docker

È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio. È possibile visualizzare il log per ACI, AKS e distribuzioni locali. L'esempio seguente illustra come stampare i log.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Se la riga si `Booting worker with pid: <pid>` verifica più volte nei log, significa che non è disponibile memoria sufficiente per avviare il thread di lavoro.
È possibile risolvere l'errore aumentando il valore di `memory_gb` in `deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>Non è possibile pianificare il contenitore

Quando si distribuisce un servizio in una destinazione di calcolo del servizio Kubernetes di Azure, Azure Machine Learning tenterà di pianificare il servizio con la quantità di risorse richiesta. Se non sono disponibili nodi nel cluster con la quantità appropriata di risorse dopo 5 minuti, la distribuzione avrà esito negativo. Il messaggio di errore è `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Per risolvere questo errore, è possibile aggiungere più nodi, modificare lo SKU dei nodi o modificare i requisiti di risorse del servizio. 

Il messaggio di errore indicherà in genere la risorsa necessaria. ad esempio, se viene visualizzato un messaggio di errore `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` che indica che il servizio richiede GPU e sono presenti tre nodi nel cluster che non hanno GPU disponibili. È possibile risolvere questo problema aggiungendo altri nodi se si usa uno SKU GPU, passando a uno SKU abilitato per GPU in caso contrario, o modificando l'ambiente in modo da non richiedere GPU.  

## <a name="service-launch-fails"></a>Errore di avvio del servizio

Dopo aver creato correttamente l'immagine, il sistema tenta di avviare un contenitore usando la configurazione della distribuzione. Nell'ambito del processo di avvio del contenitore, il sistema richiama la funzione `init()` nello script di assegnazione dei punteggi. Se la funzione `init()` contiene eccezioni non rilevate, nel messaggio di errore potrebbe essere visualizzato l'errore **CrashLoopBackOff**.

Usare le informazioni nella sezione [Esaminare il log di Docker](#dockerlog) per controllare i log.

## <a name="function-fails-get_model_path"></a>Errore della funzione: get_model_path()

Nella funzione `init()` dello script di assegnazione dei punteggi viene spesso chiamata la funzione [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) per individuare un file di modello o una cartella di file di modello nel contenitore. Se non è possibile trovare il file o la cartella di modello, la funzione ha esito negativo. Il modo più semplice per eseguire il debug di questo errore consiste nell'eseguire il codice Python seguente nella shell del contenitore:

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

Le distribuzioni del servizio Azure Kubernetes supportano il ridimensionamento automatico, che consente di aggiungere repliche per supportare un carico aggiuntivo. Il ridimensionamento automatico è progettato per gestire modifiche **graduali** del carico. Se si ricevono picchi elevati di richieste al secondo, i client potrebbero ricevere un codice di stato HTTP 503. Sebbene il ridimensionamento automatico reagisca rapidamente, il tempo necessario per la creazione di contenitori aggiuntivi richiede AKS.

Le decisioni per la scalabilità verticale e orizzontale sono basate sull'utilizzo delle repliche del contenitore correnti. Il numero di repliche occupate (elaborazione di una richiesta) divise per il numero totale di repliche correnti è l'utilizzo corrente. Se questo numero è superiore a `autoscale_target_utilization` , vengono create altre repliche. Se è inferiore, le repliche vengono ridotte. Le decisioni di aggiunta di repliche sono ansiose e veloci (circa 1 secondo). Le decisioni di rimozione delle repliche sono conservative (circa 1 minuto). Per impostazione predefinita, l'utilizzo della destinazione per la scalabilità automatica è impostato su **70%**, il che significa che il servizio è in grado di gestire picchi di richieste al secondo (RPS) **fino al 30%**.

Esistono due elementi che consentono di prevenire codici di stato 503:

> [!TIP]
> Questi due approcci possono essere usati singolarmente o in combinazione.

* Modificare il livello di utilizzo a cui il ridimensionamento automatico crea nuove repliche. È possibile regolare la destinazione di utilizzo impostando `autoscale_target_utilization` su un valore inferiore.

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

Per altre informazioni sull'impostazione di `autoscale_target_utilization`, `autoscale_max_replicas` e `autoscale_min_replicas`, vedere il riferimento al modulo [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true).

## <a name="http-status-code-504"></a>Codice di stato HTTP 504

Un codice di stato 504 indica che si è verificato il timeout della richiesta. Il timeout predefinito è 1 minuto.

È possibile aumentare il timeout o provare ad accelerare il servizio modificando il file score.py per rimuovere le chiamate non necessarie. Se queste azioni non consentono di risolvere il problema, usare le informazioni in questo articolo per eseguire il debug del file score.py. Il codice potrebbe trovarsi in uno stato non reattivo o in un ciclo infinito.

## <a name="advanced-debugging"></a>Debug avanzato

Potrebbe essere necessario eseguire il debug interattivo del codice Python contenuto nella distribuzione del modello. Ad esempio, se lo script di immissione ha esito negativo e il motivo non può essere determinato da una registrazione aggiuntiva. Usando Visual Studio Code e debugpy, è possibile connettersi al codice in esecuzione all'interno del contenitore docker.

Per ulteriori informazioni, vedere il [debug interattivo in vs Code Guida](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Forum dell'utente per la distribuzione di modelli](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: Eseguire il training e la distribuzione di modelli](tutorial-train-models-with-aml.md)
