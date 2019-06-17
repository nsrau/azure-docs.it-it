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
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66145777"
---
## <a name="update-resources"></a>Aggiornare le risorse

Esistono alcune limitazioni su ciò che può essere aggiornato. Gli elementi seguenti possono essere aggiornati: 

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

Se si prevede di aggiunta di aree di replica, non eliminare l'immagine gestita di origine. L'immagine gestita di origine è necessaria per la replica versione immagine in aree aggiuntive. 

Per aggiornare la descrizione di una raccolta, usare [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

In questo esempio viene illustrato come utilizzare [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) per aggiornare la data di fine del ciclo di vita per la definizione dell'immagine.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

In questo esempio viene illustrato come utilizzare [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) escludere questa versione dell'immagine venga utilizzato come il *più recente* immagine.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Pulire le risorse

Quando si elimina le risorse, è necessario iniziare con l'ultimo elemento nelle risorse annidate - la versione dell'immagine. Quando vengono eliminate le versioni, è possibile eliminare la definizione dell'immagine. È possibile eliminare la raccolta fino a quando non sono state eliminate tutte le risorse sottostanti.

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

