---
title: Creare un disco gestito da una versione dell'immagine
description: Creare un disco gestito da una versione di immagine in una raccolta di immagini condivise.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 40d3ff736194c4634b949af52ee7b09db20dd06d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026132"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Creare un disco gestito da una versione dell'immagine

Se necessario, è possibile esportare il sistema operativo o un singolo disco dati da una versione di immagine come disco gestito da una versione di immagine archiviata in una raccolta di immagini condivise.


## <a name="cli"></a>CLI

Elencare le versioni delle immagini in una raccolta usando [AZ sig Image-Version list](/cli/azure/sig/image-version#az_sig_image_version_list). In questo esempio vengono cercate tutte le versioni di immagini che fanno parte della definizione dell'immagine *myImageDefinition* nella raccolta immagini di *raccolta* immagini.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Impostare la `source` variabile sull'ID della versione dell'immagine, quindi usare [AZ disk create](/cli/azure/disk?view=azure-cli-latest#az_disk_create) per creare il disco gestito. 

In questo esempio il disco del sistema operativo della versione dell'immagine viene esportato per creare un disco gestito denominato *myManagedOSDisk*, nell'area *eastus* , in un gruppo di risorse denominato *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Se si vuole esportare un disco dati dalla versione dell'immagine, aggiungere `--gallery-image-reference-lun` per specificare il percorso LUN del disco dati da esportare. 

In questo esempio, viene esportato il disco dati che si trova nel LUN 0 della versione dell'immagine per creare un disco gestito denominato *myManagedDataDisk*, nell'area *eastus* , in un gruppo di risorse denominato *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Elencare le versioni delle immagini in una raccolta usando [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

Una volta fornite tutte le informazioni necessarie, è possibile usare [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) per ottenere la versione dell'immagine di origine che si vuole usare e assegnarla a una variabile. In questo esempio viene recuperata la `1.0.0` versione dell'immagine, della `myImageDefinition` definizione, nella raccolta di `myGallery` origine, nel gruppo di `myResourceGroup` risorse.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Dopo aver impostato la `source` variabile sull'ID della versione dell'immagine, usare [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) per creare una configurazione del disco e [New-AzDisk](/powershell/module/az.compute/new-azdisk) per creare il disco. 

In questo esempio il disco del sistema operativo della versione dell'immagine viene esportato per creare un disco gestito denominato *myManagedOSDisk*, nell'area *eastus* , in un gruppo di risorse denominato *myResourceGroup*. 

Creare una configurazione del disco.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Creare il disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Se si vuole esportare un disco dati nella versione dell'immagine, aggiungere un ID LUN alla configurazione del disco per specificare il percorso LUN del disco dati da esportare. 

In questo esempio, viene esportato il disco dati che si trova nel LUN 0 della versione dell'immagine per creare un disco gestito denominato *myManagedDataDisk*, nell'area *eastus* , in un gruppo di risorse denominato *myResourceGroup*. 

Creare una configurazione del disco.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Creare il disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una versione di immagine da un disco gestito usando l'interfaccia della riga di comando di [Azure](image-version-managed-image-cli.md) o [PowerShell](image-version-managed-image-powershell.md).


