---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667862"
---
## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Pulire le risorse tramite il portale di AzureClean up resources using the Azure portal

Eliminare le risorse nel portale di Azure seguendo i passaggi descritti in Pulire le [risorse.](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)

### <a name="clean-up-resources-using-powershell"></a>Pulire le risorse usando PowerShell

Se Cloud Shell è ancora aperto, non è necessario copiare/eseguire la prima riga (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```