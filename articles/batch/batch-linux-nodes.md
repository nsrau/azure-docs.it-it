---
title: Eseguire Linux nei nodi di calcolo delle macchine virtuali
description: Informazioni su come elaborare i carichi di lavoro di calcolo paralleli nei pool di macchine virtuali Linux in Azure Batch.
ms.topic: how-to
ms.date: 06/01/2018
ms.custom: H1Hack27Feb2017, tracking-python
ms.openlocfilehash: 05e23093a00eab70db683c10eae59ef27152d2f7
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145041"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Eseguire il provisioning di nodi di calcolo Linux nei pool di Batch

È possibile usare Azure Batch per eseguire carichi di lavoro di calcolo paralleli in macchine virtuali Linux e Windows. Questo articolo illustra in dettaglio come creare pool di nodi di calcolo Linux nel servizio Batch usando le librerie client [Batch Python][py_batch_package] e [Batch .NET][api_net].

> [!NOTE]
> I pacchetti dell'applicazione sono supportati in tutti i pool di Batch creati dopo il 5 luglio 2017. Sono supportati nei pool di Batch creati tra il 10 marzo 2016 e il 5 luglio 2017 solo se il pool è stato creato usando una configurazione del servizio cloud. I pool di batch creati prima del 10 marzo 2016 non supportano i pacchetti dell'applicazione. Per altre informazioni sull'uso di pacchetti dell'applicazione per distribuire applicazioni ai nodi Batch, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Configurazione della macchina virtuale
Quando si crea un pool di nodi di calcolo in Batch, sono disponibili due opzioni per la selezione delle dimensioni e del sistema operativo del nodo: la configurazione di servizi cloud e la configurazione della macchina virtuale.

