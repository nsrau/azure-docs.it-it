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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112316"
---
### <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

Creare un'identità *denominata myACRTasksId* nella sottoscrizione usando il comando [az identity create.][az-identity-create] È possibile usare lo stesso gruppo di risorse usato in precedenza per creare un registro contenitori o un altro.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Per configurare l'identità assegnata dall'utente nei passaggi seguenti, usare il comando [az identity show][az-identity-show] per archiviare l'ID risorsa, l'ID entità e l'ID client dell'identità nelle variabili.

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