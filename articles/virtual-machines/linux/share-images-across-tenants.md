---
title: Condividere immagini della raccolta tra i tenant
description: Informazioni su come condividere immagini di VM tra tenant di Azure usando raccolte di immagini condivise con esempi di Linux.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5e71f09179d414be84896a49a66480f19a0665d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843588"
---
# <a name="share-gallery-vm-images-across-azure-tenants---linux-examples"></a>Condividere immagini di macchine virtuali della raccolta tra i tenant di Azure-esempi di Linux

Le raccolte immagini condivise consentono di condividere immagini con il controllo degli accessi in base al ruolo È possibile usare il controllo degli accessi in base al ruolo di Azure per condividere immagini all'interno del tenant e anche a utenti esterni al tenant. Per ulteriori informazioni su questa semplice opzione di condivisione, vedere la pagina relativa alla [condivisione della raccolta](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Non è possibile usare il portale per distribuire una macchina virtuale da un'immagine in un altro tenant di Azure. Per creare una macchina virtuale da un'immagine condivisa tra i tenant, è necessario usare l'interfaccia della riga di comando di Azure o [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Creare una VM usando l'interfaccia della riga di comando di Azure

Accedere all'entità servizio per il tenant 1 usando appID, la chiave dell'app e l'ID del tenant 1. `az account show --query "tenantId"`Se necessario, è possibile usare per ottenere gli ID tenant.

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

Se si verificano problemi, è possibile [risolvere i problemi relativi alle raccolte di immagini condivise](../troubleshooting-shared-images.md).
