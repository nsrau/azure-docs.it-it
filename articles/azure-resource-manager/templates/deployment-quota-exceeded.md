---
title: Quota di distribuzione superata
description: Viene descritto come risolvere l'errore di avere più di 800 distribuzioni nella cronologia del gruppo di risorse.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245090"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Risolvere l'errore quando il numero di distribuzione supera 800

Ogni gruppo di risorse è limitato a 800 distribuzioni nella cronologia di distribuzione. In questo articolo viene descritto l'errore che si riceve quando una distribuzione non riesce perché supererebbe le 800 distribuzioni consentite. Per risolvere questo errore, eliminare le distribuzioni dalla cronologia del gruppo di risorse. L'eliminazione di una distribuzione dalla cronologia non influisce sulle risorse distribuite.

## <a name="symptom"></a>Sintomo

Durante la distribuzione, viene visualizzato un errore che indica che la distribuzione corrente supererà la quota di 800 distribuzioni.

## <a name="solution"></a>Soluzione

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Utilizzare il comando [az deployment group delete](/cli/azure/group/deployment) per eliminare le distribuzioni dalla cronologia.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

To delete all deployments older than five days, use:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

È possibile ottenere il conteggio corrente nella cronologia di distribuzione con il comando seguente:You can get the current count in the deployment history with the following command:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilizzare il comando [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) per eliminare le distribuzioni dalla cronologia.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

To delete all deployments older than five days, use:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

È possibile ottenere il conteggio corrente nella cronologia di distribuzione con il comando seguente:You can get the current count in the deployment history with the following command:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Soluzioni di terze parti

Le soluzioni esterne seguenti affrontano scenari specifici:The following external solutions address specific scenarios:

* [App per la logica di Azure e soluzioni PowerShellAzure Logic Apps and PowerShell solutions](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Estensione AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
