---
title: "Eseguire attività con account utente in Azure Batch | Microsoft Docs"
description: "Configurare gli account utente per l'esecuzione di attività in Azure Batch"
services: batch
author: tamram
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.openlocfilehash: d408c0565c0ed81fc97cc2b3976a4fc233e31302
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Eseguire attività con account utente in Batch

In Azure Batch un'attività viene sempre eseguita con un account utente. Per impostazione predefinita, le attività vengono eseguite con account utente standard, senza le autorizzazioni di amministratore perché le impostazioni predefinite dell'account utente in genere sono sufficienti. Per determinati scenari, tuttavia, è utile essere in grado di configurare l'account utente con cui si intende eseguire un'attività. Questo articolo illustra i tipi di account utente e il modo in cui è possibile configurarli per lo scenario in uso.

## <a name="types-of-user-accounts"></a>Tipi di account utente

Azure Batch offre due tipi di account utente per l'esecuzione di attività:

- **Account utente automatici.** Gli account utente automatici sono predefiniti e vengono creati automaticamente dal servizio Batch. Per impostazione predefinita, le attività vengono eseguite con un account utente automatico. È possibile configurare la specifica di utente automatico per un'attività per indicare con quale account utente automatico l'attività deve essere eseguita. La specifica di utente automatico consente di specificare il livello di elevazione dei privilegi e l'ambito dell'account utente automatico con cui verrà eseguita l'attività. 

- **Account utente non anonimi.** È possibile specificare uno o più account utente non anonimi per un pool al momento della creazione del pool stesso. Ogni account utente viene creato in ogni nodo del pool. Oltre al nome, specificare la password dell'account utente, il livello di elevazione dei privilegi e, per i pool Linux, la chiave privata SSH. Quando si aggiunge un'attività, è possibile specificare l'account utente non anonimo con cui eseguirla.

> [!IMPORTANT] 
> Nella versione 2017-01-01.4.0 del servizio Batch è stata introdotta una modifica significativa che richiede l'aggiornamento del codice per chiamare tale versione. Se si esegue la migrazione di codice da una versione precedente di Batch, si noti che la proprietà **runElevated** non è più supportata nelle librerie client API REST o Batch. Per specificare il livello di elevazione dei privilegi, usare la nuova proprietà **userIdentity** di un'attività. Per linee guida rapide sull'aggiornamento del codice Batch se si usa una delle librerie client, vedere la sezione [Aggiornare il codice alla libreria client Batch più recente](#update-your-code-to-the-latest-batch-client-library).
>
>

