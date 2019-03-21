---
title: 'Esempio: larghezza di banda consentita di ExpressRoute'
description: Questa definizione di criteri di esempio richiede che ExpressRoute usi un set specifico di larghezze di banda.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 87cf3f2528a58e5bd12ffcd4997f979763ab0ef0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537245"
---
# <a name="sample---allowed-expressroute-bandwidth"></a>Esempio: larghezza di banda consentita di ExpressRoute

Questo criterio richiede che le istanze di ExpressRoute usino un set specificato di larghezze di banda. Si specifica una matrice di SKU che possono essere specificati per ExpressRoute.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modello di esempio

[!code-json[main](../../../../policy-templates/samples/Network/express-route-bandwidthInMbps/azurepolicy.json "Allowed ExpressRoute bandwidth")]

È possibile distribuire questo modello usando il [portale di Azure](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con l'[interfaccia della riga di comando di Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Eseguire la distribuzione con il portale

[![Distribuzione in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fexpress-route-bandwidthInMbps%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "express-route-bandwidthInMbps" -DisplayName "Allowed ExpressRoute bandwidth" -description "This policy enables you to specify a set of express-route bandwidths that your organization can deploy." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-bandwidthInMbps/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-bandwidthInMbps/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfBandwidthinMbps <Allowed Bandwidth> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Eliminare la distribuzione di PowerShell

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'express-route-bandwidthInMbps' --display-name 'Allowed ExpressRoute bandwidth' --description 'This policy enables you to specify a set of ExpressRoute bandwidths that your organization can deploy.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-bandwidthInMbps/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-bandwidthInMbps/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "express-route-bandwidthInMbps"
```

### <a name="clean-up-azure-cli-deployment"></a>Eliminare la distribuzione dell'interfaccia della riga di comando di Azure

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere altri esempi in [Esempi di Criteri di Azure](index.md)