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
ms.openlocfilehash: 94adac6ba232f8931d00083432c027ddccb2ce64
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842496"
---
Nell'output del comando, la sezione `identity` Mostra un'identità di tipo `SystemAssigned` è impostata nell'attività. Il `principalId` è l'ID dell'entità servizio dell'identità:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
Usare il comando [AZ ACR task show][az-acr-task-show] per archiviare il PrincipalId in una variabile, da usare nei comandi successivi. Sostituire il nome dell'attività e il registro di sistema con il comando seguente:

```azurecli
principalID=$(az acr task show --name mytask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
