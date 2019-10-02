---
title: Quota di distribuzione di Azure superata
description: Viene descritto come risolvere l'errore relativo alla presenza di più di 800 distribuzioni nella cronologia del gruppo di risorse.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/01/2019
ms.author: tomfitz
ms.openlocfilehash: f06aff74e2cf800d44115f34921825122b09a9e7
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719435"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Risolvere l'errore quando il numero di distribuzioni supera 800

Ogni gruppo di risorse è limitato a 800 distribuzioni nella relativa cronologia di distribuzione. Questo articolo descrive l'errore che si riceve quando una distribuzione non riesce perché supererà le distribuzioni 800 consentite. Per correggere l'errore, eliminare le distribuzioni dalla cronologia del gruppo di risorse. L'eliminazione di una distribuzione dalla cronologia non influisce sulle risorse distribuite.

## <a name="symptom"></a>Sintomo

Durante la distribuzione viene visualizzato un errore che informa che la distribuzione corrente supera la quota di 800 distribuzioni.

## <a name="solution"></a>Soluzione

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare il comando [AZ Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete) per eliminare le distribuzioni dalla cronologia.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

È possibile ottenere il conteggio corrente nella cronologia di distribuzione con il comando seguente:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Usare il comando [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) per eliminare le distribuzioni dalla cronologia.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

È possibile ottenere il conteggio corrente nella cronologia di distribuzione con il comando seguente:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

