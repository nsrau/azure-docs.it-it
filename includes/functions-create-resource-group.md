---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "68444140"
---
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app per le funzioni, database e account di archiviazione.

Nell'esempio seguente viene creato il gruppo di risorse denominato `myResourceGroup`.  
Se non si usa Cloud Shell, prima accedere usando `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

In genere, il gruppo di risorse e le risorse vengono creati in un'[area](https://azure.microsoft.com/global-infrastructure/regions/) vicina alla località dell'utente. 
