---
title: Creare un disco gestito da una versione dell'immagine
description: Creare un disco gestito da una versione di immagine in una raccolta di immagini condivise.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: bf4a1feb91a1ac4b0bca0d6afdbac41a8be3aa4f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049750"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Creare un disco gestito da una versione dell'immagine

Se necessario, è possibile creare un disco gestito da una versione di immagine archiviata in una raccolta di immagini condivise.


## <a name="cli"></a>CLI

Impostare la `source` variabile sull'ID della versione dell'immagine, quindi usare [AZ disk create](/cli/azure/disk.md#az_disk_create) per creare il disco gestito. 


È possibile visualizzare le versioni delle immagini di un elenco usando [AZ sig Image-Version list](/cli/azure/sig/image-version.md#az_sig_image_version_list). In questo esempio vengono cercate tutte le versioni di immagini che fanno parte della definizione dell'immagine *myImageDefinition* nella raccolta immagini di *raccolta* immagini.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


In questo esempio viene creato un disco gestito denominato *myManagedDisk*, nell'area *eastus* , in un gruppo di risorse denominato *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Se il disco è un disco dati, aggiungere `--gallery-image-reference-lun` per specificare il lun.

## <a name="powershell"></a>PowerShell

È possibile elencare tutte le versioni dell'immagine usando [Get-AzResource](/powershell/module/az.resources/get-azresource). 

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

Configurare alcune variabili per le informazioni sul disco.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Creare una configurazione del disco e quindi il disco usando l'ID versione dell'immagine di origine. Per `-GalleryImageReference` , il Lun è necessario solo se l'origine è un disco dati.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Creare il disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una versione di immagine da un disco gestito usando l'interfaccia della riga di comando di [Azure](image-version-managed-image-cli.md) o [PowerShell](image-version-managed-image-powershell.md).


