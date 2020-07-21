---
title: PowerShell-creare un'immagine da uno snapshot o un disco rigido virtuale in una raccolta di immagini condivise
description: Informazioni su come creare un'immagine da uno snapshot o un disco rigido virtuale in una raccolta di immagini condivise usando PowerShell.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 315c635ba0864dc1565fd7ba5ccc450223d87ac9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494718"
---
# <a name="create-an-image-from-a-vhd-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Creare un'immagine da un disco rigido virtuale o da uno snapshot in una raccolta di immagini condivise usando PowerShell

Se si ha uno snapshot o un disco rigido virtuale esistente di cui si vuole eseguire la migrazione in una raccolta di immagini condivise, è possibile creare un'immagine della raccolta di immagini condivise direttamente dal disco rigido virtuale o dallo snapshot. Dopo aver testato la nuova immagine, è possibile eliminare il disco rigido virtuale o lo snapshot di origine. È anche possibile creare un'immagine da un disco rigido virtuale o da uno snapshot in una raccolta di immagini condivise usando l'interfaccia della riga di comando di [Azure](image-version-snapshot-cli.md).

Le immagini in una raccolta immagini hanno due componenti, che verrà creato in questo esempio:
- Una **definizione di immagine** contiene informazioni sull'immagine e sui requisiti per l'utilizzo. Questo include la possibilità di specificare se l'immagine è Windows o Linux, le note sulla versione e i requisiti di memoria minimi e massimi. Si tratta della definizione di un tipo di immagine. 
- Una **versione dell'immagine** viene usata per creare una macchina virtuale quando si usa una raccolta di immagini condivise. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Quando si crea una VM, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte.


## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario avere uno snapshot o un disco rigido virtuale. 

Se si desidera includere un disco dati, le dimensioni del disco dati non possono superare 1 TB.

Quando si lavora in questo articolo, sostituire i nomi delle risorse laddove necessario.


## <a name="get-the-snapshot-or-vhd"></a>Ottenere lo snapshot o il disco rigido virtuale

È possibile visualizzare un elenco di snapshot disponibili in un gruppo di risorse usando [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot). 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Quando si conosce il nome dello snapshot e il gruppo di risorse in cui si trova, è possibile usare `Get-AzSnapshot` di nuovo per ottenere l'oggetto snapshot e archiviarlo in una variabile da usare in un secondo momento. Questo esempio Mostra come ottenere uno snapshot denominato " *snapshot* " dal gruppo di risorse "myResourceGroup" e assegnarlo alla variabile *$source*. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

È anche possibile usare un VHD anziché uno snapshot. Per ottenere un disco rigido virtuale, usare [Get-AzDisk](/powershell/module/az.compute/get-azdisk). 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Ottenere quindi il disco rigido virtuale e assegnarlo alla `$source` variabile.

```azurepowershell-interactive
$source = Get-AzDisk `
   -SnapshotName mySnapshot
   -ResourceGroupName myResourceGroup
```

È possibile usare gli stessi cmdlet per ottenere tutti i dischi dati che si desidera includere nell'immagine. Assegnarli alle variabili, quindi usare tali variabili in un secondo momento quando si crea la versione dell'immagine.


## <a name="get-the-gallery"></a>Ottenere la raccolta

È possibile elencare tutte le raccolte e le definizioni di immagine in base al nome. I risultati sono nel formato `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Una volta trovata la raccolta corretta, creare una variabile da usare in un secondo momento. Questo esempio Mostra come ottenere la *raccolta denominata Gallery* nel gruppo di risorse *myResourceGroup* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine 

Le definizioni di immagini creano un raggruppamento logico per le immagini. Vengono usati per gestire le informazioni sull'immagine. I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. 

Quando si crea la definizione dell'immagine, assicurarsi che disponga di tutte le informazioni corrette. In questo esempio si presuppone che lo snapshot o il disco rigido virtuale provenga da una macchina virtuale in uso e che non sia stato generalizzato. Se il disco rigido virtuale o lo snapshot è stato ricavato da un sistema operativo generalizzato (dopo l'esecuzione di Sysprep per Windows o [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` o `-deprovision+user` per Linux), modificare `-OsState` in `generalized` . 

Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](./windows/shared-image-galleries.md#image-definitions).

Per creare la definizione di immagine, usare [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). In questo esempio, la definizione dell'immagine è denominata *myImageDefinition*ed è per un sistema operativo Windows specializzato. Per creare una definizione per le immagini usando un sistema operativo Linux, usare `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

### <a name="purchase-plan-information"></a>Informazioni sui piani di acquisto

In alcuni casi, è necessario passare le informazioni sul piano di acquisto in durante la creazione di una macchina virtuale da un'immagine basata su un'immagine di Azure Marketplace. In questi casi, è consigliabile includere le informazioni sul piano di acquisto nella definizione dell'immagine. In questo caso, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](marketplace-images.md).


## <a name="create-an-image-version"></a>Creare una versione di immagine

Creare una versione dell'immagine dallo snapshot usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Se si vuole che l'immagine contenga un disco dati, oltre al disco del sistema operativo, aggiungere il `-DataDiskImage` parametro e impostarlo sull'ID dello snapshot del disco dati o del disco rigido virtuale.

In questo esempio la versione dell'immagine è *1.0.0* e viene replicata nei datacenter degli *Stati Uniti centro-occidentali* e degli *Stati Uniti centro-meridionali*. Quando si scelgono le aree di destinazione per la replica, tenere presente che è necessario includere anche l'area di *origine* come destinazione per la replica.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Può essere necessario un po' di tempo per replicare l'immagine in tutte le aree di destinazione, per questo motivo Microsoft ha creato un processo in modo che sia possibile tenere traccia dello stato di avanzamento. Per visualizzare lo stato di avanzamento del processo, digitare `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> È necessario attendere che la versione dell'immagine completi la compilazione e la replica prima di poter usare lo stesso snapshot per creare un'altra versione dell'immagine. 
>
> È anche possibile archiviare la versione dell'immagine nell' [archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md) aggiungendo `-StorageAccountType Standard_ZRS` quando si crea la versione dell'immagine.
>

## <a name="delete-the-source"></a>Eliminare l'origine

Dopo aver verificato che la nuova versione dell'immagine funziona correttamente, è possibile eliminare l'origine per l'immagine con [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) o [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato che la replica è stata completata, è possibile creare una macchina virtuale dall' [immagine specializzata](vm-specialized-image-version-powershell.md).

Per informazioni su come fornire informazioni sul piano di acquisto, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](marketplace-images.md).
