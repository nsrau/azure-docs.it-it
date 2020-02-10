---
title: File di inclusione
description: File di inclusione
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112316"
---
### <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

Creare un'identità denominata *myACRTasksId* nella sottoscrizione usando il comando [AZ Identity create][az-identity-create] . È possibile usare lo stesso gruppo di risorse usato in precedenza per creare un registro contenitori o uno diverso.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Per configurare l'identità assegnata dall'utente nei passaggi seguenti, usare il comando [AZ Identity Show][az-identity-show] per archiviare l'ID risorsa dell'identità, l'ID entità e l'ID client nelle variabili.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show