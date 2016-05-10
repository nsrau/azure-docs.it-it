<properties
	pageTitle="Nodi Linux nei pool di Azure Batch | Microsoft Azure"
	description="Informazioni su come elaborare i carichi di lavoro di calcolo paralleli nei pool di macchine virtuali Linux in Azure Batch."
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="na"
	ms.date="04/19/2016"
	ms.author="marsma" />

# Effettuare il provisioning di nodi di calcolo Linux nei pool di Azure Batch

Azure Batch consente di eseguire carichi di lavoro di calcolo paralleli in macchine virtuali Linux e Windows. Questo articolo illustra in modo dettagliato come creare pool di nodi di calcolo Linux nel servizio Batch usando le librerie client [Batch Python][py_batch_package] e [Batch .NET][api_net].

> [AZURE.NOTE] Supporto Linux in Batch è attualmente disponibile in anteprima. Alcuni aspetti della funzionalità illustrati in questo articolo potrebbero subire modifiche prima della disponibilità a livello generale. I [pacchetti delle applicazioni](batch-application-packages.md) e le [attività a istanze multiple](batch-mpi.md) **non sono attualmente supportati** nei nodi di calcolo Linux.

## Configurazione macchina virtuale

Quando si crea un pool di nodi di calcolo in Batch, sono disponibili due opzioni per la selezione delle dimensioni e del sistema operativo del nodo, ovvero **Configurazione di servizi cloud** e **Configurazione macchina virtuale**.

