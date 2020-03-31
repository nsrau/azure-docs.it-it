---
title: Usare la raccolta di immagini condivise per creare un pool personalizzato - Azure Batch Documenti Microsoft
description: Creare un pool Batch con la raccolta immagini condivise per eseguire il provisioning di immagini personalizzate per calcolare i nodi che contengono il software e i dati necessari per l'applicazione. Le immagini personalizzate sono uno strumento efficace per configurare i nodi di calcolo per l'esecuzione dei carichi di lavoro di Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 2cff6a0e48fc7bf58a642f509fcda6b114e002ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022937"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Creare un pool personalizzato tappi

Quando si crea un pool in Azure Batch usando la configurazione della macchina virtuale, specificare l'immagine di macchina virtuale (VM) che fornisce la configurazione del sistema operativo per ogni nodo di calcolo nel pool. È possibile creare un pool di macchine virtuali con un'immagine di Azure Marketplace supportata o creare un'immagine personalizzata con la [raccolta immagini condivise](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Vantaggi della Galleria di immagini condivise

Quando si utilizza la Raccolta immagini condivise per l'immagine personalizzata, si ha il controllo sul tipo e la configurazione del sistema operativo, nonché sul tipo di dischi dati. L'immagine condivisa può includere applicazioni e dati di riferimento che diventano disponibili in tutti i nodi del pool Batch non appena ne viene eseguito il provisioning.

È inoltre possibile avere più versioni di un'immagine in base alle esigenze dell'ambiente. Quando si usa una versione dell'immagine per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale.

L'uso di un'immagine condivisa consente di risparmiare tempo nella preparazione dei nodi di calcolo del pool per eseguire il carico di lavoro Batch.Using a Shared Image saves time in preparing your pool's compute nodes to run your Batch workload. È possibile usare un'immagine di Azure Marketplace e installare il software in ogni nodo di calcolo dopo il provisioning, ma l'uso di un'immagine condivisa è in genere più efficiente. Inoltre, è possibile specificare più repliche per l'immagine condivisa in modo che quando si creano pool con molte macchine virtuali (più di 600 macchine virtuali), si farà risparmiare tempo durante la creazione del pool.

L'utilizzo di un'immagine condivisa configurata per lo scenario può offrire diversi vantaggi:Using a Shared Image configured for your scenario can provide several advantages:

* **Utilizzare le stesse immagini in tutte le regioni.** È possibile creare repliche di immagini condivise in aree diverse in modo che tutti i pool utilizzino la stessa immagine.
* **Configurare il sistema operativo (OS).** È possibile personalizzare la configurazione del disco del sistema operativo dell'immagine.
* **Pre-installare applicazioni**. La preinstallazione delle applicazioni sul disco del sistema operativo è più efficiente e meno soggetta a errori rispetto all'installazione delle applicazioni dopo il provisioning dei nodi di calcolo con un'attività di avvio.
* **Copiare grandi quantità di dati una sola volta.** Rendere i dati statici parte dell'immagine condivisa gestita copiandoli nei dischi dati di un'immagine gestita. Questa operazione deve essere eseguita solo una volta e consente di rendere i dati disponibili per ogni nodo del pool.
* **Cresci piscine a dimensioni maggiori.** Con la Raccolta immagini condivise, è possibile creare pool più grandi con le immagini personalizzate insieme a più repliche di immagini condivise.
* **Prestazioni migliori rispetto all'immagine personalizzata.** Usando le immagini condivise, il tempo necessario per il raggiungimento dello stato stabile del pool è fino al 25% più veloce e la latenza di inattività della macchina virtuale è fino al 30% più breve.
* **Controllo delle versioni delle immagini e raggruppamento per semplificarne la gestione.** La definizione di raggruppamento delle immagini contiene informazioni sul motivo per cui l'immagine è stata creata, sul sistema operativo a cui è destinata e sull'utilizzo dell'immagine. Il raggruppamento delle immagini consente una gestione più semplice delle immagini. Per ulteriori informazioni, consultate [Definizioni di immagini.](../virtual-machines/windows/shared-image-galleries.md#image-definitions)

## <a name="prerequisites"></a>Prerequisiti

* Un **account Azure Batch**. Per creare un account Batch, vedere le guide introduttive di Batch usando il [portale di Azure](quick-create-portal.md) o l'interfaccia della riga di comando di [Azure.](quick-create-cli.md)

