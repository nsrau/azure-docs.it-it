---
title: Carichi di lavoro del contenitore in Azure Batch | Microsoft Docs
description: Informazioni su come eseguire le applicazioni dalle immagini del contenitore in Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 08/09/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: a7de2ba66ccfb5e3f3bce688e68698d90fe2eaf6
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941327"
---
# <a name="run-container-applications-on-azure-batch"></a>Eseguire le applicazioni del contenitore in Azure Batch

Azure Batch consente di eseguire e ridimensionare numerosi processi di batch computing in Azure. Le attività di Batch possono essere eseguite direttamente sulle macchine virtuali (nodi) in un pool di Batch, ma è anche possibile configurare un pool di Batch che esegua le attività in contenitori compatibili con Docker nei nodi. Questo articolo illustra come creare un pool di nodi di calcolo che supporti l'esecuzione di attività del contenitore e quindi eseguire le attività del contenitore nel pool. 

È consigliabile avere familiarità con i concetti relativi ai contenitori e alla creazione di un pool e un processo di Batch. Gli esempi di codice usano gli SDK di .NET e Python per Batch. È anche possibile usare altri SDK per Batch e strumenti, incluso il portale di Azure, per creare pool di Batch abilitati per il contenitore ed eseguire le attività del contenitore.

## <a name="why-use-containers"></a>Perché usare i contenitori

L'uso dei contenitori consente di eseguire le attività Batch in modo semplice senza dover gestire un ambiente e le dipendenze per eseguire le applicazioni. I contenitori distribuiscono le applicazioni come unità leggere, portatili e autosufficienti che possono essere eseguite in più ambienti diversi. Ad esempio, è possibile compilare e testare in locale un contenitore, quindi caricare l'immagine del contenitore in un registro di Azure o altrove. Il modello di distribuzione del contenitore assicura che l'ambiente di runtime dell'applicazione venga sempre installato e configurato correttamente, ovunque si ospiti l'applicazione. Le attività basate sul contenitore in Batch traggono vantaggio anche dalle funzionalità di attività non del contenitore, inclusi i pacchetti dell'applicazione e la gestione dei file di risorse e di output. 

## <a name="prerequisites"></a>Prerequisiti

* **Versioni di SDK**: gli SDK di Batch supportano le immagini del contenitore nelle versioni seguenti:
    * API REST di Batch, versione: 2017-09-01.6.0
    * .NET SDK di Batch, versione 8.0.0
    * Python SDK di Batch, versione 4.0
    * Java SDK di Batch, versione 3.0
    * Node.js SDK di Batch, versione 3.0

* **Account**: nella sottoscrizione di Azure è necessario creare un account Batch e, facoltativamente, un account di archiviazione di Azure.

