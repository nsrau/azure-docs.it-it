---
title: Eseguire Linux nei nodi di calcolo della macchina virtuale | Documentazione Microsoft
description: Informazioni su come elaborare i carichi di lavoro di calcolo paralleli nei pool di macchine virtuali Linux in Azure Batch.
services: batch
documentationcenter: python
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b4b381ff1f68935084e3dd30865cf539d4abbd16
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "68323518"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Eseguire il provisioning di nodi di calcolo Linux nei pool di Batch

È possibile usare Azure Batch per eseguire carichi di lavoro di calcolo paralleli in macchine virtuali Linux e Windows. Questo articolo illustra come creare pool di nodi di calcolo Linux nel servizio batch usando le librerie client batch [Python][py_batch_package] e [batch .NET][api_net] .

> [!NOTE]
> I pacchetti dell'applicazione sono supportati in tutti i pool di Batch creati dopo il 5 luglio 2017. Sono supportati nei pool di Batch creati tra il 10 marzo 2016 e il 5 luglio 2017 solo se il pool è stato creato usando una configurazione del servizio cloud. I pool di batch creati prima del 10 marzo 2016 non supportano i pacchetti dell'applicazione. Per altre informazioni sull'uso di pacchetti dell'applicazione per distribuire applicazioni ai nodi Batch, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Configurazione macchina virtuale
Quando si crea un pool di nodi di calcolo in Batch, sono disponibili due opzioni per la selezione delle dimensioni e del sistema operativo del nodo: la configurazione di servizi cloud e la configurazione della macchina virtuale.

