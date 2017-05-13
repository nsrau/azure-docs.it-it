---
title: 'Esercitazione: Usare la libreria client di Azure Batch per Node.js | Microsoft Docs'
description: Informazioni sui concetti di base di Azure Batch e compilazione di una soluzione semplice con Node.js.
services: batch
author: shwetams
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: nodejs
ms.topic: hero-article
ms.workload: big-compute
ms.date: 04/25/2017
ms.author: shwetams
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 14edac7896ac3aff8788b26906b8a97a35fae60e
ms.contentlocale: it-it
ms.lasthandoff: 05/02/2017

---

# <a name="get-started-with-batch-sdk-for-nodejs"></a>Introduzione all'SDK di Batch per Node.js

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Questo articolo illustra i concetti di base relativi alla compilazione di un client Batch in Node.js con l'[SDK di Azure Batch per Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/). Verrà adottato un approccio graduale che prevede la definizione di uno scenario per un'applicazione batch e quindi la relativa configurazione con un client Node.js.  

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente sappia usare Node.js e abbia familiarità con Linux. Presuppone anche che abbia un account Azure configurato con i diritti di accesso necessari per creare servizi Batch e Archiviazione.

Prima di affrontare i passaggi descritti in questo articolo, è consigliabile vedere la [panoramica tecnica di Azure Batch](batch-technical-overview.md).

## <a name="the-tutorial-scenario"></a>Scenario dell'esercitazione
In questo scenario di flusso di lavoro batch, un semplice script scritto in Python scarica tutti i file CSV da un contenitore di archiviazione BLOB di Azure e li converte in JSON. Per elaborare in parallelo più contenitori dell'account di archiviazione, è possibile distribuire lo script come processo di Azure Batch.

## <a name="azure-batch-architecture"></a>Architettura di Azure Batch
Il diagramma seguente illustra come è possibile ridimensionare lo script Python usando Azure Batch e un client Node.js.

![Scenario di Azure Batch](./media/batch-nodejs-get-started/BatchScenario.png)

Il client Node.js distribuisce un processo batch con un'attività di preparazione (illustrata in dettaglio più avanti) e un set di attività in base al numero di contenitori nell'account di archiviazione. È possibile scaricare gli script dal repository GitHub.