* **Immagine della raccolta immagini condivisa**. Per creare un'immagine condivisa, è necessario disporre o creare una risorsa immagine gestita. È consigliabile creare l'immagine dagli snapshot del disco del sistema operativo della macchina virtuale e, facoltativamente, i relativi dischi dati collegati. Per ulteriori informazioni, consultate [Preparare un'immagine gestita.](#prepare-a-managed-image)

> [!NOTE]
> L'immagine condivisa deve essere nella stessa sottoscrizione dell'account Batch. L'immagine condivisa può trovarsi in aree diverse, purché contenga repliche nella stessa area dell'account Batch.

## <a name="prepare-a-managed-image"></a>Preparare un'immagine gestitaPrepare a managed image

In Azure è possibile preparare un'immagine gestita da:In Azure, you can prepare a managed image from:

* Snapshot del sistema operativo e dei dischi dati di una macchina virtuale di AzureSnapshots of an Azure VM's OS and data disks
* Una macchina virtuale di Azure generalizzata con dischi gestiti
* Un disco rigido virtuale locale generalizzato caricato nel cloud

Per ridimensionare i pool di Batch in modo affidabile con un'immagine personalizzata, si consiglia di creare un'immagine gestita usando *solo* il primo modo, ovvero usando gli snapshot dei dischi della macchina virtuale. Vedere i passaggi seguenti per preparare una macchina virtuale, acquisire uno snapshot e creare un'immagine dallo snapshot.

### <a name="prepare-a-vm"></a>Preparare una VM

Se si sta creando una nuova macchina virtuale per l'immagine, usare un'immagine di Azure Marketplace di prima entità supportata da Batch come immagine di base per l'immagine gestita. Solo le immagini di prima parte possono essere utilizzate come immagine di base. Per ottenere un elenco completo dei riferimenti alle immagini di Azure Marketplace supportati da Azure Batch, vedere operazione [SKU agente nodo elenco.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> È possibile usare un'immagine di terze parti che dispone di licenza aggiuntiva e di condizioni di acquisto come immagine di base. Per informazioni su queste immagini del Marketplace, vedere il materiale sussidiario per le macchine virtuali [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) o [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
).

* Verificare che la macchina virtuale venga creata con un disco gestito. Questa è l'impostazione di archiviazione predefinita quando si crea una macchina virtuale.
* Non installare le estensioni di Azure, ad esempio l'estensione Script personalizzato, nella macchina virtuale. Se l'immagine contiene un'estensione preinstallata, Azure può incontrare alcuni problemi durante la distribuzione del pool di Batch.
* Quando si usano dischi dati collegati, è necessario montare e formattare i dischi dall'interno di una macchina virtuale per usarli.
* Verificare che l'immagine del sistema operativo di base usi l'unità temporanea predefinita. L'agente del nodo Batch attualmente prevede l'uso dell'unità temporanea predefinita.
* Quando la VM è in esecuzione, connetterla tramite RDP (per Windows) o SSH (per Linux). Installare il software necessario o copiare i dati desiderati.  

### <a name="create-a-vm-snapshot"></a>Creare uno snapshot della macchina virtuale

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. Per creare uno snapshot del sistema operativo o dei dischi dati di una macchina virtuale, è possibile usare il portale di Azure o gli strumenti da riga di comando. Per i passaggi e le opzioni per creare uno snapshot, vedere il materiale sussidiario per le VM [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) o [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Creare un'immagine da uno o più snapshot

Per creare un'immagine gestita da uno snapshot, usare gli strumenti da riga di comando di Azure, ad esempio il comando [az image create](/cli/azure/image). Creare un'immagine specificando uno snapshot del disco del sistema operativo e facoltativamente uno o più snapshot del disco dati.

### <a name="create-a-shared-image-gallery"></a>Creare una Raccolta immagini condivise

Dopo aver creato correttamente l'immagine gestita, è necessario creare una raccolta immagini condivise per rendere disponibile l'immagine personalizzata. Per informazioni su come creare una raccolta di immagini condivise per le immagini, vedere Creare una raccolta di immagini condivise con l'interfaccia della riga di comando di Azure o Creare una raccolta di immagini condivise usando il portale di Azure.To learn how to create a Shared Image Gallery for your images, see [Create a Shared Image Gallery with Azure CLI](../virtual-machines/linux/shared-images.md) o Create a Shared Image Gallery using the Azure [portal.](../virtual-machines/linux/shared-images-portal.md)

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Creare un pool da un'immagine condivisa usando l'interfaccia della riga di comando di AzureCreate a pool from a Shared Image using the Azure CLI

Per creare un pool dall'immagine condivisa usando `az batch pool create` l'interfaccia della riga di comando di Azure, usare il comando. Specificare l'ID `--image` immagine condivisa nel campo. Assicurarsi che il tipo di sistema operativo e sKU corrispondano alle versioni specificate da`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Creare un pool da un'immagine condivisa usando CCreate a pool from a Shared Image using C #

In alternativa, è possibile creare un pool da un'immagine condivisa usando l'SDK di C.

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Creare un pool da un'immagine condivisa usando il portale di AzureCreate a pool from a Shared Image using the Azure portal

Usare la procedura seguente per creare un pool da un'immagine condivisa nel portale di Azure.Use the following steps to create a pool from a Shared Image in the Azure portal.

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Vai a **Account Batch** e seleziona il tuo account.
1. Selezionare **Pool** e quindi **Aggiungi** per creare un nuovo pool.
1. Nella sezione **Tipo di immagine** selezionare Raccolta immagini **condivise**.
1. Completare le sezioni rimanenti con le informazioni sull'immagine gestita.
1. Selezionare **OK**.

![Creare un pool con da un'immagine condivisa con il portale.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerazioni per i pool di grandi dimensioni

Se si prevede di creare un pool con centinaia o migliaia di macchine virtuali o più usando un'immagine condivisa, usare le indicazioni seguenti.

* **Numeri di replica della Raccolta immagini condivisa.**  Per ogni pool con un massimo di 600 istanze, è consigliabile mantenere almeno una replica. Ad esempio, se si sta creando un pool con 3000 macchine virtuali, è consigliabile mantenere almeno 5 repliche dell'immagine. Si consiglia sempre di mantenere più repliche rispetto ai requisiti minimi per migliorare le prestazioni.

* **Timeout di ridimensionamento.** Se il pool contiene un numero fisso di nodi `resizeTimeout` (se non viene ridimensionato automaticamente), aumentare la proprietà del pool in base alle dimensioni del pool. Per ogni 1000 macchine virtuali, il timeout di ridimensionamento consigliato è di almeno 15 minuti. Ad esempio, il timeout di ridimensionamento consigliato per un pool con 2000 macchine virtuali è di almeno 30 minuti.

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
