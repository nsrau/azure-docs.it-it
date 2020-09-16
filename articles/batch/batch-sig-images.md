---
title: Usare la raccolta di immagini condivise per creare un pool di immagini personalizzato
description: I pool di immagini personalizzati sono un modo efficiente per configurare i nodi di calcolo per l'esecuzione dei carichi di lavoro di batch.
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 31fcbff50a2a66aec1643f1bac351e0401205861
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605193"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Usare la raccolta di immagini condivise per creare un pool di immagini personalizzato

Quando si crea un pool in Azure Batch usando la configurazione della macchina virtuale, specificare l'immagine di macchina virtuale (VM) che fornisce la configurazione del sistema operativo per ogni nodo di calcolo nel pool. È possibile creare un pool di macchine virtuali con un'immagine di Azure Marketplace supportata o creare un'immagine personalizzata con un' [immagine della raccolta immagini condivisa](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Vantaggi della Raccolta immagini condivise

Quando si usa la Raccolta immagini condivise per l' immagine personalizzata, si possiede il controllo sul tipo di sistema operativo e la configurazione, nonché sul tipo di dischi dati. L'Immagine condivisa può includere applicazioni e dati di riferimento che diventano disponibili in tutti i nodi del pool di Azure Batch non appena viene effettuato il provisioning.

È possibile inoltre avere più versioni di un'immagine in base alle necessità del proprio ambiente. Quando si usa una versione dell'immagine per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale.

L'uso di un'Immagine condivisa permette di risparmiare tempo nel preparare i nodi di calcolo del pool per l'esecuzione del carico di lavoro di Batch. È possibile usare un'immagine di Azure Marketplace e installare il software in ogni nodo di calcolo dopo averne effettuato il provisioning, tuttavia l'uso di un'Immagine condivisa in genere è più efficiente. Inoltre, è possibile specificare più repliche per l'Immagine condivisa, in modo tale che quando si creano pool con molte VM (più di 600), si risparmia tempo durante la creazione degli stessi.

L'uso di un'Immagine condivisa configurata per uno scenario specifico può essere caratterizzato da numerosi vantaggi:

- **Usare le stesse immagini tra le diverse aree.** È possibile creare repliche di Immagini condivise tra diverse aree in modo che tutti i pool usino la stessa immagine.
- **Configurare il sistema operativo.** È possibile personalizzare la configurazione del disco del sistema operativo dell'immagine.
- **Pre-installare applicazioni**. La pre-installazione delle applicazioni sul disco del sistema operativo risulta essere più efficiente e meno soggetta a errori rispetto all'installazione di applicazioni dopo il provisioning dei nodi di calcolo con un'attività di avvio.
- **Copiare grandi quantità di dati una volta sola.** Incorporare i dati statici nell'Immagine condivisa gestita copiandoli nei dischi dati di un'immagine gestita. Questa operazione deve essere eseguita solo una volta e consente di rendere i dati disponibili per ogni nodo del pool.
- **Aumentare le dimensioni dei pool.** Con la Raccolta immagini condivise è possibile creare pool di dimensioni maggiori con le immagini personalizzate, insieme a un maggior numero di repliche di Immagini condivise.
- **Prestazioni migliori rispetto all'utilizzo di una sola immagine gestita come immagine personalizzata.** Per un pool di immagini personalizzate per immagini condivise, il tempo per raggiungere lo stato stabile è più veloce del 25% e la latenza di inattività della macchina virtuale è fino al 30% più breve.
- **Controllo delle versioni delle immagini e raggruppamento per la semplificazione della gestione.** La definizione di raggruppamento delle immagini contiene informazioni sui motivi per cui è stata creata l'immagine, sul sistema operativo per cui è stata creata e le informazioni sull'uso. Il raggruppamento delle immagini consente una gestione più semplice delle stesse. Per altre informazioni, vedere [Definizioni delle immagini](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> È necessario eseguire l'autenticazione con Azure AD. Se si usa shared-key-auth, si riceve un errore di autenticazione.  

- Un **account Azure Batch**. Per creare un account Batch, vedere le guide introduttive di Batch con il [portale di Azure](quick-create-portal.md) o l'[interfaccia della riga di comando di Azure](quick-create-cli.md).

- **Un'immagine Raccolta immagini condivise**. Per creare un'Immagine condivisa, è necessario avere o creare una risorsa immagine gestita. È consigliabile creare l'immagine dagli snapshot del disco del sistema operativo della macchina virtuale e, facoltativamente, i relativi dischi dati collegati.

> [!NOTE]
> Se l'immagine condivisa non si trova nella stessa sottoscrizione dell'account batch, è necessario [registrare il provider di risorse Microsoft.Batch](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) per tale sottoscrizione. Le due sottoscrizioni devono trovarsi nello stesso tenant Azure AD.
>
> L'immagine può trovarsi in un'area diversa, purché includa repliche nella stessa area dell'account batch.

Se si usa un'applicazione Azure AD per creare un pool di immagini personalizzato con un'immagine della raccolta immagini condivisa, a tale applicazione deve essere stato concesso un [ruolo predefinito di Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) che consente di accedere all'immagine condivisa. È possibile concedere questo accesso nel portale di Azure passando all'immagine condivisa, selezionando **controllo di accesso (IAM)** e aggiungendo un'assegnazione di ruolo per l'applicazione.

## <a name="prepare-a-shared-image"></a>Preparare un'immagine condivisa

In Azure è possibile preparare un'immagine condivisa da un'immagine gestita, che può essere creata da:

- Snapshot dei dischi dati e sistema operativo di una macchina virtuale di Azure
- Una macchina virtuale generalizzata di Azure con dischi gestiti
- Un disco rigido virtuale locale generalizzato caricato nel cloud

> [!NOTE]
> Attualmente, Batch supporta solo Immagini condivise generalizzate. Al momento non è possibile creare un pool di immagini personalizzato da un'Immagine condivisa specializzata.

I passaggi seguenti illustrano come preparare una macchina virtuale, acquisire uno snapshot e creare un'immagine dallo snapshot.

### <a name="prepare-a-vm"></a>Preparare una VM

Se si crea una nuova macchina virtuale per l'immagine, usare un'immagine produttore di Azure Marketplace supportata da Batch come immagine di base per l'immagine gestita. Solo le immagini produttore possono essere usate come immagine di base. Per ottenere un elenco completo di riferimenti a immagini di Azure Marketplace supportate da Azure Batch, vedere l'operazione [List node agent SKUs](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus).

> [!NOTE]
> È possibile usare un'immagine di terze parti che dispone di licenza aggiuntiva e di condizioni di acquisto come immagine di base. Per informazioni su queste immagini del Marketplace, vedere il materiale sussidiario per le macchine virtuali [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) o [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms).

- Assicurarsi che la macchina virtuale venga creata con un disco gestito. Questa è l'impostazione di archiviazione predefinita quando si crea una macchina virtuale.
- Non installare le estensioni di Azure, ad esempio l'estensione Script personalizzato, nella macchina virtuale. Se l'immagine contiene un'estensione preinstallata, Azure può incontrare alcuni problemi durante la distribuzione del pool di Batch.
- Quando si usano dischi dati allegati è necessario montare e formattare i dischi all'interno di una macchina virtuale per poterli usare.
- Verificare che l'immagine del sistema operativo di base usi l'unità temporanea predefinita. L'agente del nodo Batch attualmente prevede l'uso dell'unità temporanea predefinita.
- Quando la VM è in esecuzione, connetterla tramite RDP (per Windows) o SSH (per Linux). Installare il software necessario o copiare i dati desiderati.  

### <a name="create-a-vm-snapshot"></a>Creare uno snapshot della macchina virtuale

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. Per creare uno snapshot del sistema operativo o dei dischi dati di una macchina virtuale, è possibile usare il portale di Azure o gli strumenti da riga di comando. Per i passaggi e le opzioni per creare uno snapshot, vedere il materiale sussidiario per le VM [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) o [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Creare un'immagine da uno o più snapshot

Per creare un'immagine gestita da uno snapshot, usare gli strumenti da riga di comando di Azure, ad esempio il comando [az image create](/cli/azure/image). Creare un'immagine specificando uno snapshot del disco del sistema operativo e, facoltativamente, uno o più snapshot dei dischi dati.

### <a name="create-a-shared-image-gallery"></a>Creare una Raccolta immagini condivise

Dopo aver creato l'immagine gestita è necessario creare una Raccolta immagini condivise per rendere disponibile l'immagine personalizzata. Per informazioni su come creare una Raccolta immagini condivise per le immagini, vedere [Creare una raccolta di immagini condivise con l'interfaccia della riga di comando di Azure](../virtual-machines/shared-images-cli.md) oppure [Creare una raccolta di immagini condivise con il portale di Azure](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Creare un pool da Immagini condivise con l'interfaccia della riga di comando di Azure

Per creare un pool dalle Immagini condivise con l'interfaccia della riga di comando di Azure, usare il comando `az batch pool create`. Specificare l'ID Immagine condivisa nel campo `--image`. Assicurarsi che il tipo di sistema operativo e lo SKU corrispondano alle versioni specificate da `--node-agent-sku-id`

> [!NOTE]
> È necessario eseguire l'autenticazione con Azure AD. Se si usa shared-key-auth, si riceve un errore di autenticazione.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Creare un pool da Immagini condivise con C#

In alternativa, è possibile creare un pool da un'Immagine condivisa con l'SDK C#.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Creare un pool da Immagini condivise con Python

È inoltre possibile creare un pool da un'Immagine condivisa con l'SDK Python: 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Creare un pool da un'Immagine condivisa con l'interfaccia della il portale di Azure

Usare la procedura seguente per creare un pool da un'Immagine condivisa nel portale di Azure.

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Passare ad **account Batch** e selezionare l'account.
1. Selezionare **Pool** quindi **Aggiungi** per creare un nuovo pool.
1. Nella sezione **Tipo di immagine** selezionare **Raccolta immagini condivise**.
1. Completare le sezioni rimanenti con le informazioni relative all'immagine gestita.
1. Selezionare **OK**.

![Creare un pool da un'Immagine condivisa con il portale.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerazioni per i pool di grandi dimensioni

Se si prevede di creare un pool con centinaia o migliaia di VM o più con un'immagine condivisa, usare le indicazioni seguenti.

- **Numeri di replica di Raccolta immagini condivise.**  Per ogni pool che contiene fino a 600 istanze, si consiglia di mantenere almeno una replica. Se, ad esempio, si sta creando un pool con 3000 macchine virtuali è necessario mantenere almeno 5 repliche dell'immagine. Per ottenere prestazioni migliori, è sempre consigliabile mantenere un numero maggiore di repliche rispetto ai requisiti minimi.

- **Timeout ridimensionamento.** Aumentare la proprietà `resizeTimeout` del pool in base alle dimensioni del pool, se il pool contiene un numero fisso di nodi (e se non viene ridimensionato automaticamente). Per ogni 1000 VM, il timeout di ridimensionamento consigliato è di almeno 15 minuti. Ad esempio, il timeout di ridimensionamento consigliato per un pool con 2000 macchine virtuali è di almeno 30 minuti.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica approfondita di Batch, vedere [Flusso di lavoro e risorse del servizio Batch](batch-service-workflow-features.md).
- Informazioni su [Raccolta immagini condivise](../virtual-machines/windows/shared-image-galleries.md).
