---
title: Usare la raccolta di immagini condivise per creare un pool personalizzato-Azure Batch | Microsoft Docs
description: Creare un pool di batch con la raccolta di immagini condivise per eseguire il provisioning di immagini personalizzate nei nodi di calcolo che contengono il software e i dati necessari per l'applicazione. Le immagini personalizzate sono uno strumento efficace per configurare i nodi di calcolo per l'esecuzione dei carichi di lavoro di Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: lahugh
ms.openlocfilehash: c3f5155c7d1576657f36445562b5b425148a838a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348963"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Usare la raccolta di immagini condivise per creare un pool personalizzato

Quando si crea un pool in Azure Batch usando la configurazione della macchina virtuale, specificare l'immagine di macchina virtuale (VM) che fornisce la configurazione del sistema operativo per ogni nodo di calcolo nel pool. È possibile creare un pool di macchine virtuali con un'immagine di Azure Marketplace supportata o creare un'immagine personalizzata con la [raccolta di immagini condivise](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Vantaggi della raccolta immagini condivise

Quando si usa la raccolta di immagini condivise per l'immagine personalizzata, è possibile controllare il tipo di sistema operativo e la configurazione, nonché il tipo di dischi dati. L'immagine condivisa può includere le applicazioni e i dati di riferimento che diventano disponibili in tutti i nodi del pool di batch non appena ne viene effettuato il provisioning.

È inoltre possibile disporre di più versioni di un'immagine in base alle esigenze dell'ambiente in uso. Quando si usa una versione di immagine per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale.

L'uso di un'immagine condivisa consente di risparmiare tempo nella preparazione dei nodi di calcolo del pool per l'esecuzione del carico di lavoro batch. È possibile usare un'immagine di Azure Marketplace e installare il software in ogni nodo di calcolo dopo il provisioning, ma l'uso di un'immagine condivisa è in genere più efficiente. Inoltre, è possibile specificare più repliche per l'immagine condivisa, in modo che quando si creano pool con molte VM (più di 600 VM), si risparmia tempo durante la creazione del pool.

L'uso di un'immagine condivisa configurata per lo scenario può offrire diversi vantaggi:

* **Usare le stesse immagini tra le aree.** È possibile creare repliche di immagini condivise in aree diverse in modo che tutti i pool utilizzino la stessa immagine.
* **Configurare il sistema operativo.** È possibile personalizzare la configurazione del disco del sistema operativo dell'immagine.
* **Pre-installare applicazioni**. La pre-installazione delle applicazioni nel disco del sistema operativo è più efficiente e meno soggetta a errori rispetto all'installazione di applicazioni dopo il provisioning dei nodi di calcolo con un'attività di avvio.
* **Copiare grandi quantità di dati una volta.** Fare in modo che una parte dei dati statici dell'immagine condivisa gestita venga copiata nei dischi dati di un'immagine gestita. Questa operazione deve essere eseguita solo una volta e consente di rendere i dati disponibili per ogni nodo del pool.
* **Espandere i pool in dimensioni maggiori.** Con la raccolta di immagini condivise è possibile creare pool di dimensioni maggiori con le immagini personalizzate insieme a più repliche di immagini condivise.
* **Prestazioni migliori rispetto all'immagine personalizzata.** Utilizzando immagini condivise, il tempo necessario affinché il pool raggiunga lo stato stabile è più veloce del 25% e la latenza di inattività della macchina virtuale è fino al 30% più breve.
* **Controllo delle versioni delle immagini e raggruppamento per semplificare la gestione.** La definizione di raggruppamento di immagini contiene informazioni sui motivi per cui è stata creata l'immagine, il sistema operativo per cui è stata creata e le informazioni sull'utilizzo dell'immagine. Il raggruppamento di immagini consente una gestione più semplice delle immagini. Per altre informazioni, vedere [definizioni di immagine](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Prerequisiti

* Un **account Azure Batch**. Per creare un account batch, vedere le guide introduttive di batch usando il [portale di Azure](quick-create-portal.md) o l'interfaccia della riga di comando di [Azure](quick-create-cli.md).

* **Immagine della raccolta di immagini condivise**. Per creare un'immagine condivisa, è necessario avere o creare una risorsa immagine gestita. È consigliabile creare l'immagine dagli snapshot del disco del sistema operativo della macchina virtuale e, facoltativamente, i relativi dischi dati collegati. Per altre informazioni, vedere [preparare un'immagine gestita](#prepare-a-managed-image).

> [!NOTE]
> L'immagine condivisa deve trovarsi nella stessa sottoscrizione dell'account batch. L'immagine condivisa può trovarsi in aree diverse, purché includa repliche nella stessa area dell'account batch.

## <a name="prepare-a-managed-image"></a>Preparare un'immagine gestita

In Azure è possibile preparare un'immagine gestita da:

* Snapshot del sistema operativo e dei dischi dati di una macchina virtuale di Azure
* Una macchina virtuale di Azure generalizzata con Managed Disks
* Un disco rigido virtuale generalizzato locale caricato nel cloud

Per ridimensionare i pool di Batch in modo affidabile con un'immagine personalizzata, si consiglia di creare un'immagine gestita usando *solo* il primo modo, ovvero usando gli snapshot dei dischi della macchina virtuale. Vedere i passaggi seguenti per preparare una macchina virtuale, acquisire uno snapshot e creare un'immagine dallo snapshot.

### <a name="prepare-a-vm"></a>Preparare una VM

Se si sta creando una nuova macchina virtuale per l'immagine, usare un'immagine di Azure Marketplace di prima entità supportata da batch come immagine di base per l'immagine gestita. Solo le immagini di primo entità possono essere utilizzate come immagine di base. Per ottenere un elenco completo dei riferimenti alle immagini di Azure Marketplace supportati da Azure Batch, vedere l'operazione [List node Agent SKU](/rest/api/batchservice/account/listnodeagentskus) .

> [!NOTE]
> È possibile usare un'immagine di terze parti che dispone di licenza aggiuntiva e di condizioni di acquisto come immagine di base. Per informazioni su queste immagini del Marketplace, vedere il materiale sussidiario per le macchine virtuali [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) o [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
).

