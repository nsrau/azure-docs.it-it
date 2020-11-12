---
title: Eseguire Linux nei nodi di calcolo delle macchine virtuali
description: Informazioni su come elaborare carichi di lavoro di calcolo paralleli nei pool di macchine virtuali Linux in Azure Batch.
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: 0a9c801a13af05f077b87f296992da7f50742e4b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533498"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Eseguire il provisioning di nodi di calcolo Linux nei pool di Batch

È possibile usare Azure Batch per eseguire carichi di lavoro di calcolo paralleli in macchine virtuali Linux e Windows. Questo articolo illustra in dettaglio come creare pool di nodi di calcolo Linux nel servizio Batch usando le librerie client [Batch Python](https://pypi.python.org/pypi/azure-batch) e [Batch .NET](/dotnet/api/microsoft.azure.batch). 

## <a name="virtual-machine-configuration"></a>Configurazione macchina virtuale

Quando si crea un pool di nodi di calcolo in Batch, sono disponibili due opzioni per la selezione delle dimensioni e del sistema operativo del nodo: la configurazione di servizi cloud e la configurazione della macchina virtuale. La maggior parte dei pool di nodi di calcolo di Windows usa la [configurazione dei servizi cloud](nodes-and-pools.md#cloud-services-configuration), che specifica che il pool è costituito da nodi di servizi cloud di Azure. Questi pool forniscono solo nodi di calcolo di Windows.

Al contrario, la [configurazione della macchina virtuale](nodes-and-pools.md#virtual-machine-configuration) specifica che il pool è costituito da macchine virtuali di Azure, che possono essere create da immagini Linux o Windows. Quando si crea un pool con la configurazione della macchina virtuale, è necessario specificare una [dimensione del nodo di calcolo disponibile](../virtual-machines/sizes.md), il riferimento all'immagine della macchina virtuale e lo SKU dell'agente del nodo batch (un programma eseguito in ogni nodo e fornisce un'interfaccia tra il nodo e il servizio batch) e il riferimento all'immagine della macchina virtuale che verrà installato nei nodi.

### <a name="virtual-machine-image-reference"></a>Riferimento all'immagine della macchina virtuale

Il servizio Batch usa [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md) per fornire nodi di calcolo nella configurazione macchina virtuale. È possibile specificare un'immagine da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)oppure [usare la raccolta di immagini condivise per preparare un'immagine personalizzata](batch-sig-images.md).

Quando si crea un riferimento a un'immagine di macchina virtuale, è necessario specificare le proprietà seguenti:

| **Proprietà riferimento immagine** | **Esempio** |
| --- | --- |
| Editore |Canonical |
| Offerta |UbuntuServer |
| SKU |18.04-LTS |
| Versione |più recenti |

> [!TIP]
> Per altre informazioni su queste proprietà e su come specificare immagini del Marketplace, [vedere trovare immagini di VM Linux in Azure Marketplace con l'interfaccia della](../virtual-machines/linux/cli-ps-findimage.md)riga di comando di Azure. Si noti che non tutte le immagini del Marketplace sono attualmente compatibili con Batch.

### <a name="node-agent-sku"></a>SKU dell'agente del nodo

L' [agente del nodo batch](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) è un programma che viene eseguito in ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio batch. Sono disponibili diverse implementazioni dell'agente del nodo, definite SKU, per sistemi operativi diversi. Quando si crea una configurazione della macchina virtuale, è essenzialmente necessario specificare prima di tutto il riferimento all'immagine della macchina virtuale e quindi l'agente del nodo da installare nell'immagine. Ogni SKU dell'agente del nodo è in genere compatibile con più immagini di macchina virtuale. Ecco alcuni esempi di SKU dell'agente del nodo:

- batch.node.ubuntu 18.04
- batch.node.centos 7
- batch.node.windows amd64

### <a name="list-of-virtual-machine-images"></a>Elenco di immagini di macchine virtuali

Non tutte le immagini del Marketplace sono compatibili con gli agenti del nodo batch attualmente disponibili. Per elencare tutte le immagini di macchine virtuali del Marketplace supportate per il servizio batch e i relativi SKU dell'agente del nodo, usare [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (batch .NET) o l'API corrispondente in un altro SDK per il linguaggio.

## <a name="create-a-linux-pool-batch-python"></a>Creare un pool Linux: Batch Python

Il frammento di codice seguente illustra un esempio su come usare la [libreria client di Microsoft Azure Batch per Python](https://pypi.python.org/pypi/azure-batch) per creare un pool di nodi di calcolo Ubuntu Server. Per altri dettagli sul modulo batch Python, vedere la [documentazione di riferimento](/python/api/overview/azure/batch).

Questo frammento di codice crea in modo esplicito una classe [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) e ne specifica tutte le proprietà (editore, offerta, SKU, versione). Nel codice di produzione è tuttavia consigliabile usare il metodo [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) per effettuare una selezione tra le combinazioni di immagine e SKU dell'agente del nodo disponibili in fase di esecuzione.

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
vm_size = "STANDARD_D2_V3"
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
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Come indicato in precedenza, è consigliabile usare il metodo [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) per effettuare una selezione dinamica tra le combinazioni di agente del nodo e dell'immagine del Marketplace attualmente supportate, anziché creare in modo esplicito un [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) . Il frammento di codice Python seguente illustra come usare questo metodo.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Creare un pool Linux: Batch .NET

Il frammento di codice seguente illustra un esempio di come usare la libreria client [Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) per creare un pool di nodi di calcolo Ubuntu Server. Per altri dettagli su batch .NET, vedere la [documentazione di riferimento](/dotnet/api/microsoft.azure.batch).

Il frammento di codice seguente usa il metodo [PoolOperations. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) per effettuare una selezione dall'elenco delle combinazioni di immagini del Marketplace e SKU dell'agente del nodo attualmente supportate. Questa tecnica è consigliata perché l'elenco di combinazioni supportate può variare di tanto in tanto. in genere a causa dell'aggiunta di altre combinazioni supportate.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

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

Anche se il frammento di codice precedente usa il metodo [PoolOperations. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) per elencare dinamicamente e selezionare le combinazioni di immagine e SKU dell'agente del nodo supportate (scelta consigliata), è anche possibile configurare un [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) in modo esplicito:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Connettersi a nodi Linux tramite SSH

Durante lo sviluppo o la risoluzione dei problemi potrebbe essere necessario accedere ai nodi del pool. A differenza dei nodi di calcolo di Windows, non è possibile usare Remote Desktop Protocol (RDP) per connettersi ai nodi Linux. Il servizio Batch consente invece l'accesso SSH in ogni nodo per la connessione remota.

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

Questo codice avrà un output simile all'esempio seguente. In questo caso, il pool contiene quattro nodi Linux.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Invece di una password, è possibile specificare una chiave pubblica SSH durante la creazione di un utente in un nodo. In Python SDK usare il parametro **ssh_public_key** in [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). In .NET usare la proprietà [ComputeNodeUser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) .

## <a name="pricing"></a>Prezzi

Azure Batch è basato sulla tecnologia di Servizi cloud di Azure e di Macchine virtuali di Azure. Il servizio Batch è gratuito, vengono quindi addebitate solo le risorse di calcolo usate dalle soluzioni Batch e i costi associati che comportano. Scegliendo la **configurazione della macchina virtuale** , i costi verranno addebitati in base alla struttura dei [prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/).

Se si distribuiscono applicazioni ai nodi Batch tramite [pacchetti dell'applicazione](batch-application-packages.md), vengono inoltre addebitati i costi per le risorse di Archiviazione di Azure utilizzate dai pacchetti dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- Esplorare gli [esempi di codice Python](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) nel [repository GitHub Azure-batch-Samples](https://github.com/Azure/azure-batch-samples) per informazioni su come eseguire operazioni batch comuni, ad esempio la creazione di pool, processi e attività. Il file [README](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) associato agli esempi di Python include dettagli sull'installazione dei pacchetti necessari.
- Informazioni sull'uso di [macchine virtuali con priorità bassa](batch-low-pri-vms.md) con batch.
