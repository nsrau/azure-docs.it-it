---
title: Condividere le immagini della raccolta da un tenant in Azure | Microsoft Docs
description: Informazioni su come condividere le immagini di VM tra i tenant di Azure usando raccolte di immagini condivise.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbf42de406ecb0caed67b77743f376284ab64608
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706562"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Condividere le immagini di macchina virtuale di raccolta tra i tenant di Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> È possibile usare il portale per distribuire una macchina virtuale da un'immagine in un altro tenant di azure. Per creare una macchina virtuale da un'immagine condivise tra i tenant, è necessario usare il comando di Azure oppure [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Creare una VM usando Azure CLI

Accedere all'entità servizio per tenant 1 usando l'appID, la chiave dell'app e l'ID del tenant 1. È possibile usare `az account show --query "tenantId"` per ottenere gli ID tenant se necessario.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Accedere all'entità servizio per tenant 2 usando l'appID, la chiave dell'app e l'ID del tenant 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Creare la macchina virtuale Sostituire le informazioni nell'esempio con quelli personalizzati.

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