---
title: Inviare un numero elevato di attività - Azure Batch | Microsoft Docs
description: Come inviare in modo efficiente un numero elevato di attività in un singolo processo di Azure Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3c683b24db2899ee680988c7bedc760d6bb8ec73
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052866"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Inviare un numero elevato di attività a un processo di Batch

Quando si eseguono carichi di lavoro di Azure Batch su larga scala, può essere necessario inviare decine o centinaia di migliaia di attività, o anche di più, a un unico processo. 

Questo articolo contiene linee guida e alcuni esempi di codice per inviare un numero elevato di attività con una velocità effettiva notevolmente maggiore a un unico processo di Batch. Dopo l'invio, le attività vengono inserite nella coda di Batch per l'elaborazione nel pool specificato per il processo.

## <a name="use-task-collections"></a>Usare le raccolte di attività

Le API di Batch offrono metodi per aggiungere in modo efficiente le attività a un processo come *raccolta*, oltre che individualmente. Quando si aggiunge un numero elevato di attività, è consigliabile usare metodi o overload appropriati per aggiungere le attività come raccolta. In genere una raccolta di attività viene creata definendo le attività mentre si scorre un set di file di input o parametri per il processo.

Le dimensioni massime della raccolta di attività che è possibile aggiungere in una singola chiamata dipendono dall'API di Batch usata:

