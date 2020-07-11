---
title: Eseguire la migrazione di un'immagine gestita a una raccolta di immagini condivise
description: Informazioni su come usare Azure PowerShell per eseguire la migrazione di un'immagine gestita a una versione di immagine in una raccolta di immagini condivise.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 7e59ee029b1705f6f789812b870de96bbb74a6e5
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223551"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>Eseguire la migrazione da un'immagine gestita a un'immagine della raccolta immagini condivisa

Se si vuole eseguire la migrazione di un'immagine gestita esistente in una raccolta di immagini condivise, è possibile creare un'immagine della raccolta di immagini condivise direttamente dall'immagine gestita. Dopo aver testato la nuova immagine, è possibile eliminare l'immagine gestita di origine. È anche possibile eseguire la migrazione da un'immagine gestita a una raccolta di immagini condivise usando l'interfaccia della riga di comando di [Azure](image-version-managed-image-cli.md).

Le immagini in una raccolta immagini hanno due componenti, che verrà creato in questo esempio:
- Una **definizione di immagine** contiene informazioni sull'immagine e sui requisiti per l'utilizzo. Questo include la possibilità di specificare se l'immagine è Windows o Linux, le note sulla versione e i requisiti di memoria minimi e massimi. Si tratta della definizione di un tipo di immagine. 
- Una **versione dell'immagine** viene usata per creare una macchina virtuale quando si usa una raccolta di immagini condivise. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Quando si crea una VM, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte.


## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario disporre di un'immagine gestita esistente. Se l'immagine gestita contiene un disco dati, le dimensioni del disco dati non possono superare 1 TB.

Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.

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

Quando si crea la definizione dell'immagine, assicurarsi che disponga di tutte le informazioni corrette. Poiché le immagini gestite sono sempre generalizzate, è necessario impostare `-OsState generalized` . 

Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Per creare la definizione di immagine, usare [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). In questo esempio, la definizione dell'immagine è denominata *myImageDefinition*ed è per un sistema operativo Windows generalizzato. Per creare una definizione per le immagini usando un sistema operativo Linux, usare `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>Ottenere l'immagine gestita

È possibile visualizzare un elenco delle immagini disponibili in un gruppo di risorse usando [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Quando si conosce il nome dell'immagine e in quale gruppo di risorse si trova, è possibile usare nuovamente `Get-AzImage` per ottenere l'oggetto immagine e archiviarlo in una variabile da usare in un secondo momento. Questo esempio mostra come ottenere un'immagine denominata *myImage* dal gruppo di risorse "myResourceGroup" che lo assegna alla variabile *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Creare una versione di immagine

Creare una versione dell'immagine dall'immagine gestita usando [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio la versione dell'immagine è *1.0.0* e viene replicata nei datacenter degli *Stati Uniti centro-occidentali* e degli *Stati Uniti centro-meridionali*. Quando si scelgono le aree di destinazione per la replica, tenere presente che è necessario includere anche l'area di *origine* come destinazione per la replica. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Può essere necessario un po' di tempo per replicare l'immagine in tutte le aree di destinazione, per questo motivo Microsoft ha creato un processo in modo che sia possibile tenere traccia dello stato di avanzamento. Per visualizzare lo stato di avanzamento, digitare `$job.State` .

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine. 
>
> Quando si crea la versione dell'immagine, è anche possibile archiviare l'immagine nell'archiviazione Premium, aggiungendo `-StorageAccountType Premium_LRS`, oppure nell'[archiviazione con ridondanza della zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs), aggiungendo `-StorageAccountType Standard_ZRS`.
>

## <a name="delete-the-managed-image"></a>Eliminare l'immagine gestita

Dopo aver verificato che la nuova versione dell'immagine funziona correttamente, è possibile eliminare l'immagine gestita.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato che la replica è stata completata, è possibile creare una macchina virtuale dall' [immagine generalizzata](vm-generalized-image-version-powershell.md).

Per informazioni su come fornire informazioni sul piano di acquisto, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](marketplace-images.md).