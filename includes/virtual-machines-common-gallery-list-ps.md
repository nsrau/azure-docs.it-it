---
title: includere il file
description: Includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: fc60d1266f4aad4c337611f0fa32fe8293744559
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792276"
---
## <a name="list-information"></a>Informazioni di un elenco

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





