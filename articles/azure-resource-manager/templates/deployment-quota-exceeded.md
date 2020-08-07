---
title: Quota di distribuzione superata
description: Viene descritto come risolvere l'errore relativo alla presenza di più di 800 distribuzioni nella cronologia del gruppo di risorse.
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 8996d7817eea2f8daf44fbc9b4416c884b05940f
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987053"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Risolvere l'errore quando il numero di distribuzioni supera 800

Ogni gruppo di risorse è limitato a 800 distribuzioni nella relativa cronologia di distribuzione. Questo articolo descrive l'errore che si riceve quando una distribuzione non riesce perché supererà le distribuzioni 800 consentite. Per correggere l'errore, eliminare le distribuzioni dalla cronologia del gruppo di risorse. L'eliminazione di una distribuzione dalla cronologia non influisce sulle risorse distribuite.

Azure Resource Manager Elimina automaticamente le distribuzioni dalla cronologia nel modo più vicino al limite. Questo errore può essere comunque visualizzato per uno dei motivi seguenti:

1. Si dispone di un blocco CanNotDelete nel gruppo di risorse che impedisce le eliminazioni dalla cronologia di distribuzione.
1. Si è scelto di eliminare le eliminazioni automatiche.
1. Si dispone di un numero elevato di distribuzioni in esecuzione simultaneamente e le eliminazioni automatiche non vengono elaborate abbastanza rapidamente per ridurre il numero totale.

Per informazioni sulla rimozione del blocco o sulla scelta delle eliminazioni automatiche, vedere [eliminazioni automatiche dalla cronologia di distribuzione](deployment-history-deletions.md).

Questo articolo descrive come eliminare manualmente le distribuzioni dalla cronologia.

## <a name="symptom"></a>Sintomo

Durante la distribuzione viene visualizzato un errore che informa che la distribuzione corrente supera la quota di 800 distribuzioni.

## <a name="solution"></a>Soluzione

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare il comando [AZ Deployment Group Delete](/cli/azure/group/deployment) per eliminare le distribuzioni dalla cronologia.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Per eliminare tutte le distribuzioni precedenti a cinque giorni, usare:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

È possibile ottenere il conteggio corrente nella cronologia di distribuzione con il comando seguente:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Usare il comando [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) per eliminare le distribuzioni dalla cronologia.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Per eliminare tutte le distribuzioni precedenti a cinque giorni, usare:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

È possibile ottenere il conteggio corrente nella cronologia di distribuzione con il comando seguente:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Soluzioni di terze parti

Le soluzioni esterne seguenti risolvono scenari specifici:

* [App per la logica di Azure e soluzioni PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Estensione AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