> [!NOTE] 
> Per motivi di sicurezza, gli account utente descritti in questo articolo non supportano i protocolli RDP (Remote Desktop Protocol) o SSH (Secure Shell). 
>
> Per connettersi a un nodo che esegue la configurazione della macchina virtuale Linux tramite SSH, vedere [Installare e configurare Desktop remoto per connettersi a una VM Linux di Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Per connettersi ai nodi che eseguono Windows tramite RDP, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](../virtual-machines/windows/connect-logon.md).<br /><br />
> Per connettersi a un nodo che esegue la configurazione del servizio cloud tramite RDP, vedere [Impostare una connessione Desktop remoto per un ruolo nei servizi cloud di Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Accesso degli account utente a file e directory

Un account utente automatico e un account utente non anonimo hanno entrambi accesso in lettura e scrittura alla directory di lavoro dell'attività, alla directory condivisa e alla directory di attività a istanze multiple. Entrambi i tipi di account hanno anche accesso in lettura alle directory di avvio e a quelle di preparazione dei processi.

Se un'attività viene eseguita con lo stesso account usato per l'esecuzione di un'attività di avvio, tale attività ha accesso in lettura e scrittura alla directory dell'attività di avvio. In modo analogo, se un'attività viene eseguita con lo stesso account usato per l'esecuzione di un'attività di preparazione dei processi, tale attività ha accesso in lettura e scrittura anche alla directory dell'attività di preparazione dei processi. Se un'attività viene eseguita con un account diverso rispetto a quello usato per l'attività di avvio o per quella di preparazione dei processi, tale attività ha solo accesso in lettura alla directory corrispondente.

Per altre informazioni sull'accesso a file e directory da parte un'attività, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Accesso con privilegi elevati per le attività 

Il livello di elevazione dei privilegi dell'account utente indica se un'attività viene eseguita con privilegi elevati. Un account utente automatico e un account utente non anonimo consentono entrambi l'accesso con privilegi elevati. Le due opzioni per il livello di elevazione dei privilegi sono le seguenti:

- **NonAdmin** (Non amministratore): l'attività viene eseguita come utente standard senza accesso con privilegi elevati. Il livello di elevazione dei privilegi predefinito per un account utente Batch è sempre **NonAdmin**.
- **Admin** (Amministratore): l'attività viene eseguita come utente con accesso con privilegi elevati e opera con le autorizzazioni di amministratore complete. 

## <a name="auto-user-accounts"></a>Account utente automatici

Per impostazione predefinita, in Batch le attività vengono eseguite con un account utente automatico, come utente standard senza accesso con privilegi elevati e con un ambito di attività. Quando per l'ambito di attività è configurata la specifica di utente automatico, il servizio Batch crea un account utente di questo tipo solo per l'attività.

L'alternativa all'ambito di attività è l'ambito di pool. Quando per l'ambito di pool è configurata la specifica di utente automatico, l'attività viene eseguita con un account di questo tipo disponibile per qualsiasi attività nel pool. Per altre informazioni sull'ambito di pool, vedere la sezione [Eseguire un'attività con l'account utente automatico con ambito di pool](#run-a-task-as-the-autouser-with-pool-scope).   

L'ambito predefinito è diverso in nodi Windows e Linux:

- Nei nodi Windows le attività vengono eseguite nell'ambito di attività per impostazione predefinita.
- I nodi Linux vengono sempre eseguiti nell'ambito di pool.

Esistono quattro possibili configurazioni per la specifica di utente automatico, ognuna delle quali corrisponde a un account utente automatico univoco:

- Accesso senza privilegi di amministratore con ambito di attività (specifica di utente automatico predefinito)
- Accesso con privilegi di amministratore (elevati) con ambito di attività
- Accesso senza privilegi di amministratore con ambito di pool
- Accesso con privilegi di amministratore con ambito di pool

> [!IMPORTANT] 
> Le attività in esecuzione nell'ambito di attività non dispongono dell'accesso standard ad altre attività in un nodo. Un utente malintenzionato con accesso all'account, tuttavia, potrebbe aggirare questa restrizione inviando un'attività che viene eseguita con privilegi di amministratore e che può accedere alle directory di altre attività. Un utente malintenzionato potrebbe anche usare il protocollo RDP o SSH per connettersi a un nodo. È importante pertanto proteggere l'accesso alle chiavi dell'account Batch per impedire che si verifichi uno scenario di questo tipo. Se si sospetta che l'account sia stato compromesso, assicurarsi di rigenerare le chiavi.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Eseguire un'attività come utente automatico con accesso con privilegi elevati

Quando è necessario eseguire un'attività con accesso con privilegi elevati, è possibile configurare la specifica di utente automatico per i privilegi di amministratore. A un'attività di avvio, ad esempio, potrebbe essere necessario l'accesso con privilegi elevati per installare il software nel nodo.

> [!NOTE] 
> In generale, è consigliabile usare l'accesso con privilegi elevati solo quando è necessario e concedere esclusivamente i privilegi minimi necessari per ottenere il risultato desiderato. Se ad esempio un'attività di avvio consente di installare software per l'utente corrente, anziché per tutti gli utenti, è possibile evitare di concedere l'accesso con privilegi elevati alle attività. È possibile configurare la specifica di utente automatico per l'ambito di pool e senza privilegi di amministratore per tutte le attività che devono essere eseguite con lo stesso account, tra cui l'attività di avvio. 
>
>

I frammenti di codice seguente illustrano come configurare la specifica di utente automatico. Gli esempi impostano il livello di elevazione dei privilegi su `Admin` e su `Task`. L'ambito di attività è l'impostazione predefinita, ma in questo caso viene inclusa a scopo di esempio.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Eseguire un'attività come utente automatico con ambito di pool

Quando viene eseguito il provisioning di un nodo, a livello di pool vengono creati due account utente automatici per ogni nodo del pool, uno con accesso con privilegi elevati e uno senza accesso con privilegi elevati. Se si imposta l'ambito dell'utente automatico sull'ambito di pool per una determinata attività, questa verrà eseguita con uno di tali due account a livello di pool. 

Quando si specifica l'ambito di pool per l'utente automatico, tutte le attività eseguite con privilegi di amministratore vengono eseguite con lo stesso account utente automatico a livello di pool. In modo analogo, le attività eseguite senza privilegi di amministratore vengono eseguite anche con un unico account utente automatico a livello di pool. 

> [!NOTE] 
> I due account utente automatici a livello di pool sono account separati. Le attività in esecuzione con l'account amministrativo a livello di pool non possono condividere dati con quelle in esecuzione con l'account standard e viceversa. 
>
>

Il vantaggio di eseguire attività con lo stesso account utente automatico consiste nel fatto che le attività sono in grado di condividere dati con altre attività in esecuzione nello stesso nodo.

La condivisione di segreti tra le attività è uno scenario in cui l'esecuzione di attività con uno dei due account utente automatici a livello di pool è particolarmente utile. Si supponga ad esempio che un'attività di avvio richieda il provisioning di un segreto sul nodo che può essere usato da altre attività. È possibile usare l'API di protezione dati di Windows (DPAPI), ma in questo caso sono necessari anche i privilegi di amministratore. In alternativa, è possibile proteggere il segreto a livello di utente. Le attività in esecuzione con lo stesso account utente possono accedere al segreto senza accesso con privilegi elevati.

Un altro scenario in cui può essere opportuno eseguire attività con un account utente automatico con ambito di pool è la condivisione di file di tipo MPI (Message Passing Interface). Una condivisione di file MPI è utile quando i nodi nell'attività MPI devono usare gli stessi dati di file. Il nodo head crea una condivisione di file cui i nodi figlio possono accedere se sono in esecuzione con lo stesso account utente automatico. 

Il frammento di codice seguente imposta l'ambito dell'utente automatico sull'ambito di pool per un'attività in Batch .NET. Il livello di elevazione dei privilegi viene omesso, in modo che l'attività venga eseguita con l'account utente automatico standard a livello di pool.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Account utente non anonimi

Quando si crea un pool, è possibile definire gli account utente non anonimi. A un account utente non anonimo sono associati un nome e una password definiti dall'utente. Per un account utente non anonimo, è possibile specificare il livello di elevazione dei privilegi. Per i nodi Linux, è anche possibile specificare una chiave privata SSH.

Un account utente non anonimo è presente in tutti i nodi del pool ed è disponibile per tutte le attività in esecuzione su tali nodi. In un pool è possibile definire un numero qualsiasi di utenti non anonimi. Quando si aggiunge un'attività o una raccolta di attività, è possibile specificare che l'attività venga eseguita con uno degli account utente non anonimi definiti nel pool.

Un account utente non anonimo è utile quando si vuole che tutte le attività in un processo vengano eseguite con lo stesso account utente, ma si vuole anche isolarle dalle attività in esecuzione contemporaneamente in altri processi. È possibile ad esempio creare un utente non anonimo per ogni processo ed eseguire le attività di ogni processo con tale account. Ogni processo può condividere un segreto con le proprie attività, ma non con attività in esecuzione in altri processi.

È anche possibile usare un account utente non anonimo per eseguire un'attività che imposta le autorizzazioni in risorse esterne, ad esempio in condivisioni di file. Con un account utente non anonimo è possibile controllare l'identità dell'utente e usare tale identità per impostare le autorizzazioni.  

Gli account utente non anonimi consentono di abilitare il protocollo SSH senza password tra i nodi Linux. È possibile usare un account utente non anonimo con nodi Linux per cui è necessario eseguire attività a istanze multiple. Ogni nodo nel pool può eseguire attività con un account utente definito nell'intero pool. Per altre informazioni sulle attività a istanze multiple, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Batch](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Creare account utente non anonimi

Per creare account utente non anonimi in Batch, aggiungere una raccolta di account utente al pool. I frammenti di codice seguenti illustrano come creare account utente non anonimi in .NET, Java e Python in un pool, sia con privilegi di amministratore che senza. Gli esempi creano pool usando la configurazione del servizio cloud, ma si usa lo stesso approccio quando si crea un pool di Windows o Linux tramite la configurazione della macchina virtuale.

#### <a name="batch-net-example-windows"></a>Esempio per Batch .NET (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                                         
    virtualMachineSize: "small",                                                
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Esempio per Batch .NET (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Esempio per Batch Java

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Esempio per Batch Python

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Eseguire un'attività con un account utente non anonimo con accesso con privilegi elevati

Per eseguire un'attività come utente con privilegi elevati, impostarne la proprietà **UserIdentity** su un account utente non anonimo creato con la proprietà **ElevationLevel** impostata su `Admin`.

Questo frammento di codice specifica che l'attività deve essere eseguita con un account utente non anonimo. Tale account utente è stato definito nel pool al momento della relativa creazione. In questo caso l'account utente non anonimo è stato creato con le autorizzazioni di amministratore:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aggiornare il codice in base alla libreria client Batch più recente

Nella versione 2017-01-01.4.0 del servizio Batch è stata introdotta una modifica significativa, ovvero la proprietà **runElevated** disponibile nelle versioni precedenti è stata sostituita con la proprietà **userIdentity**. Le tabelle seguenti illustrano una semplice corrispondenza che consente di aggiornare il codice dalle versioni precedenti delle librerie client.

### <a name="batch-net"></a>Batch .NET

| Versione precedente                  | Versione aggiornata                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` non specificato | Non sono necessari aggiornamenti                                                                                               |

### <a name="batch-java"></a>Batch Java

| Versione precedente                      | Versione aggiornata                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` non specificato | Non sono necessari aggiornamenti                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Versione precedente                      | Versione aggiornata                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, dove <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, dove <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated` non specificato | Non sono necessari aggiornamenti                                                                                                                                  |


## <a name="next-steps"></a>Passaggi successivi

### <a name="batch-forum"></a>Forum di Batch

Il [forum di Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch) su MSDN consente di seguire discussioni su Batch e di inviare domande sul servizio. Leggere i post aggiunti e inviare domande durante le procedure di sviluppo delle soluzioni Batch.