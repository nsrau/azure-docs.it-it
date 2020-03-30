---
title: Condividere immagini della raccolta tra tenant in AzureShare gallery images across tenants in Azure
description: Informazioni su come condividere immagini di macchine virtuali tra tenant di Azure usando raccolte di immagini condivise.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034979"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Condividere immagini di macchine virtuali della raccolta tra tenant di AzureShare gallery VM images across Azure tenants

Le raccolte di immagini condivise consentono di condividere immagini utilizzando il controllo degli accessi in base al ruolo. È possibile usare il controllo degli accessi in base al ruolo per condividere immagini all'interno del tenant e anche a utenti esterni al tenant. Per ulteriori informazioni su questa semplice opzione di condivisione, vedere [la raccolta Condividi la raccolta](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Non è possibile usare il portale per distribuire una macchina virtuale da un'immagine in un altro tenant azure.You cannot use the portal to deploy a VM from an image in another azure tenant. Per creare una macchina virtuale da un'immagine condivisa tra tenant, è necessario usare l'interfaccia della riga di comando di Azure o [Powershell.](../windows/share-images-across-tenants.md)

## <a name="create-a-vm-using-azure-cli"></a>Creare una macchina virtuale usando l'interfaccia della riga di comando di AzureCreate a VM using Azure CLI

Accedere all'entità servizio per il tenant 1 usando l'appID, la chiave dell'app e l'ID del tenant 1. Se necessario, è possibile usare `az account show --query "tenantId"` per ottenere gli ID tenant.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Accedere all'entità servizio per il tenant 2 usando l'appID, la chiave dell'app e l'ID del tenant 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Creare la macchina virtuale Sostituire le informazioni nell'esempio con le proprie.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi, è possibile [risolvere i problemi relativi alle raccolte di immagini condivise](troubleshooting-shared-images.md).