---
title: includere file
description: includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 10c2b447a3f174afe93f56084827756d24d982cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82792191"
---
## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini. I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

Creare una raccolta di immagini usando [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). L'esempio seguente crea una raccolta denominata *myGallery* nel gruppo di risorse *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   

## <a name="share-the-gallery"></a>Condividere la raccolta

È consigliabile condividere l'accesso a livello di raccolta immagini. Usare un indirizzo di posta elettronica e il cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) per ottenere l'ID oggetto per l'utente e quindi usare [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) per concedere l'accesso alla raccolta. Sostituire l'indirizzo di posta elettronica di esempio (in questo esempio alinne_montes@contoso.com) con quello personalizzato.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

