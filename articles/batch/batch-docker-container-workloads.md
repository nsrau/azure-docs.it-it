---
title: Carichi di lavoro del contenitore in Azure Batch | Microsoft Docs
description: Informazioni su come eseguire le applicazioni dalle immagini del contenitore in Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 02/26/2018
ms.author: danlep
ms.openlocfilehash: a26d786ffcb74bb28fb9bd065e49398d52d2b662
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Eseguire le applicazioni del contenitore in Azure Batch

Azure Batch consente di eseguire e ridimensionare tantissimi processi di batch computing in Azure. Fino ad ora, le attività di Batch sono state eseguite direttamente sulle macchine virtuali in un pool di Batch, ma ora è possibile configurare un pool di Batch che esegua le attività in contenitori Docker.

L'uso dei contenitori consente di eseguire le attività Batch in modo semplice senza dover gestire le dipendenze e i pacchetti di applicazioni. I contenitori distribuiscono le applicazioni come unità leggere, portatili e autosufficienti che possono essere eseguite in un'ampia gamma di ambienti. Ad esempio, è possibile compilare e testare in locale un contenitore, quindi caricare l'immagine del contenitore in un registro di Azure o altrove. Il modello di distribuzione del contenitore assicura che l'ambiente di runtime dell'applicazione venga sempre installato e configurato correttamente, indipendentemente dalla posizione in cui si ospita l'applicazione. Questa esercitazione illustra come usare .NET SDK di Batch per creare un pool di nodi di calcolo che supporti l'esecuzione di attività del contenitore e come eseguire le attività del contenitore nel pool.

Questo articolo presuppone che l'utente abbia familiarità con il concetto di contenitore Docker e con la creazione di un pool di Batch e di un processo tramite .NET SDK. I frammenti di codice sono concepiti per essere usati in un'applicazione client simile all'[esempio DotNetTutorial](batch-dotnet-get-started.md) e sono esempi di codice necessari per supportare le applicazioni del contenitore in Batch.


## <a name="prerequisites"></a>prerequisiti

* Versioni di SDK: gli SDK di Batch supportano le immagini del contenitore nelle versioni seguenti:
    * API REST di Batch, versione: 2017-09-01.6.0
    * .NET SDK di Batch, versione 8.0.0
    * Python SDK di Batch, versione 4.0
    * Java SDK di Batch, versione 3.0
    * Node.js SDK di Batch, versione 3.0

* Account: nell'account di Azure è necessario creare un account Batch e, facoltativamente, un account di Archiviazione generico.

* Un'immagine di macchina virtuale supportata. I contenitori sono supportati solo in pool creati con la configurazione macchina virtuale di immagini dettagliate nella sezione seguente, "Immagini di macchine virtuali supportate".

* Se si specifica un'immagine personalizzata, l'applicazione deve usare l'autenticazione di Azure Active Directory per eseguire carichi di lavoro basati sul contenitore. Se si usa un'immagine di Azure Marketplace, non è necessaria l'autenticazione di Azure AD, l'autenticazione della chiave condivisa sarà sufficiente. Il supporto di Azure Batch per Azure AD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).


## <a name="supported-virtual-machine-images"></a>Immagini di macchine virtuali supportate

Per creare un pool di nodi di calcolo della macchina virtuale è necessario specificare un'immagine di Windows o Linux.

### <a name="windows-images"></a>Immagini di Windows

Per carichi di lavoro del contenitore Windows, Batch attualmente supporta le immagini personalizzate create dalle macchine virtuali che eseguono Docker in Windows. In alternativa è possibile usare Windows Server 2016 Datacenter con l'immagine dei contenitori di Azure Marketplace. Questa immagine è compatibile con l'ID SKU dell'agente del nodo `batch.node.windows amd64`. Il tipo di contenitore supportato è attualmente limitato a Docker.

### <a name="linux-images"></a>Immagini di Linux