**Cloud Services Configuration** (Configurazione servizi cloud) fornisce *solo*nodi di calcolo Windows. Le dimensioni disponibili per i nodi di calcolo sono elencate in [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md) e i sistemi operativi disponibili sono elencati in [Rilasci del sistema operativo guest Azure e matrice di compatibilità dell'SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando si crea un pool che contiene nodi di Servizi cloud di Azure, è necessario specificare le dimensioni del nodo e la rispettiva famiglia del sistema operativo, descritti negli articoli menzionati in precedenza. Quando si creano pool di nodi di calcolo Windows, viene in genere usata l'opzione Servizi cloud.

**Configurazione macchina virtuale** fornisce immagini Linux e Windows per i nodi di calcolo. Le dimensioni disponibili per i nodi di calcolo sono elencate in [Dimensioni delle macchine virtuali in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) e [Dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Quando si crea un pool contenente nodi con configurazione della macchina virtuale, è necessario specificare le dimensioni dei nodi, il riferimento a un'immagine della macchina virtuale e lo SKU dell'agente del nodo Batch da installare nei nodi.

### <a name="virtual-machine-image-reference"></a>Riferimento all'immagine della macchina virtuale

Il servizio Batch usa [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md) per fornire nodi di calcolo nella configurazione macchina virtuale. È possibile specificare un'immagine di [Azure Marketplace][vm_marketplace] o un'immagine personalizzata precedentemente preparata. Per altri dettagli sulle immagini personalizzate, vedere [Creare un pool con Raccolta immagini condivise](batch-sig-images.md).

Quando si configura un riferimento all'immagine di macchina virtuale, si specificano le proprietà di un'immagine di macchina virtuale. Le proprietà seguenti sono necessarie quando si crea un riferimento all'immagine di macchina virtuale:

| **Proprietà del riferimento all'immagine** | **Esempio** |
| --- | --- |
| Editore |Canonical |
| Offerta |UbuntuServer |
| SKU |18.04-LTS |
| Versione |più recenti |

> [!TIP]
> Per altre informazioni su queste proprietà e su come elencare le immagini del Marketplace, vedere [Selezionare immagini di VM Linux con l'interfaccia della riga di comando di Azure](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Si noti che non tutte le immagini del Marketplace sono attualmente compatibili con Batch. Per altre informazioni, vedere [SKU dell'agente del nodo](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>SKU dell'agente del nodo
L'agente del nodo Batch è un programma in esecuzione in ogni nodo del pool e fornisce l'interfaccia di comando e controllo tra il nodo e il servizio Batch. Sono disponibili diverse implementazioni dell'agente del nodo, definite SKU, per sistemi operativi diversi. Quando si crea una configurazione della macchina virtuale, è essenzialmente necessario specificare prima di tutto il riferimento all'immagine della macchina virtuale e quindi l'agente del nodo da installare nell'immagine. Ogni SKU dell'agente del nodo è in genere compatibile con più immagini di macchina virtuale. Ecco alcuni esempi di SKU dell'agente del nodo:

* batch.node.ubuntu 18.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Non tutte le immagini di macchina virtuale disponibili nel Marketplace sono compatibili con gli agenti del nodo Batch attualmente disponibili. È necessario usare SDK di Batch per elencare gli SKU dell'agente del nodo disponibili e le immagini di macchina virtuale con cui sono compatibili. Vedere [Elenco di immagini di macchine virtuali](#list-of-virtual-machine-images) più avanti in questo articolo per altre informazioni ed esempi su come recuperare un elenco di immagini valide in fase di esecuzione.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Creare un pool Linux: Batch Python
Il frammento di codice seguente illustra un esempio su come usare la [libreria client di Microsoft Azure Batch per Python][py_batch_package] per creare un pool di nodi di calcolo Ubuntu Server. La documentazione di riferimento per il modulo Batch Python è disponibile in [azure.batch package ][py_batch_docs] in Read the Docs.

Questo frammento di codice crea in modo esplicito una classe [ImageReference][py_imagereference] e ne specifica tutte le proprietà (editore, offerta, SKU, versione). Nel codice di produzione è tuttavia consigliabile usare il metodo [list_node_agent_skus][py_list_supported_images] per determinare e selezionare una delle combinazioni disponibili di immagine e SKU dell'agente del nodo in fase di esecuzione.

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
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Come indicato in precedenza, invece di creare la classe [ImageReference][py_imagereference] in modo esplicito, è consigliabile usare il metodo [list_supported_images][py_list_supported_images] per selezionare in modo dinamico una delle combinazioni di agente del nodo e immagine del Marketplace attualmente supportate. Il frammento di codice Python seguente illustra come usare questo metodo.

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
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Creare un pool Linux: Batch .NET
Il frammento di codice seguente illustra un esempio di come usare la libreria client [Batch .NET][nuget_batch_net] per creare un pool di nodi di calcolo Ubuntu Server. La [documentazione di riferimento di Batch .NET][api_net] è disponibile su docs.microsoft.com.

Il frammento di codice seguente usa il metodo [PoolOperations][net_pool_ops].[ListSupportedImages][net_list_supported_images] per selezionare dall'elenco una delle combinazioni di immagine del Marketplace e SKU dell'agente del nodo attualmente supportate. Questa tecnica è consigliabile perché l'elenco di combinazioni supportate può cambiare nel tempo, in genere a causa dell'aggiunta di altre combinazioni supportate.

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

Anche se il frammento di codice precedente usa il metodo [PoolOperations][net_pool_ops].[ListSupportedImages][net_list_supported_images] per elencare in modo dinamico e selezionare una delle combinazioni di immagine e SKU dell'agente del nodo supportate (consigliato), è anche possibile configurare una classe [ImageReference][net_imagereference] in modo esplicito:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Elenco di immagini di macchine virtuali
Per ottenere l'elenco di tutte le immagini delle macchine virtuali del Marketplace supportate per il servizio Batch e gli agenti del nodo corrispondenti, usare [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) o l'API corrispondente nel rispettivo SDK del linguaggio scelto.

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

Invece di una password, è possibile specificare una chiave pubblica SSH durante la creazione di un utente in un nodo. In Python SDK usare il parametro **ssh_public_key** in [ComputeNodeUser][py_computenodeuser]. In .NET usare la proprietà [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## <a name="pricing"></a>Prezzi
Azure Batch è basato sulla tecnologia di Servizi cloud di Azure e di Macchine virtuali di Azure. Il servizio Batch è gratuito, vengono quindi addebitate solo le risorse di calcolo usate dalle soluzioni Batch e i costi associati che comportano. Scegliendo la **configurazione di Servizi cloud**, i costi verranno addebitati in base alla struttura dei [prezzi di Servizi cloud][cloud_services_pricing]. Scegliendo la **configurazione della macchina virtuale**, i costi verranno addebitati in base alla struttura dei [prezzi di Macchine virtuali][vm_pricing].

Se si distribuiscono applicazioni ai nodi Batch tramite [pacchetti dell'applicazione](batch-application-packages.md), vengono inoltre addebitati i costi per le risorse di Archiviazione di Azure utilizzate dai pacchetti dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Gli [esempi di codice Python][github_samples_py] nel repository [azure-batch-samples][github_samples] in GitHub contengono script che illustrano come eseguire operazioni Batch comuni, ad esempio la creazione di pool, processi e attività. Il file [README][github_py_readme] associato agli esempi di Python include dettagli sull'installazione dei pacchetti necessari.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_mgmt]: /dotnet/api/overview/azure/batch
[api_rest]: /rest/api/batchservice/
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: /dotnet/api/microsoft.azure.batch.imagereference
[net_list_supported_images]: /dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: /rest/api/batchservice/pool/add
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: /python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
