---
title: Condividere le immagini della raccolta da un tenant in Azure | Microsoft Docs
description: Informazioni su come condividere le immagini di VM tra i tenant di Azure usando raccolte di immagini condivise.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: c26abe948fa415c780d543c615c34af2091cfbc7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709169"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Condividere le immagini di macchina virtuale di raccolta tra i tenant di Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> È possibile usare il portale per distribuire una macchina virtuale da un'immagine in un altro tenant di azure. Per creare una macchina virtuale da un'immagine condivise tra i tenant, è necessario usare il [CLI Azure](../linux/share-images-across-tenants.md) o Powershell.

## <a name="create-a-vm-using-powershell"></a>Creare una VM usando PowerShell


Log in entrambi i tenant usando l'ID dell'applicazione, ID tenant e del segreto. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Creare la macchina virtuale nel gruppo di risorse che dispone dell'autorizzazione per la registrazione dell'app. Sostituire le informazioni contenute in questo esempio con quelli personalizzati.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare immagini condivise le risorse della raccolta usando il [portale di Azure](shared-images-portal.md).