* [Client Node.js](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/storehbaseclient.js)
* [Script della shell per l'attività di preparazione](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh)
* [Processore Python per la conversione da CSV a JSON](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py)

> [!TIP]
> Il client Node.js del collegamento riportato non contiene codice specifico da distribuire come app per le funzioni di Azure. Per istruzioni per crearne una, vedere i collegamenti seguenti.
> - [Creare un'app per le funzioni](../azure-functions/functions-create-first-azure-function.md)
> - [Creare una funzione trigger timer](../azure-functions/functions-bindings-timer.md)
>
>

## <a name="build-the-application"></a>Compilare l'applicazione.

Verrà ora illustrato in modo dettagliato il processo per la compilazione del client Node.js.

### <a name="step-1-install-azure-batch-sdk"></a>Passaggio 1: Installare l'SDK di Azure Batch

È possibile installare l'SDK di Azure Batch per Node.js usando il comando npm install.

`npm install azure-batch`

Questo comando installa l'ultima versione dell'SDK azure-batch per Node.

>[!Tip]
> In un'app per le funzioni di Azure, è possibile passare alla "console Kudu" nella scheda Impostazioni di Funzioni di Azure per eseguire i comandi npm install, in questo caso per installare l'SDK di Azure Batch per Node.js.
>
>

### <a name="step-2-create-an-azure-batch-account"></a>Passaggio 2: Creare un account Azure Batch

È possibile creare l'account dal [portale di Azure](batch-account-create-portal.md) o dalla riga di comando ([Powershell](batch-powershell-cmdlets-get-started.md) /[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview)).

Di seguito sono riportati i comandi per creare un account tramite l'interfaccia della riga di comando di Azure.

Creare un gruppo di risorse. Ignorare questo passaggio se è già disponibile un gruppo di risorse in cui creare l'account Batch.

`az group create -n "<resource-group-name>" -l "<location>"`

Creare quindi un account Azure Batch.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Ogni account Batch ha chiavi di accesso corrispondenti, necessarie per creare altre risorse nell'account Azure Batch. Nell'ambiente di produzione è consigliabile usare Azure Key Vault per archiviare tali chiavi. È quindi possibile creare un'entità servizio per l'applicazione. Con questa entità servizio, l'applicazione può creare un token OAuth per accedere alle chiavi dall'insieme di credenziali delle chiavi.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Copiare e archiviare la chiave da usare nei passaggi successivi.

### <a name="step-3-create-an-azure-batch-service-client"></a>Passaggio 3: Creare un client del servizio Azure Batch
Il frammento di codice seguente importa il modulo Node.js azure-batch e quindi crea un client del servizio Batch. È necessario prima di tutto creare un oggetto SharedKeyCredentials con la chiave dell'account Batch copiata nel passaggio precedente.

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

L'URI di Azure Batch è riportato nella scheda Panoramica del portale di Azure, nel formato seguente:

`https://accountname.location.batch.azure.com`

Fare riferimento allo screenshot:

![URI di Azure Batch](./media/batch-nodejs-get-started/azurebatchuri.png)



### <a name="step-4-create-an-azure-batch-pool"></a>Passaggio 4: Creare un pool di Azure Batch
Un pool di Azure Batch è costituito da più VM, dette anche nodi Batch. Il servizio Azure Batch distribuisce le attività in questi nodi e li gestisce. È possibile definire i parametri di configurazione per il pool.

* Tipo di immagine di macchina virtuale
* Dimensioni dei nodi macchina virtuale
* Numero di nodi macchina virtuale

> [!Tip]
> Le dimensioni e il numero dei nodi macchina virtuale dipendono in gran parte dal numero di attività che si vuole eseguire in parallelo e anche dall'attività stessa. È consigliabile eseguire test per determinare il numero e le dimensioni ideali.
>
>

Il frammento di codice seguente crea gli oggetti dei parametri di configurazione.

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!Tip]
> Per l'elenco delle immagini di VM Linux disponibili per Azure Batch e dei relativi ID SKU, vedere [Elenco di immagini di macchine virtuali](batch-linux-nodes.md#list-of-virtual-machine-images).
>
>

Dopo aver definito la configurazione del pool, è possibile creare il pool di Azure Batch. Il comando per il pool di Batch crea i nodi macchina virtuale di Azure e li prepara in modo che siano pronti a ricevere le attività da eseguire. Ogni pool dovrà avere un ID univoco a cui fare riferimento nei passaggi successivi.

Il frammento di codice seguente crea un pool di Azure Batch.

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicated:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

È possibile controllare lo stato del pool creato e verificare che sia "active" prima di procedere con l'invio di un processo al pool.

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");    

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Di seguito è riportato un oggetto risultato di esempio restituito dalla funzione pool.get.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  maxTasksPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```


### <a name="step-4-submit-an-azure-batch-job"></a>Passaggio 5: Inviare un processo di Azure Batch
Un processo di Azure Batch è un gruppo logico di attività simili. In questo scenario, esegue l'elaborazione da CSV a JSON. Ogni attività potrà elaborare i file CSV presenti in ogni contenitore di archiviazione di Azure.

Queste attività verranno eseguite in parallelo e distribuite in più nodi, orchestrati dal servizio Azure Batch.

> [!Tip]
> È possibile usare la proprietà [maxTasksPerNode](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) per specificare il numero massimo di attività che è possibile eseguire simultaneamente in un singolo nodo.
>
>

#### <a name="preparation-task"></a>Attività di preparazione

I nodi VM creati sono nodi Ubuntu vuoti. Spesso è necessario installare un set di programmi come prerequisito.
Per i nodi Linux è in genere possibile usare uno script della shell che installa i prerequisiti prima dell'esecuzione delle attività effettive. Può tuttavia trattarsi di qualsiasi eseguibile programmabile.
Lo [script della shell](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) di questo esempio installa Python-pip e Azure Storage SDK per Python.

È possibile caricare lo script in un account di archiviazione di Azure e generare un URI di firma di accesso condiviso per accedere allo script. Questo processo può essere automatizzato con Azure Storage SDK per Node.js.

> [!Tip]
> Un'attività di preparazione per un processo viene eseguita solo sui nodi VM in cui deve essere eseguita l'attività specifica. Se si vogliono installare i prerequisiti in tutti i nodi indipendentemente dalle attività in essi eseguite, si può usare la proprietà [startTask](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) durante l'aggiunta di un pool. È possibile usare la definizione di attività di preparazione seguente come riferimento.
>
>

Un'attività di preparazione viene specificata durante l'invio del processo di Azure Batch. Di seguito sono riportati i parametri di configurazione dell'attività di preparazione.

* **ID**: identificatore univoco dell'attività di preparazione.
* **commandLine**: riga di comando per eseguire l'eseguibile dell'attività.
* **resourceFiles**: matrice di oggetti che specifica i dettagli dei file da scaricare per l'esecuzione dell'attività.  Di seguito sono riportate le opzioni.
    - blobSource: URI di firma di accesso condiviso del file
    - filePath: percorso locale per il download e il salvataggio del file
    - fileMode: applicabile solo per nodi Linux, in formato ottale con valore predefinito 0770
* **waitForSuccess**: se è impostato su true, l'attività non viene eseguita in caso di errori dell'attività di preparazione.
* **runElevated**: impostare su true se sono necessari privilegi elevati per eseguire l'attività.

Il frammento di codice seguente illustra un esempio di configurazione dello script dell'attività di preparazione:

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Se per l'esecuzione delle attività non è necessario installare prerequisiti, è possibile ignorare le attività di preparazione. Il codice seguente crea un processo con il nome visualizzato "process csv files".

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```


### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>Passaggio 6: Inviare le attività di Azure Batch per un processo

Dopo aver creato il processo per l'elaborazione dei file CSV, si creeranno le attività per tale processo. Supponendo di avere quattro contenitori, è necessario creare quattro attività, una per ogni contenitore.

Lo [script Python](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py) accetta due parametri.

* container_name: contenitore di archiviazione da cui scaricare i file
* pattern: parametro facoltativo del modello di nome file

Supponendo di avere i quattro contenitori "con1", "con2", "con3" e "con4", il codice seguente illustra l'invio delle attività al processo "process csv" di Azure Batch creato in precedenza.

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){           

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);     
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

Il codice aggiunge più attività al pool. Ogni attività viene eseguita in un nodo del pool di VM creato. Se il numero di attività è superiore al numero di VM in un pool o alla proprietà maxTasksPerNode, le attività restano in attesa finché non diventa disponibile un nodo. Questa orchestrazione viene gestita automaticamente da Azure Batch.

Il portale offre visualizzazioni dettagliate delle attività e degli stati del processo. È anche possibile usare le funzioni list e get in Azure SDK per Node. Per informazioni dettagliate, vedere la [documentazione](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html).

## <a name="next-steps"></a>Passaggi successivi

- Se non si ha familiarità con il servizio, è consigliabile vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md) .
- Per esplorare l'API Batch, vedere le [informazioni di riferimento su Node.js per Batch](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/).