**Configurazione di servizi cloud** fornisce *solo* nodi di calcolo Windows. Le dimensioni disponibili per i nodi di calcolo sono elencate in [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md) e i sistemi operativi disponibili sono elencati in [Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando si crea un pool contenente nodi dei servizi cloud, è necessario specificare solo le dimensioni del nodo e la rispettiva "Famiglia del sistema operativo", disponibili in questi articoli. Quando si creano pool di nodi di calcolo Windows, viene in genere usata l'opzione relativa ai servizi cloud.

**Configurazione macchina virtuale** fornisce immagini Linux e Windows per i nodi di calcolo. Le dimensioni disponibili per i nodi di calcolo sono elencate in [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Quando si crea un pool contente nodi di tipo Configurazione macchina virtuale, è necessario specificare non solo le dimensioni dei nodi, ma anche il **riferimento all'immagine della macchina virtuale** e lo **SKU dell'agente del nodo** di Batch da installare nei nodi.

### Riferimento all'immagine della macchina virtuale

Il servizio Batch usa automaticamente [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per fornire nodi di calcolo Linux e le immagini del sistema operativo per queste macchine virtuali sono fornite dal [Marketplace per Macchine virtuali di Azure][vm_marketplace]. Quando si configura un riferimento all'immagine di macchina virtuale, si specificano le proprietà di un'immagine di macchina virtuale del Marketplace. Le proprietà seguenti sono necessarie quando si crea un riferimento all'immagine di macchina virtuale:

| **Proprietà del riferimento all'immagine** | **Esempio** |
| ----------------- | ------------------------ |
| Autore | Canonical |
| Offerta | UbuntuServer |
| SKU | 14\.04.4-LTS |
| Versione | più recenti |

> [AZURE.TIP] Per altre informazioni su queste proprietà e su come elencare le immagini del Marketplace, vedere [Individuare e selezionare immagini di macchine virtuali Linux in Azure con l'interfaccia della riga di comando o PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Si noti che non tutte le immagini del Marketplace sono attualmente compatibili con Batch. Vedere [SKU dell'agente del nodo](#node-agent-sku) più avanti.

### SKU dell'agente del nodo

L'agente del nodo Batch è un programma in esecuzione in ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio Batch. Sono disponibili diverse implementazioni dell'agente del nodo, definite SKU, per sistemi operativi diversi. Quando si crea una configurazione di macchina virtuale, è essenzialmente necessario specificare prima di tutto il riferimento all'immagine di macchina virtuale e quindi specificare l'agente del nodo da installare nell'immagine. Ogni SKU dell'agente del nodo è in genere compatibile con più immagini di macchina virtuale. Ecco alcuni esempi di SKU dell'agente del nodo:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [AZURE.IMPORTANT] Non tutte le immagini di macchina virtuale disponibili nel Marketplace sono compatibili con gli agenti del nodo Batch attualmente disponibili. È necessario usare Batch SDK per elencare gli SKU dell'agente del nodo disponibili e le immagini di macchina virtuale con cui sono compatibili. Per altre informazioni, vedere [Elenco di immagini di macchina virtuale](#list-of-virtual-machine-images) più avanti.

## Creare un pool Linux: Batch Python

Il frammento di codice seguente illustra la creazione di un pool di nodi di calcolo Ubuntu Server mediante la [libreria client di Microsoft Azure Batch per Python][py_batch_package]. Documentazione di riferimento per il modulo Batch Python è disponibile nel[azure.batch package ][py_batch_docs] nella sezione relativa alla lettura dei documenti.

In questo frammento di codice si crea in modo esplicito un metodo [ImageReference][py_imagereference], specificando tutte le rispettive proprietà, ovvero server di pubblicazione, offerta, SKU, versione. È tuttavia consigliabile usare il metodo [list\_node\_agent\_skus][py_list_skus] nel codice di produzione per determinare e selezionare una delle combinazioni di immagine e SKU dell'agente del nodo disponibili in fase di esecuzione.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Come indicato in precedenza, invece di creare [ImageReference][py_imagereference] in modo esplicito, è consigliabile usare il metodo [list\_node\_agent\_skus][py_list_skus] per selezionare in modo dinamico una delle combinazioni di agente del nodo e immagine del Marketplace attualmente supportate. Il frammento di codice Python seguente mostra l'utilizzo di questo metodo.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## Creare un pool Linux: Batch .NET

Il frammento di codice seguente illustra la creazione di un pool di nodi di calcolo Ubuntu Server mediante la libreria client [Batch .NET][nuget_batch_net]. La [documentazione di riferimento per Batch .NET][api_net] è disponibile su MSDN.

Il frammento di codice seguente usa il metodo [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] per selezionare una delle combinazioni di immagine del Marketplace e SKU dell'agente del nodo attualmente supportate. Questa tecnica è consigliabile perché l'elenco di combinazioni supportate può cambiare nel tempo, in genere a causa dell'aggiunta di altre combinazioni supportate.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Anche se il frammento di codice precedente usa il metodo [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] per elencare in modo dinamico e selezionare una delle combinazioni di immagine e SKU dell'agente del nodo supportate, è anche possibile configurare un metodo [ImageReference][net_imagereference] in modo esplicito:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## Elenco di immagini di macchine virtuali

La tabella seguente elenca le immagini di macchina virtuale del Marketplace compatibili con gli agenti del nodo Batch disponibili **al momento della stesura del documento**. È importante notare che questo elenco non è definitivo, perché è possibile che immagini e agenti del nodo vengano aggiunti o rimossi in qualsiasi momento. È consigliabile che le applicazioni e i servizi Batch usino sempre [list\_node\_agent\_skus][py_list_skus] \(Python) e [ListNodeAgentSkus][net_list_skus] \(Batch .NET) per determinare e selezionare gli SKU attualmente disponibili.

> [AZURE.WARNING] L'elenco seguente potrebbe subire modifiche in qualsiasi momento. Usare sempre i metodi di tipo **list node agent SKU** disponibili nelle API di Batch per elencare e selezionare le combinazioni compatibili di macchina virtuale e SKU dell'agente del nodo quando si eseguono i processi Batch.

| **Autore** | **Offerta** | **SKU dell'immagine** | **Versione** | **ID dello SKU dell'agente del nodo** |
| ------- | ------- | ------- | ------- | ------- |
| Canonical | UbuntuServer | 14\.04.0-LTS | più recenti | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.1-LTS | più recenti | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.2-LTS | più recenti | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.3-LTS | più recenti | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.4-LTS | più recenti | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 15\.10 | più recenti | batch.node.debian 8 |
| Credativ | Debian | 8 | più recenti | batch.node.debian 8 |
| OpenLogic | CentOS | 7\.0 | più recenti | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.1 | più recenti | batch.node.centos 7 |
| OpenLogic | CentOS | 7,2 | più recenti | batch.node.centos 7 |
| Oracle | Oracle-Linux-7 | OL70 | più recenti | batch.node.centos 7 |
| SUSE | SLES | 12 | più recenti | batch.node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | più recenti | batch.node.opensuse 42.1 |
| SUSE | SLES-HPC | 12 | più recenti | batch.node.opensuse 42.1 |
| SUSE | openSUSE | 13\.2 | più recenti | batch.node.opensuse 13.2 |
| SUSE | openSUSE-Leap | 42\.1 | più recenti | batch.node.opensuse 42.1 |
| MicrosoftWindowsServer | WindowsServer | 2008 R2-SP1 | più recenti | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | più recenti | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | più recenti | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | più recenti | batch.node.windows amd64 |

## Connettersi a nodi Linux

Durante lo sviluppo o la risoluzione dei problemi potrebbe essere necessario accedere ai nodi del pool. A differenza dei nodi di calcolo Windows, non è possibile usare il protocollo RDP (Remote Desktop Protocol) per connettersi ai nodi Linux. Il servizio Batch consente invece l'accesso SSH in ogni nodo per la connessione remota.

Il frammento di codice Python seguente crea un utente su ogni nodo in un pool, necessario per la connessione remota, quindi stampa le informazioni di connessione SSH per ogni nodo.

```python
import getpass

# Specify the username and prompt for a password
username = "linuxuser"
password = getpass.getpass()

# Create the user that will be added to each node
# in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = client.compute_node.get_remote_login_settings(pool_id,
                                                          node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Ecco l'output di esempio per il codice precedente per un pool contenente quattro nodi Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Si noti che, invece di una password, è possibile specificare una chiave pubblica SSH durante la creazione di un utente su un nodo. In Python SDK questa operazione viene eseguita mediante il parametro **ssh\_public\_key** su [ComputeNodeUser][py_computenodeuser] e in .NET mediante la proprietà [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## Prezzi

Azure Batch è basato sulla tecnologia di Servizi cloud di Azure e di Macchine virtuali di Azure. Il servizio Batch stesso è gratuito, ovvero vengono addebitate solo le risorse di calcolo utilizzate dalle soluzioni Batch. Quando si sceglie l'opzione **Configurazione macchina virtuale**, gli addebiti saranno basati sulla struttura di [Prezzi di Servizi cloud][cloud_services_pricing]. Quando si sceglie l'opzione **Configurazione macchina virtuale**, gli addebiti saranno basati sulla struttura di [Prezzi di Macchine virtuali][vm_pricing].

## Passaggi successivi

### Esempi di codice Batch Python

Vedere gli [esempi di codice Python][github_samples_py] nel repository [azure-batch-samples][github_samples] su GitHub per ottenere alcuni script che illustrano come eseguire operazioni Batch comuni, ad esempio la creazione di pool, processi e attività e altro ancora. Il file [README][github_py_readme] associato agli esempi Python include dettagli sull'installazione dei pacchetti necessari.

### Forum di Batch

Il [Forum di Azure Batch][forum] su MSDN consente di seguire discussioni su Batch inviare domande sul servizio. Leggere i post contrassegnati e inviare domande durante lo sviluppo di soluzioni Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/it-IT/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramma di alto livello di pacchetti applicazione"

<!---HONumber=AcomDC_0427_2016-->