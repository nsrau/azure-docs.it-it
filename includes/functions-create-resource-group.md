---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c20f86fe7fdcfc7ecc940923a8c98fa1fbf4cf65
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246970"
---
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app per le funzioni, database e account di archiviazione.

Nell'esempio seguente viene creato il gruppo di risorse denominato `myResourceGroup`.  
Se non si usa Cloud Shell, prima accedere usando `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
In genere, il gruppo di risorse e le risorse vengono create in un'area vicina alla località dell'utente. Per visualizzare tutte le località supportate per i piani di Servizio app, eseguire il comando [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).