* Le API di Batch seguenti limitano la raccolta a **100 attività**. Il limite può essere inferiore a seconda delle dimensioni delle attività, ad esempio nel caso in cui l'attività includa un numero elevato di file di risorse o di variabili di ambiente.

    * [API REST](/rest/api/batchservice/task/addcollection)
    * [API Python](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#azure_batch_operations_TaskOperations_add_collection)
    * [API Node.js](/javascript/api/azure-batch/task?view=azure-node-latest#addcollection)

  Quando si usano queste API, è necessario specificare la logica per dividere il numero di attività in modo da rispettare il limite della raccolta e per gestire errori e tentativi in caso di errore durante l'aggiunta delle attività. Se una raccolta di attività è troppo grande per essere aggiunta, la richiesta genera un errore ed è necessario riprovare con un numero inferiore di attività.

* Le API seguenti supportano raccolte di attività molto più grandi, limitate solo dalla disponibilità di RAM nel client richiedente. Queste API gestiscono in modo trasparente la divisione della raccolta di attività in "blocchi" per le API di livello inferiore e i tentativi in caso di errore durante l'aggiunta delle attività.

    * [API .NET](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [API Java](/java/api/com.microsoft.azure.batch.protocol._tasks.addcollectionasync?view=azure-java-stable)
    * [Estensione dell'interfaccia della riga di comando di Azure Batch](batch-cli-templates.md) con i modelli dell'interfaccia della riga di comando di Batch
    * [Estensione di Python SDK](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Aumentare la velocità effettiva dell'invio di attività

L'aggiunta di un numero elevato di attività a un processo può richiedere del tempo, ad esempio fino a 1 minuto per aggiungere 20.000 attività tramite l'API .NET. A seconda dell'API di Batch e del carico di lavoro, è possibile migliorare la velocità effettiva delle attività modificando uno o più elementi seguenti:

* **Dimensioni delle attività**: l'aggiunta di attività di grandi dimensioni richiede più tempo rispetto a quelle più piccole. Per ridurre le dimensioni di ogni attività in una raccolta, è possibile semplificare la riga di comando dell'attività, ridurre il numero delle variabili di ambiente o gestire i requisiti per l'esecuzione dell'attività in modo più efficiente. Anziché usare un numero elevato di file di risorse, è possibile ad esempio installare le dipendenze delle attività usando un'[attività di avvio](batch-api-basics.md#start-task) nel pool oppure usare un [pacchetto dell'applicazione](batch-application-packages.md) o un [contenitore Docker](batch-docker-container-workloads.md).

* **Numero di operazioni parallele**: a seconda dell'API di Batch, è possibile migliorare la velocità effettiva aumentando il numero massimo di operazioni simultanee del client di Batch. Configurare questa impostazione usando la proprietà dell'API .NET [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) o il parametro `threads` dei metodi, ad esempio [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#add-collection), nell'estensione di Python SDK di Batch. Questa proprietà non è disponibile nella versione nativa di Python SDK di Batch. Per impostazione predefinita, questa proprietà è impostata su 1, ma è possibile impostarla su un valore superiore per migliorare la velocità effettiva delle operazioni. L'aumento della velocità effettiva comporta un maggiore utilizzo della larghezza di banda della rete e delle prestazioni della CPU. La velocità effettiva delle attività aumenta fino a 100 volte rispetto a `MaxDegreeOfParallelism` o `threads`. In pratica, è consigliabile impostare un numero di operazioni simultanee inferiore a 100. 
 
  L'estensione dell'interfaccia della riga di comando di Azure Batch con i modelli di Batch aumenta automaticamente il numero di operazioni simultanee in base al numero di core disponibili, ma questa proprietà non è configurabile nell'interfaccia della riga di comando. 

* **Limiti di connessione HTTP**: il numero di connessioni HTTP simultanee può limitare le prestazioni del client di Batch quando viene aggiunto un numero elevato di attività. Il numero di connessioni HTTP è limitato con determinate API. Durante lo sviluppo con l'API .NET, ad esempio, la proprietà [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) è impostata su 2 per impostazione predefinita. È consigliabile aumentare il valore a un numero prossimo o superiore al numero di operazioni parallele.

## <a name="example-batch-net"></a>Esempio: Batch .NET

I frammenti di codice C# seguenti mostrano le impostazioni di configurazione per l'aggiunta di un numero elevato di attività usando l'API .NET di Batch.

Per migliorare la velocità effettiva delle attività, aumentare il valore della proprietà [MaxDegreeofParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) dell'oggetto [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Ad esempio: 

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Aggiungere una raccolta di attività al processo usando l'overload appropriato del metodo [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) o [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
). Ad esempio: 

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Esempio: estensione dell'interfaccia della riga di comando di Batch

Tramite l'uso delle estensioni dell'interfaccia della riga di comando di Azure Batch con i [modelli dell'interfaccia della riga di comando di Batch](batch-cli-templates.md), è possibile creare un file JSON del modello di processo che includa una [factory delle attività](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). La factory delle attività consente di configurare una raccolta di attività correlate per un processo da una singola definizione di attività.  

Di seguito è riportato un modello di processo di esempio per un processo di sweep parametrico unidimensionale con un numero elevato di attività, in questo caso 250.000. La riga di comando dell'attività è un semplice comando `echo`.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
Per eseguire un processo con il modello, vedere [Usare il trasferimento di file e i modelli dell'interfaccia della riga di comando di Azure Batch](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Esempio: estensione di Python SDK di Batch

Per usare l'estensione di Python SDK di Azure Batch, installare prima Python SDK e la relativa estensione:

```
pip install azure-batch
pip install azure-batch-extensions
```

Configurare un oggetto `BatchExtensionsClient` che usi l'estensione dell'SDK:

```python

client = batch.BatchExtensionsClient(base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Creare una raccolta di attività da aggiungere a un processo. Ad esempio: 


```python
tasks=list()
# Populate the list with your tasks
...

```

Aggiungere la raccolta di attività tramite [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python#add-collection). Impostare il parametro `threads` per aumentare il numero di operazioni simultanee:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

L'estensione di Python SDK di Batch supporta anche l'aggiunta dei parametri di attività a un processo tramite una specifica JSON per una factory delle attività. Configurare ad esempio i parametri di processo per uno sweep parametrico simile a quello dell'esempio di [modello dell'interfaccia della riga di comando di Batch](#example-batch-cli-template) riportato in precedenza:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Aggiungere i parametri al processo. Impostare il parametro `threads` per aumentare il numero di operazioni simultanee:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'uso dell'estensione dell'interfaccia della riga di comando di Azure Batch con i [modelli dell'interfaccia della riga di comando di Batch](batch-cli-templates.md).
* Altre informazioni sull'[estensione di Python SDK di Batch](https://pypi.org/project/azure-batch-extensions/).