Per i carichi di lavoro del contenitore Linux, Batch supporta attualmente solo immagini personalizzate create dalle macchine virtuali che eseguono Docker nelle distribuzioni Linux seguenti: Ubuntu 16.04 LTS o CentOS 7.3. Se si sceglie di inserire l'immagine personalizzata di Linux, vedere le istruzioni in [Usare un'immagine personalizzata gestita per creare un pool di macchine virtuali](batch-custom-images.md).

È possibile usare [Docker Community Edition](https://www.docker.com/community-edition) (Community Edition di Docker) o [Docker Enterprise Edition](https://www.docker.com/enterprise-edition) (Enterprise Edition di Docker).

Se si desidera sfruttare le prestazioni della GPU delle dimensioni della macchina virtuale di Azure NC o NV, è necessario installare i driver NVIDIA nell'immagine. È anche necessario installare ed eseguire Docker Engine Utility per le GPU NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Per accedere alla rete RDMA di Azure, usare macchine virtuali delle dimensioni seguenti: A8, A9, H16r, H16mr o NC24r. I driver RDMA necessari sono installati nelle immagini CentOS 7.3 HPC e Ubuntu 16.04 LTS di Azure Marketplace. Potrebbe essere necessaria una configurazione aggiuntiva per eseguire carichi di lavoro MPI. Vedere [Usare istanze con supporto per RDMA o abilitate per GPU in pool di Batch](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Limitazioni

* Batch offre supporto RDMA solo per i contenitori in esecuzione nei pool di Linux.


## <a name="authenticate-using-azure-active-directory"></a>Eseguire l'autenticazione con Azure Active Directory

Se si usa un'immagine di macchina virtuale personalizzata per creare il pool di Batch, l'applicazione client deve autenticarsi con l'autenticazione integrata di Azure Active Directory, l'autenticazione con la chiave condivisa non funziona. Prima di eseguire l'applicazione, assicurarsi di registrarla in Azure AD per stabilirne un'identità e specificarne le autorizzazioni per le altre applicazioni.

In aggiunta, quando si usa un'immagine di macchina virtuale personalizzata, è necessario concedere il controllo di accesso IAM all'applicazione affinché acceda all'immagine di macchina virtuale. Nel portale di Azure aprire **Tutte le risorse**, selezionare l'immagine del contenitore e nella sezione **Controllo di accesso (IAM)** del pannello dell'immagine fare clic su **Aggiungi**. Nel pannello **Aggiungi autorizzazioni** specificare un **ruolo** in **Assegna accesso a**, selezionare **Applicazione, gruppo o utente di Azure AD** quindi in **Seleziona** immettere il nome dell'applicazione.

Nell'applicazione, passare un token di autenticazione di Azure AD quando si crea il client Batch tramite [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), come descritto in [Autenticare le soluzioni di gestione Batch con Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Fare riferimento a un'immagine della macchina virtuale per la creazione del pool

Nel codice dell'applicazione, inserire un riferimento all'immagine della macchina virtuale da usare nella creazione dei nodi di calcolo del pool. A tale scopo creare un oggetto [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference). È possibile specificare l'immagine da usare in uno dei seguenti modi:

* Se si usa un'immagine personalizzata, specificare un identificatore della risorsa di Azure Resource Manager per l'immagine della macchina virtuale. L'identificatore dell'immagine ha un formato del percorso, come mostrato nell'esempio seguente:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Per ottenere questo ID di immagine dal portale di Azure, aprire **Tutte le risorse**, selezionare l'immagine personalizzata e nella sezione **Panoramica** del pannello dell'immagine, copiare il percorso in **ID della risorsa**.

* Se si usa un'immagine di [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based), specificare un gruppo di parametri che descrivono l'immagine: il server di pubblicazione, il tipo di offerta, la SKU e la versione dell'immagine, come indicato in [Elenco di immagini di macchine virtuali](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Configurazione del contenitore per il pool di Batch

Un pool di Batch è una raccolta di nodi di calcolo in cui Batch esegue le attività in un processo. Quando si crea il pool, si specifica la configurazione della macchina virtuale per i nodi di calcolo. L'oggetto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) contiene un riferimento all'oggetto [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration). Per abilitare i carichi di lavoro del contenitore nel pool, specificare le impostazioni `ContainerConfiguration`. La configurazione della macchina virtuale è anche il momento in cui si specifica il riferimento all'immagine e l'ID SKU dell'agente del nodo dell'immagine, come illustrato negli esempi seguenti.

Esistono diverse opzioni per la creazione del pool. È possibile creare un pool con o senza immagini del contenitore prelette. 

Il processo di pull, o di prelettura, consente di pre-caricare le immagini del contenitore dall'Hub Docker o da un altro registro del contenitore in Internet. Il vantaggio della prelettura delle immagini del contenitore è che quando le attività iniziano l'esecuzione non devono attendere che l'immagine contenitore venga scaricata. La configurazione del contenitore esegue il pull delle immagini del contenitore nelle macchine virtuali quando viene creato il pool. Le attività eseguite nel pool potranno quindi fare riferimento all'elenco delle immagini del contenitore e alle opzioni di esecuzione del contenitore.



### <a name="pool-without-prefetched-container-images"></a>Pool senza immagini del contenitore prelette

Per configurare il pool senza immagini del contenitore prelette, definire gli oggetti `ContainerConfiguration` e `VirtualMachineConfiguration` come illustrato nell'esempio seguente. Questo e gli esempi seguenti presuppongono che si usi un'immagine Ubuntu 16.04 LTS personalizzata con il motore Docker installato.

```csharp
// Specify container configuration
ContainerConfiguration containerConfig = new ContainerConfiguration();

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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Prelettura delle immagini per la configurazione del contenitore

Per eseguire la prelettura delle immagini del contenitore nel pool, aggiungere l'elenco di immagini del contenitore, `containerImageNames`, a `ContainerConfiguration` e assegnare un nome all'elenco immagini. L'esempio seguente presuppone che si usi un'immagine personalizzata di Ubuntu 16.04 LTS, che si esegua una prelettura di un'immagine TensorFlow dall'[Hub Docker](https://hub.docker.com) e che si avvii TensorFlow in un'attività di avvio.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Prelettura delle immagini da un registro di sistema del contenitore privato

È anche possibile eseguire la prelettura delle immagini del contenitore eseguendo l'autenticazione a un server del registro di sistema del contenitore privato. Nell'esempio seguente gli oggetti `ContainerConfiguration` e `VirtualMachineConfiguration` usano un'immagine personalizzata di Ubuntu 16.04 LTS ed eseguono la prelettura di un'immagine privata di TensorFlow dal registro contenitori privato di Azure.

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

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Impostazioni del contenitore per l'attività

Quando si configurano le attività da eseguire sui nodi di calcolo, è necessario specificare le impostazioni specifiche per il contenitore, ad esempio le opzioni di immagini di esecuzione dell'attività, le immagini da usare e il registro.

Usare la proprietà ContainerSettings delle classi di attività per configurare le impostazioni specifiche del contenitore. Queste impostazioni vengono definite dalla classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

Se si eseguono attività sulle immagini del contenitore, l'[attività cloud](/dotnet/api/microsoft.azure.batch.cloudtask) e l'[attività di gestione dei processi](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) richiedono le impostazioni del contenitore. Tuttavia, l'[attività di avvio](/dotnet/api/microsoft.azure.batch.starttask), l'[attività di preparazione del processo](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) e l'[attività di rilascio del processo](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) non richiedono le impostazioni dei contenitori, vale a dire che possono essere eseguite in un contesto del contenitore o direttamente nel nodo.

Quando si configurano le impostazioni del contenitore, tutte le directory elencate in modo ricorsivo in `AZ_BATCH_NODE_ROOT_DIR`, ovvero la radice della directory di Azure Batch nel nodo, vengono mappate nel contenitore, tutte le variabili di ambiente dell'attività vengono mappate nel contenitore e la riga di comando dell'attività viene eseguita nel contenitore.

L'esempio di codice [Prelettura delle immagini per la configurazione del contenitore](#prefetch-images-for-container-configuration) ha illustrato come specificare una configurazione del contenitore per un'attività di avvio. L'esempio di codice seguente illustra come specificare la configurazione del contenitore per un'attività cloud:

```csharp
// Simple task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
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
