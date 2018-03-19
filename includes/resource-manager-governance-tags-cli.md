---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b9484336add0719749e9f0af56bdd70fa3906ef5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
Per aggiungere due tag a un gruppo di risorse, usare il comando [az group update](/cli/azure/group#az_group_update):

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Si supponga di voler aggiungere un terzo tag. Eseguire di nuovo il comando con il nuovo tag. Il tag viene aggiunto ai tag esistenti.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Le risorse non ereditano i tag dal gruppo di risorse. Attualmente, il gruppo di risorse dispone di tre tag, ma le risorse non hanno alcun tag. Per applicare tutti i tag da un gruppo di risorse alle risorse e mantenere i tag esistenti nelle risorse, usare lo script seguente:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

In alternativa, è possibile applicare i tag dal gruppo di risorse alle risorse senza mantenere i tag esistenti:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Per combinare diversi valori in un singolo tag, usare una stringa JSON.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Per rimuovere tutti i tag in un gruppo di risorse, usare:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