* Assicurarsi che la macchina virtuale venga creata con un disco gestito. Questa è l'impostazione di archiviazione predefinita quando si crea una macchina virtuale.
* Non installare le estensioni di Azure, ad esempio l'estensione Script personalizzato, nella macchina virtuale. Se l'immagine contiene un'estensione preinstallata, Azure può incontrare alcuni problemi durante la distribuzione del pool di Batch.
* Quando si usano dischi dati collegati, è necessario montare e formattare i dischi all'interno di una macchina virtuale per usarli.
* Verificare che l'immagine del sistema operativo di base usi l'unità temporanea predefinita. L'agente del nodo Batch attualmente prevede l'uso dell'unità temporanea predefinita.
* Quando la VM è in esecuzione, connetterla tramite RDP (per Windows) o SSH (per Linux). Installare il software necessario o copiare i dati desiderati.  

### <a name="create-a-vm-snapshot"></a>Creare uno snapshot della macchina virtuale

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. Per creare uno snapshot del sistema operativo o dei dischi dati di una macchina virtuale, è possibile usare il portale di Azure o gli strumenti da riga di comando. Per i passaggi e le opzioni per creare uno snapshot, vedere il materiale sussidiario per le VM [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) o [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Creare un'immagine da uno o più snapshot

Per creare un'immagine gestita da uno snapshot, usare gli strumenti da riga di comando di Azure, ad esempio il comando [az image create](/cli/azure/image). Creare un'immagine specificando uno snapshot del disco del sistema operativo e, facoltativamente, uno o più snapshot del disco dati.

### <a name="create-a-shared-image-gallery"></a>Creare una raccolta di immagini condivise

Dopo aver creato l'immagine gestita, è necessario creare una raccolta di immagini condivise per rendere disponibile l'immagine personalizzata. Per informazioni su come creare una raccolta di immagini condivise per le immagini, vedere [creare una raccolta di immagini condivise con l'interfaccia](../virtual-machines/linux/shared-images.md) della riga di comando di Azure o [creare una raccolta di immagini condivise usando il portale di Azure](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Creare un pool da un'immagine condivisa usando l'interfaccia della riga di comando di Azure

Per creare un pool dall'immagine condivisa usando l'interfaccia della riga di comando di Azure, usare il comando `az batch pool create`. Specificare l'ID immagine condivisa nel campo `--image`. Verificare che il tipo di sistema operativo e lo SKU corrispondano alle versioni specificate da `--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Creare un pool da un'immagine condivisa usandoC#

In alternativa, è possibile creare un pool da un'immagine condivisa usando l' C# SDK.

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Creare un pool da un'immagine condivisa usando il portale di Azure

Usare la procedura seguente per creare un pool da un'immagine condivisa nel portale di Azure.

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Passare a **account batch** e selezionare l'account.
1. Selezionare **pool** e quindi **Aggiungi** per creare un nuovo pool.
1. Nella sezione **tipo di immagine** selezionare **raccolta immagini condivise**.
1. Completare le sezioni rimanenti con le informazioni sull'immagine gestita.
1. Scegliere **OK**.

![Creare un pool con da un'immagine condivisa con il portale.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerazioni per i pool di grandi dimensioni

Se si prevede di creare un pool con centinaia o migliaia di VM o più usando un'immagine condivisa, usare le linee guida seguenti.

* **Numeri di replica della raccolta immagini condivisa.**  Per ogni pool con fino a 600 istanze, si consiglia di contenere almeno una replica. Se, ad esempio, si sta creando un pool con 3000 di macchine virtuali, è necessario tenere almeno 5 repliche dell'immagine. Per ottenere prestazioni migliori, è sempre consigliabile mantenere più repliche rispetto ai requisiti minimi.

* **Timeout di ridimensionamento.** Se il pool contiene un numero fisso di nodi (se non viene ridimensionato automaticamente), aumentare la proprietà `resizeTimeout` del pool in base alle dimensioni del pool. Per ogni VM 1000, il timeout di ridimensionamento consigliato è di almeno 15 minuti. Ad esempio, il timeout di ridimensionamento consigliato per un pool con 2000 di macchine virtuali è di almeno 30 minuti.

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
