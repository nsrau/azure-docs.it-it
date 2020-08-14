---
title: Creare un'immagine da una macchina virtuale (anteprima)
description: Informazioni su come usare Azure PowerShell per creare un'immagine in una raccolta di immagini condivise da una macchina virtuale esistente in Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 757b297d3d74365928cda0934485c0018f28ffee
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225649"
---
# <a name="preview-create-an-image-from-a-vm"></a>Anteprima: creare un'immagine da una macchina virtuale

Se è presente una macchina virtuale esistente che si vuole usare per creare più macchine virtuali identiche, è possibile usare tale macchina virtuale per creare un'immagine in una raccolta di immagini condivise usando Azure PowerShell. È anche possibile creare un'immagine da una macchina virtuale usando l'interfaccia della riga di comando di [Azure](image-version-vm-cli.md).

È possibile acquisire un'immagine da macchine virtuali [specializzate e generalizzate](./windows/shared-image-galleries.md#generalized-and-specialized-images) usando Azure PowerShell. 

Le immagini in una raccolta immagini hanno due componenti, che verrà creato in questo esempio:
- Una **definizione di immagine** contiene informazioni sull'immagine e sui requisiti per l'utilizzo. Questo include la possibilità di specificare se l'immagine è Windows o Linux, le note sulla versione e i requisiti di memoria minimi e massimi. Si tratta della definizione di un tipo di immagine. 
- Una **versione dell'immagine** viene usata per creare una macchina virtuale quando si usa una raccolta di immagini condivise. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Quando si crea una VM, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte.


## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario disporre di una raccolta di immagini condivise esistente e di una macchina virtuale esistente in Azure da usare come origine. 

Se la macchina virtuale dispone di un disco dati collegato, le dimensioni del disco dati non possono superare 1 TB.

Quando si lavora in questo articolo, sostituire i nomi delle risorse laddove necessario.


## <a name="get-the-gallery"></a>Ottenere la raccolta

È possibile elencare tutte le raccolte e le definizioni di immagine in base al nome. I risultati sono nel formato `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Dopo aver individuato la raccolta e le definizioni di immagini appropriate, è possibile crearne le variabili da usare in un secondo momento. Questo esempio Mostra come ottenere la *raccolta denominata Gallery* nel gruppo di risorse *myResourceGroup* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Ottenere la macchina virtuale

Per visualizzare un elenco di macchine virtuali disponibili in un gruppo di risorse, usare [Get-AzVM](/powershell/module/az.compute/get-azvm). Quando si conosce il nome della macchina virtuale e il gruppo di risorse in cui si trova, è possibile usare `Get-AzVM` di nuovo per ottenere l'oggetto macchina virtuale e archiviarlo in una variabile da usare in un secondo momento. Questo esempio Mostra come ottenere una macchina virtuale denominata *sourceVM* dal gruppo di risorse "myResourceGroup" e assegnarla alla variabile *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

È consigliabile stop\deallocate la macchina virtuale prima di creare un'immagine con [Stop-AzVM](/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine 

Le definizioni di immagini creano un raggruppamento logico per le immagini. Vengono usati per gestire le informazioni sull'immagine. I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. 

Quando si crea la definizione dell'immagine, assicurarsi che disponga di tutte le informazioni corrette. Se la macchina virtuale è stata generalizzata (usando Sysprep per Windows o waagent-deprovision per Linux), è necessario creare una definizione di immagine usando `-OsState generalized` . Se la macchina virtuale non è stata generalizzata, creare una definizione di immagine usando `-OsState specialized` .

Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](./windows/shared-image-galleries.md#image-definitions).

Per creare la definizione di immagine, usare [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

In questo esempio, la definizione dell'immagine è denominata *myImageDefinition*ed è destinata a una VM specializzata che esegue Windows. Per creare una definizione per le immagini con Linux, usare `-OsType Linux` . 

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


## <a name="create-an-image-version"></a>Creare una versione di immagine

Creare una versione dell'immagine usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio la versione dell'immagine è *1.0.0* e viene replicata nei datacenter degli *Stati Uniti centro-occidentali* e degli *Stati Uniti centro-meridionali*. Quando si scelgono le aree di destinazione per la replica, tenere presente che è necessario includere anche l'area di *origine* come destinazione per la replica.

Per creare una versione di immagine dalla macchina virtuale, usare `$vm.Id.ToString()` per `-Source`.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Può essere necessario un po' di tempo per replicare l'immagine in tutte le aree di destinazione, per questo motivo Microsoft ha creato un processo in modo che sia possibile tenere traccia dello stato di avanzamento. Per visualizzare lo stato di avanzamento del processo, digitare `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine.
>
> Per archiviare l'immagine nell'archiviazione Premium, è anche possibile aggiungere `-StorageAccountType Premium_LRS` o l' [archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md) aggiungendo `-StorageAccountType Standard_ZRS` quando si crea la versione dell'immagine.
>

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato che la nuova versione dell'immagine funziona correttamente, è possibile creare una macchina virtuale. Creare una macchina virtuale da una [versione di immagine specializzata](vm-specialized-image-version-powershell.md) o da una [versione di immagine generalizzata](vm-generalized-image-version-powershell.md).

Per informazioni su come fornire informazioni sul piano di acquisto, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](marketplace-images.md).
