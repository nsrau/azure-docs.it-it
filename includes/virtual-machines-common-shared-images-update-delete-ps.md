---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 32978257c3e209dc78a29c6e8ae0d1c4ae016a5b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559202"
---
## <a name="update-resources"></a>Aggiornare le risorse

Esistono alcune limitazioni sugli elementi che è possibile aggiornare. È possibile aggiornare gli elementi seguenti: 

Raccolta di immagini condivise:
- Descrizione

Definizione delle immagini:
- VCPU consigliati
- Memoria consigliata
- Descrizione
- Data di scadenza

Versione immagine:
- Conteggio di repliche a livello di area
- Aree di destinazione
- Esclusione dalla versione più recente
- Data di scadenza

Se si prevede di aggiungere aree di replica, non eliminare l'immagine gestita di origine. L'immagine gestita di origine è necessaria per la replica della versione dell'immagine in altre aree. 

Per aggiornare la descrizione di una raccolta, usare [Update-AzGallery](/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Questo esempio illustra come usare [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) per aggiornare la data di fine della vita per la definizione di immagine.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Questo esempio illustra come usare [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) per escludere la versione dell'immagine da usare come immagine *più recente* .

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

Questo esempio illustra come usare [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) per includere questa versione dell'immagine in da considerare per l'immagine *più recente* .

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si eliminano risorse, è necessario iniziare con l'ultimo elemento nelle risorse nidificate, ovvero la versione dell'immagine. Una volta eliminate le versioni, è possibile eliminare la definizione dell'immagine. Non è possibile eliminare la raccolta fino a quando non sono state eliminate tutte le risorse sottostanti.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```