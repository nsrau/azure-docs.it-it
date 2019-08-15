---
title: Usare la raccolta di immagini condivise per creare un pool-Azure Batch | Microsoft Docs
description: Creare un pool di batch con la raccolta di immagini condivise per eseguire il provisioning di immagini personalizzate nei nodi di calcolo che contengono il software e i dati necessari per l'applicazione. Le immagini personalizzate sono uno strumento efficace per configurare i nodi di calcolo per l'esecuzione dei carichi di lavoro di Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/14/2019
ms.author: lahugh
ms.openlocfilehash: 00da17512cbc2e713955ea83c7d9fa7517958169
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036700"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>Usare la raccolta di immagini condivise per creare un pool

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

* **Immagine della raccolta di immagini condivise**. Per altre informazioni e procedure per preparare un'immagine condivisa, vedere [creare una raccolta di immagini condivise con l'interfaccia](../virtual-machines/linux/shared-images.md) della riga di comando di Azure o [creare una raccolta di immagini condivise usando il portale di Azure](../virtual-machines/linux/shared-images-portal.md).

> [!NOTE]
> L'immagine condivisa deve trovarsi nella stessa sottoscrizione dell'account batch. L'immagine condivisa può trovarsi in aree diverse, purché includa repliche nella stessa area dell'account batch.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Creare un pool da un'immagine condivisa usando l'interfaccia della riga di comando di Azure

Per creare un pool dall'immagine condivisa usando l'interfaccia della riga di comando di `az batch pool create` Azure, usare il comando. Specificare l'ID immagine condivisa nel `--image` campo. Verificare che il tipo di sistema operativo e lo SKU corrispondano alle versioni specificate da`--node-agent-sku-id`

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

## <a name="considerations-for-large-pools"></a>Considerazioni per i pool di grandi dimensioni

Se si prevede di creare un pool con centinaia o migliaia di VM o più usando un'immagine condivisa, usare le linee guida seguenti.

* **Numeri di replica della raccolta immagini condivisa.**  Per ogni pool con fino a 600 istanze, si consiglia di contenere almeno una replica. Se, ad esempio, si sta creando un pool con 3000 di macchine virtuali, è necessario tenere almeno 5 repliche dell'immagine. Per ottenere prestazioni migliori, è sempre consigliabile mantenere più repliche rispetto ai requisiti minimi.

* **Timeout ridimensionamento** Se il pool contiene un numero fisso di nodi (se non viene ridimensionato automaticamente), aumentare `resizeTimeout` la proprietà del pool in base alle dimensioni del pool. Per ogni VM 1000, il timeout di ridimensionamento consigliato è di almeno 15 minuti. Ad esempio, il timeout di ridimensionamento consigliato per un pool con 2000 di macchine virtuali è di almeno 30 minuti.

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
