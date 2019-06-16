---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a55653c0f23be594fe65e7a322c11edc37ee1ce6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66145766"
---
## <a name="shared-image-management"></a>Gestione delle immagini condivise 

Di seguito sono riportati alcuni esempi di attività comuni di gestione e della relativa modalità di completamento tramite PowerShell.

Elenca tutte le raccolte in base al nome.

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Elenca tutte le definizioni delle immagini in base al nome.

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Elenca tutte le versioni delle immagini in base al nome.

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Elimina una versione dell'immagine. Questo esempio elimina la versione dell'immagine denominata *1.0.0*.

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





