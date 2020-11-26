---
title: Risolvere i problemi di distribuzione del servizio Web remoto
titleSuffix: Azure Machine Learning
description: Informazioni su come aggirare, risolvere e risolvere i problemi relativi agli errori di distribuzione comuni di Docker con il servizio Azure Kubernetes e le istanze di contenitore di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperfq4, devx-track-python, deploy, contperfq2
ms.openlocfilehash: 0b8da0be16adc79b606b59f394b223b001453607
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185063"
---
# <a name="troubleshoot-model-deployment"></a>Risolvere i problemi di distribuzione del modello

Informazioni su come risolvere e risolvere gli errori comuni di distribuzione di Docker remoti con istanze di contenitore di Azure (ACI) e Azure Kubernetes Service (AKS) usando Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Passaggi per la distribuzione di Docker dei modelli di Machine Learning

Quando si distribuisce un modello in un calcolo non locale in Azure Machine Learning, si verifica quanto segue:

1. Il Dockerfile specificato nell'oggetto ambienti in InferenceConfig viene inviato al cloud, insieme al contenuto della directory di origine
1. Se un'immagine compilata in precedenza non è disponibile nel registro contenitori, una nuova immagine Docker viene compilata nel cloud e archiviata nel registro contenitori predefinito dell'area di lavoro.
1. L'immagine Docker dal registro contenitori viene scaricata nella destinazione di calcolo.
1. L'archivio BLOB predefinito dell'area di lavoro viene montato nella destinazione di calcolo, consentendo l'accesso ai modelli registrati
1. Il server Web viene inizializzato eseguendo la funzione dello script di immissione `init()`
1. Quando il modello distribuito riceve una richiesta, la `run()` funzione gestisce la richiesta

La differenza principale quando si usa una distribuzione locale è che l'immagine del contenitore è compilata nel computer locale, motivo per cui è necessario che Docker sia installato per una distribuzione locale.

Per comprendere i casi in cui si verificano errori, è necessario comprendere questi passaggi di alto livello.

## <a name="get-deployment-logs"></a>Ottenere i log di distribuzione

Il primo passaggio per il debug degli errori consiste nell'ottenere i log di distribuzione. Per prima cosa, seguire le istruzioni riportate [qui](how-to-deploy-and-where.md#connect-to-your-workspace) per connettersi all'area di lavoro.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

Per ottenere i log da un WebService distribuito, eseguire le operazioni seguenti:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Supponendo che si disponga di un oggetto di tipo `azureml.core.Workspace` denominato `ws` , è possibile eseguire le operazioni seguenti:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Eseguire il debug in locale

Se si verificano problemi durante la distribuzione di un modello in ACI o AKS, distribuirlo come servizio Web locale. L'utilizzo di un servizio Web locale rende più semplice la risoluzione dei problemi. Per risolvere i problemi relativi a una distribuzione in locale, vedere l' [articolo sulla risoluzione dei problemi locali](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>Non è possibile pianificare il contenitore

Quando si distribuisce un servizio in una destinazione di calcolo del servizio Kubernetes di Azure, Azure Machine Learning tenterà di pianificare il servizio con la quantità di risorse richiesta. Se non sono disponibili nodi nel cluster con la quantità appropriata di risorse dopo 5 minuti, la distribuzione avrà esito negativo. Il messaggio di errore è `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Per risolvere questo errore, è possibile aggiungere più nodi, modificare lo SKU dei nodi o modificare i requisiti di risorse del servizio. 

Il messaggio di errore indicherà in genere la risorsa necessaria. ad esempio, se viene visualizzato un messaggio di errore `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` che indica che il servizio richiede GPU e sono presenti tre nodi nel cluster che non hanno GPU disponibili. È possibile risolvere questo problema aggiungendo altri nodi se si usa uno SKU GPU, passando a uno SKU abilitato per GPU in caso contrario, o modificando l'ambiente in modo da non richiedere GPU.  

## <a name="service-launch-fails"></a>Errore di avvio del servizio

Dopo aver creato correttamente l'immagine, il sistema tenta di avviare un contenitore usando la configurazione della distribuzione. Nell'ambito del processo di avvio del contenitore, il sistema richiama la funzione `init()` nello script di assegnazione dei punteggi. Se la funzione `init()` contiene eccezioni non rilevate, nel messaggio di errore potrebbe essere visualizzato l'errore **CrashLoopBackOff**.

Usare le informazioni nell'articolo [esaminare il registro Docker](how-to-troubleshoot-deployment-local.md#dockerlog) .

## <a name="function-fails-get_model_path"></a>Errore della funzione: get_model_path()

Nella funzione `init()` dello script di assegnazione dei punteggi viene spesso chiamata la funzione [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) per individuare un file di modello o una cartella di file di modello nel contenitore. Se non è possibile trovare il file o la cartella di modello, la funzione ha esito negativo. Il modo più semplice per eseguire il debug di questo errore consiste nell'eseguire il codice Python seguente nella shell del contenitore:

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

Per altre informazioni sull'impostazione di `autoscale_target_utilization`, `autoscale_max_replicas` e `autoscale_min_replicas`, vedere il riferimento al modulo [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py).

## <a name="http-status-code-504"></a>Codice di stato HTTP 504

Un codice di stato 504 indica che si è verificato il timeout della richiesta. Il timeout predefinito è 1 minuto.

È possibile aumentare il timeout o provare ad accelerare il servizio modificando il file score.py per rimuovere le chiamate non necessarie. Se queste azioni non consentono di risolvere il problema, usare le informazioni in questo articolo per eseguire il debug del file score.py. Il codice potrebbe trovarsi in uno stato non reattivo o in un ciclo infinito.

## <a name="advanced-debugging"></a>Debug avanzato

Potrebbe essere necessario eseguire il debug interattivo del codice Python contenuto nella distribuzione modello. Ad esempio, se lo script di immissione ha esito negativo e il motivo non può essere determinato da una registrazione aggiuntiva. Usando Visual Studio Code e debugpy, è possibile connettersi al codice in esecuzione all'interno del contenitore docker.

Per ulteriori informazioni, vedere il [debug interattivo in vs Code Guida](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Forum dell'utente per la distribuzione di modelli](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione:

* [Come e dove distribuire modelli](how-to-deploy-and-where.md)
* [Esercitazione: Eseguire il training e la distribuzione di modelli](tutorial-train-models-with-aml.md)
* [Come eseguire ed eseguire il debug di esperimenti localmente](./how-to-debug-visual-studio-code.md)