* **Un'immagine di macchina virtuale supportata**: i contenitori sono supportati solo nei pool creati con Configurazione macchina virtuale, dalle immagini illustrate in dettaglio nella sezione seguente, "Immagini di macchine virtuali supportate". Se si fornisce un'immagine personalizzata, vedere le considerazioni nella sezione seguente e i requisiti in [Usare un'immagine personalizzata gestita per creare un pool di macchine virtuali](batch-custom-images.md). 

### <a name="limitations"></a>Limitazioni

* Batch offre supporto RDMA solo per i contenitori in esecuzione nei pool di Linux

* Per i carichi di lavoro di contenitori Windows, è consigliabile scegliere una dimensione di macchina virtuale multicore per il pool

## <a name="supported-virtual-machine-images"></a>Immagini di macchine virtuali supportate

Per creare un pool di nodi di calcolo della macchina virtuale per i carichi di lavoro del contenitore, usare una delle immagini di Windows o Linux supportate seguenti. Per altre informazioni sulle immagini del Marketplace compatibili con Batch, vedere [Elenco di immagini di macchine virtuali](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Immagini di Windows

Per i carichi di lavoro dei contenitori Windows, Batch attualmente supporta l'immagine **Windows Server 2016 Datacenter con contenitori** di Azure Marketplace. In Windows sono supportate solo le immagini del contenitore Docker.

È anche possibile creare immagini personalizzate da VM che eseguono Docker in Windows.

### <a name="linux-images"></a>Immagini di Linux

Per i carichi di lavoro dei contenitori Linux, Batch attualmente supporta le immagini Linux seguenti pubblicate da Microsoft Azure Batch in Azure Marketplace:

* **CentOS per pool di contenitori di Azure Batch**

* **CentOS (con driver RDMA) per pool di contenitori di Azure Batch**

* **Server Ubuntu per pool di contenitori di Azure Batch**

* **Server Ubuntu (con driver RDMA) per pool di contenitori di Azure Batch**

Queste immagini possono essere usate solo nei pool di Azure Batch. Forniscono:

* Runtime del contenitore [Moby](https://github.com/moby/moby) preinstallato 

* Driver GPU con tecnologia NVIDIA preinstallati, per semplificare la distribuzione nelle VM di Azure serie N

* Una scelta di immagini con o senza driver RDMA pre-installati. Questi driver consentono ai nodi del pool di accedere alla rete RDMA di Azure in caso di distribuzione in dimensioni delle macchine virtuali con supporto per RDMA. 

È anche possibile creare immagini personalizzate da VM che eseguono Docker in una delle distribuzioni Linux compatibili con Batch. Se si sceglie di inserire l'immagine personalizzata di Linux, vedere le istruzioni in [Usare un'immagine personalizzata gestita per creare un pool di macchine virtuali](batch-custom-images.md).

Per il supporto Docker in un'immagine personalizzata, installare [Docker Community Edition (CE)](https://www.docker.com/community-edition) o [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Altre considerazioni sull'uso di un'immagine Linux personalizzata:

* Per sfruttare le prestazioni della GPU delle dimensioni serie N di Azure quando si usa un'immagine personalizzata, preinstallare i driver NVIDIA. È anche necessario installare Docker Engine Utility per le GPU NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Per accedere alla rete RDMA di Azure, usare una dimensione di VM idonea per RDMA. I driver RDMA necessari vengono installati nelle immagini CentOS HPC e Ubuntu supportate da Batch. Potrebbe essere necessaria una configurazione aggiuntiva per eseguire carichi di lavoro MPI. Vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Configurazione del contenitore per il pool di Batch

Per abilitare un pool Batch a eseguire carichi di lavoro del contenitore, è necessario specificare le impostazioni di [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) nell'oggetto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) del pool. Questo articolo contiene i collegamenti alla documentazione di riferimento sull'API Batch .NET. Nell'API [Python per Batch](/python/api/azure.batch) si trovano impostazioni corrispondenti.

È possibile creare un pool abilitato per il contenitore con o senza le immagini del contenitore prelette, come illustrato negli esempi seguenti. Il processo di pull, o di prelettura, consente di precaricare le immagini del contenitore dall'hub Docker o da un altro registro contenitori in Internet. Per prestazioni ottimali, usare un [registro contenitori di Azure](../container-registry/container-registry-intro.md) nella stessa area dell'account Batch.

Il vantaggio della prelettura delle immagini del contenitore è che quando le attività iniziano l'esecuzione non devono attendere che l'immagine contenitore venga scaricata. La configurazione del contenitore esegue il pull delle immagini del contenitore nelle macchine virtuali quando viene creato il pool. Le attività eseguite nel pool potranno quindi fare riferimento all'elenco delle immagini del contenitore e alle opzioni di esecuzione del contenitore.


### <a name="pool-without-prefetched-container-images"></a>Pool senza immagini del contenitore prelette

Per configurare un pool abilitato per il contenitore senza immagini del contenitore prelette, definire gli oggetti `ContainerConfiguration` e `VirtualMachineConfiguration` come illustrato nell'esempio seguente relativo a Python. Questo esempio usa il server Ubuntu per l'immagine dei pool di contenitori di Azure Batch dal Marketplace.


```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Prelettura delle immagini per la configurazione del contenitore

Per eseguire la prelettura delle immagini del contenitore nel pool, aggiungere l'elenco di immagini del contenitore, `container_image_names` in Python, a `ContainerConfiguration`. 

Il seguente esempio di base relativo a Python illustra come eseguire la prelettura di un'immagine del contenitore Ubuntu standard dall'[hub Docker](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


L'esempio seguente relativo a C# presuppone che si voglia eseguire una prelettura di un'immagine TensorFlow dall'[hub Docker](https://hub.docker.com). Questo esempio include un'attività di avvio che viene eseguita nell'host della VM sui nodi del pool. È possibile eseguire un'attività di avvio nell'host, ad esempio, per montare un file server accessibile dai contenitori.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Prelettura delle immagini da un contenitori privato

È anche possibile eseguire la prelettura delle immagini del contenitore eseguendo l'autenticazione a un server del registro contenitori privato. Nell'esempio seguente gli oggetti `ContainerConfiguration` e `VirtualMachineConfiguration` eseguono la prelettura di un'immagine privata di TensorFlow da un registro contenitori di Azure privato. Il riferimento all'immagine è lo stesso dell'esempio precedente.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Impostazioni del contenitore per l'attività

Per eseguire un'attività contenitore in un pool abilitato per il contenitore, specificare le impostazioni specifiche per il contenitore. Le impostazioni includono l'immagine da usare, il registro e le opzioni di esecuzione del contenitore.

* Usare la proprietà `ContainerSettings` delle classi di attività per configurare le impostazioni specifiche del contenitore. Queste impostazioni vengono definite dalla classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings). Si noti che `--rm` l'opzione contenitore non richiede un' `--runtime` opzione aggiuntiva perché viene gestita da batch. 

* Se si eseguono attività sulle immagini del contenitore, l'[attività cloud](/dotnet/api/microsoft.azure.batch.cloudtask) e l'[attività di gestione dei processi](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) richiedono le impostazioni del contenitore. Tuttavia, l'[attività di avvio](/dotnet/api/microsoft.azure.batch.starttask), l'[attività di preparazione del processo](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) e l'[attività di rilascio del processo](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) non richiedono le impostazioni dei contenitori, vale a dire che possono essere eseguite in un contesto del contenitore o direttamente nel nodo.

### <a name="container-task-command-line"></a>Riga di comando dell'attività contenitore

Quando si esegue un'attività contenitore, Batch usa automaticamente il comando [docker create](https://docs.docker.com/engine/reference/commandline/create/) per creare un contenitore usando l'immagine specificata nell'attività. Batch controlla quindi l'esecuzione dell'attività nel contenitore. 

Come per le attività di Batch non contenitore, è necessario impostare una riga di comando per un'attività contenitore. Dato che Batch crea automaticamente il contenitore, la riga di comando specifica solo il comando o i comandi che verranno eseguiti nel contenitore.

Se l'immagine del contenitore per un'attività di Batch è configurata con uno script [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example), è possibile impostare la riga di comando per usare l'ENTRYPOINT predefinito o sostituirlo: 

* Per usare l'ENTRYPOINT predefinito dell'immagine del contenitore, impostare la riga di comando dell'attività sulla stringa vuota `""`.

* Per sostituire l'ENTRYPOINT predefinito oppure se l'immagine non include un ENTRYPOINT, impostare una riga di comando appropriata per il contenitore, ad esempio, `/app/myapp` o `/bin/sh -c python myscript.py`.

[ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) sono argomenti aggiuntivi facoltativi che è possibile specificare per il comando `docker create` usato da Batch per creare ed eseguire il contenitore. Ad esempio, per impostare una directory di lavoro per il contenitore, impostare l'opzione `--workdir <directory>`. Vedere le informazioni di riferimento su [docker create](https://docs.docker.com/engine/reference/commandline/create/) per le opzioni aggiuntive.

### <a name="container-task-working-directory"></a>Directory di lavoro dell'attività contenitore

Un'attività contenitore di Azure Batch viene eseguita in una directory di lavoro nel contenitore, molto simile alla directory impostata da Batch per un'attività normale (non contenitore). Si noti che questa directory di lavoro è diversa da [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir), se configurata nell'immagine, o dalla directory di lavoro del contenitore predefinita (`C:\` per un contenitore Windows o `/` per un contenitore Linux). 

Per un'attività contenitore Batch:

* Per tutte le directory che appaiono in modo ricorsivo sotto `AZ_BATCH_NODE_ROOT_DIR` nel nodo host (la radice delle directory di Azure Batch) viene eseguito il mapping nel contenitore
* Viene eseguito il mapping di tutte le variabili di ambiente delle attività nel contenitore
* La directory di lavoro dell'attività `AZ_BATCH_TASK_WORKING_DIR` per il nodo viene impostata sulla stessa valida per un'attività normale e mappata nel contenitore. 

Questi mapping consentono di usare le attività contenitore come le attività non contenitore. Ad esempio, installare applicazioni usando i pacchetti dell'applicazione, accedere ai file di risorse da Archiviazione di Azure, usare le impostazioni di ambiente delle attività e rendere persistenti i file di output di attività dopo l'arresto del contenitore.

### <a name="troubleshoot-container-tasks"></a>Risolvere i problemi delle attività contenitore

Se l'attività contenitore non viene eseguita come previsto, potrebbe essere necessario ottenere informazioni sulla configurazione di WORKDIR o ENTRYPOINT dell'immagine del contenitore. Per visualizzare la configurazione, eseguire il comando [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/). 

Se necessario, modificare le impostazioni dell'attività contenitore in base all'immagine:

* Specificare un percorso assoluto nella riga di comando dell'attività. Se l'ENTRYPOINT predefinito dell'immagine viene usato per la riga di comando dell'attività, assicurarsi che sia impostato un percorso assoluto.

* Nelle opzioni di esecuzione del contenitore dell'attività, modificare la directory di lavoro in modo che corrisponda a WORKDIR nell'immagine. Ad esempio, impostare `--workdir /app`.

## <a name="container-task-examples"></a>Esempi di attività contenitore

Il frammento di codice Python seguente mostra una riga di comando di base in esecuzione in un contenitore creato da un'immagine fittizia estratta dall'Hub Docker. In questo caso, l'opzione del contenitore `--rm` rimuove il contenitore al termine dell'esecuzione dell'attività e l'opzione `--workdir` imposta una directory di lavoro. La riga di comando esegue l'override dell'ENTRYPOINT del contenitore con un comando della shell semplice che consente di scrivere un piccolo file nella directory di lavoro dell'attività nell'host. 

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

L'esempio C# seguente illustra le impostazioni di base del contenitore per un'attività cloud:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Passaggi successivi

* Vedere anche il toolkit [Batch Shipyard](https://github.com/Azure/batch-shipyard) per la distribuzione semplificata dei carichi di lavoro dei contenitori in Azure Batch tramite [ricette Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Per altre informazioni sull'installazione e l'uso di Docker CE in Linux, consultare la documentazione di [Docker](https://docs.docker.com/engine/installation/).

* Per altre informazioni sull'uso delle immagini personalizzate, vedere [Usare un'immagine personalizzata gestita per creare un pool di macchine virtuali](batch-custom-images.md).

* Altre informazioni sul [progetto Moby](https://mobyproject.org/), un framework per la creazione di sistemi basati su contenitori.