**Cloud Services Configuration** (Configurazione servizi cloud) fornisce *solo*nodi di calcolo Windows. Le dimensioni disponibili per i nodi di calcolo sono elencate in [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md) e i sistemi operativi disponibili sono elencati in [Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando si crea un pool che contiene nodi di Servizi cloud di Azure, è necessario specificare le dimensioni del nodo e la rispettiva famiglia del sistema operativo, descritti negli articoli menzionati in precedenza. Quando si creano pool di nodi di calcolo Windows, viene in genere usata l'opzione Servizi cloud.

La **configurazione della macchina virtuale** fornisce immagini Linux e Windows per i nodi di calcolo. Le dimensioni disponibili per i nodi di calcolo sono elencate in [Dimensioni delle macchine virtuali in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) e [Dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Quando si crea un pool contenente nodi con configurazione della macchina virtuale, è necessario specificare le dimensioni dei nodi, il riferimento a un'immagine della macchina virtuale e lo SKU dell'agente del nodo Batch da installare nei nodi.

### <a name="virtual-machine-image-reference"></a>Riferimento all'immagine della macchina virtuale
Il servizio Batch usa [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per fornire nodi di calcolo nella configurazione macchina virtuale. È possibile specificare un'immagine da [Azure Marketplace][vm_marketplace]o fornire un'immagine personalizzata preparata. Per altri dettagli sulle immagini personalizzate, vedere [Creare un pool con un'immagine personalizzata](batch-custom-images.md).

Quando si configura un riferimento all'immagine di macchina virtuale, si specificano le proprietà di un'immagine di macchina virtuale. Le proprietà seguenti sono necessarie quando si crea un riferimento all'immagine di macchina virtuale:

| **Proprietà del riferimento all'immagine** | **Esempio** |
| --- | --- |
| Pubblicato da |Canonico |
| Offerta |UbuntuServer |
| SKU |14.04.4-LTS |
| Versione |latest |

> [!TIP]
> Per altre informazioni su queste proprietà e su come elencare le immagini del Marketplace, vedere [Selezionare immagini di VM Linux con l'interfaccia della riga di comando di Azure](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Si noti che non tutte le immagini del Marketplace sono attualmente compatibili con Batch. Per altre informazioni, vedere [SKU dell'agente del nodo](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>SKU dell'agente del nodo
L'agente del nodo Batch è un programma in esecuzione in ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio Batch. Sono disponibili diverse implementazioni dell'agente del nodo, definite SKU, per sistemi operativi diversi. Quando si crea una configurazione della macchina virtuale, è essenzialmente necessario specificare prima di tutto il riferimento all'immagine della macchina virtuale e quindi l'agente del nodo da installare nell'immagine. Ogni SKU dell'agente del nodo è in genere compatibile con più immagini di macchina virtuale. Ecco alcuni esempi di SKU dell'agente del nodo:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Non tutte le immagini di macchina virtuale disponibili nel Marketplace sono compatibili con gli agenti del nodo Batch attualmente disponibili. È necessario usare SDK di Batch per elencare gli SKU dell'agente del nodo disponibili e le immagini di macchina virtuale con cui sono compatibili. Vedere [Elenco di immagini di macchine virtuali](#list-of-virtual-machine-images) più avanti in questo articolo per altre informazioni ed esempi su come recuperare un elenco di immagini valide in fase di esecuzione.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Creare un pool Linux: Batch Python
Il frammento di codice seguente mostra un esempio di come usare la [libreria client di Microsoft Azure batch per Python][py_batch_package] per creare un pool di nodi di calcolo Ubuntu Server. La documentazione di riferimento per il modulo Python di batch è disponibile nel [pacchetto Azure. batch][py_batch_docs] per leggere i documenti.

Questo frammento di codice crea un [ImageReference][py_imagereference] in modo esplicito e specifica ognuna delle proprietà (editore, offerta, SKU, versione). Nel codice di produzione, tuttavia, è consigliabile usare il metodo [list_node_agent_skus][py_list_skus] per determinare e selezionare le combinazioni di immagine e SKU dell'agente del nodo disponibili in fase di esecuzione.

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
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Come indicato in precedenza, invece di creare [ImageReference][py_imagereference] in modo esplicito, si usa il metodo [list_node_agent_skus][py_list_skus] per effettuare una selezione dinamica tra le combinazioni di agente nodo/immagine del Marketplace attualmente supportate. Il frammento di codice Python seguente illustra come usare questo metodo.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Creare un pool Linux: Batch .NET
Il frammento di codice seguente mostra un esempio di come usare la libreria client [batch .NET][nuget_batch_net] per creare un pool di nodi di calcolo Ubuntu Server. È possibile trovare la [documentazione di riferimento di batch .NET][api_net] in docs.Microsoft.com.

Nel frammento di codice seguente viene usato [PoolOperations][net_pool_ops]. Metodo [ListNodeAgentSkus][net_list_skus] per la selezione dall'elenco delle combinazioni di immagini del Marketplace e SKU dell'agente del nodo attualmente supportate. Questa tecnica è consigliabile perché l'elenco di combinazioni supportate può cambiare nel tempo, in genere a causa dell'aggiunta di altre combinazioni supportate.

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
    imageRef.Sku.Contains("14.04");

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
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Sebbene il frammento di codice precedente usi [PoolOperations][net_pool_ops]. Metodo [ListNodeAgentSkus][net_list_skus] per elencare dinamicamente e selezionare le combinazioni di immagine e SKU dell'agente del nodo supportate (scelta consigliata), è anche possibile configurare un [ImageReference][net_imagereference] in modo esplicito:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Elenco di immagini di macchine virtuali
La tabella seguente elenca le immagini di macchina virtuale del Marketplace compatibili con gli agenti del nodo Batch disponibili al momento dell'ultimo aggiornamento di questo articolo. È importante notare che questo elenco non è definitivo, perché è possibile che immagini e agenti del nodo vengano aggiunti o rimossi in qualsiasi momento. È consigliabile che le applicazioni e i servizi batch usino sempre [list_node_agent_skus][py_list_skus] (Python) o [ListNodeAgentSkus][net_list_skus] (batch .NET) per determinare e selezionare gli SKU attualmente disponibili.

> [!WARNING]
> L'elenco seguente può essere modificato in qualsiasi momento. Usare sempre i metodi di tipo **list node agent SKU** disponibili nelle API Batch per elencare le combinazioni compatibili di macchina virtuale e SKU dell'agente del nodo quando si eseguono i processi Batch.
>
>

| **Autore** | **Offerta** | **SKU dell'immagine** | **Versione** | **ID dello SKU dell'agente del nodo** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| o batch | rendering-centos73 | rendering | latest | batch.node.centos 7 |
| o batch | rendering-windows2016 | rendering | latest | batch.node.windows amd64 |
| Canonico | UbuntuServer | 16.04-LTS | latest | batch.node.ubuntu 16.04 |
| Canonico | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| Credativ | Debian | 9 | latest | batch.node.debian 9 |
| Credativ | Debian | 8 | latest | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | batch.node.windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008 R2-SP1 | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | latest | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Connettersi a nodi Linux tramite SSH
Durante lo sviluppo o la risoluzione dei problemi potrebbe essere necessario accedere ai nodi del pool. A differenza dei nodi di calcolo Windows, non è possibile usare il protocollo RDP (Remote Desktop Protocol) per connettersi ai nodi Linux. Il servizio Batch consente invece l'accesso SSH in ogni nodo per la connessione remota.

Il frammento di codice Python seguente crea un utente in ogni nodo in un pool, necessario per la connessione remota, quindi stampa le informazioni di connessione Secure Shell (SSH) per ogni nodo.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
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

Invece di una password, è possibile specificare una chiave pubblica SSH durante la creazione di un utente in un nodo. In Python SDK usare il parametro **ssh_public_key** in [ComputeNodeUser][py_computenodeuser]. In .NET usare [ComputeNodeUser][net_computenodeuser]. Proprietà [SshPublicKey][net_ssh_key] .

## <a name="pricing"></a>Prezzi
Azure Batch è basato sulla tecnologia di Servizi cloud di Azure e di Macchine virtuali di Azure. Il servizio Batch stesso è gratuito, ovvero vengono addebitate solo le risorse di calcolo utilizzate dalle soluzioni Batch. Quando si sceglie la **configurazione di servizi cloud**, l'addebito viene addebitato in base alla struttura dei [prezzi dei servizi cloud][cloud_services_pricing] . Quando si sceglie **configurazione macchina virtuale**, l'addebito viene addebitato in base alla struttura dei [prezzi delle macchine virtuali][vm_pricing] . 

Se si distribuiscono applicazioni ai nodi Batch tramite [pacchetti dell'applicazione](batch-application-packages.md), vengono inoltre addebitati i costi per le risorse di Archiviazione di Azure utilizzate dai pacchetti dell'applicazione. In generale, i costi di Archiviazione di Azure sono minimi. 

## <a name="next-steps"></a>Passaggi successivi

Gli [esempi di codice Python][github_samples_py] nel repository [Azure-batch-Samples][github_samples] in GitHub contengono script che illustrano come eseguire operazioni batch comuni, ad esempio la creazione di pool, processi e attività. Il [file Leggimi][github_py_readme] che accompagna gli esempi di Python contiene informazioni dettagliate su come installare i pacchetti necessari.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
