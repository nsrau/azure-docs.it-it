---
title: Condividere immagini della raccolta tra tenant in AzureShare gallery images across tenants in Azure
description: Informazioni su come condividere immagini di macchine virtuali tra tenant di Azure usando raccolte di immagini condivise.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276346"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Condividere immagini di macchine virtuali della raccolta tra tenant di AzureShare gallery VM images across Azure tenants

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Creare un set di scalabilità con l'interfaccia della riga di comando di Azure

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

Creare il set di scalabilità. Sostituire le informazioni nell'esempio con le proprie.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi, è possibile [risolvere i problemi relativi alle raccolte di immagini condivise](troubleshooting-shared-images.md).
