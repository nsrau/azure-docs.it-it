---
title: Condividere immagini della raccolta tra i tenant in Azure
description: Informazioni su come condividere immagini di VM tra tenant di Azure usando le raccolte di immagini condivise.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5b86335ab8bcc3af75dbd8af39e3d913f2461e58
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119842"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Condividere le immagini di macchine virtuali della raccolta tra i tenant di Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Creare un set di scalabilità con l'interfaccia della riga di comando di Azure

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
