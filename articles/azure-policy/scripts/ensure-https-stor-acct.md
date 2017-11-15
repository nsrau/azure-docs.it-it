---
title: 'Esempio JSON di Criteri di Azure: assicurare solo traffico HTTPS agli account di archiviazione | Microsoft Docs'
description: Questo criterio di esempio JSON richiede che gli account di archiviazione usino traffico HTTPS.
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 6f96cae1d429662cb3fb9c24b8701be0fb6bb774
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="ensure-https-traffic-only-for-storage-account"></a>Assicurare solo traffico HTTPS agli account di archiviazione

Questo criterio richiede che gli account di archiviazione usino traffico HTTPS.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modello di esempio

[!code-json[main](../../../policy-templates/samples/Storage/https-traffic-only/azurepolicy.json "Ensure https traffic only for storage account")]


È possibile distribuire questo modello usando il [portale di Azure](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con l'[interfaccia della riga di comando di Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Eseguire la distribuzione con il portale

[![Distribuzione in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade)

## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]


````powershell
$definition = New-AzureRmPolicyDefinition -Name "https-traffic-only" -DisplayName "Ensure https traffic only for storage account" -description "Ensure https traffic only for storage account" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
$assignment
````

### <a name="clean-up-powershell-deployment"></a>Eliminare la distribuzione di PowerShell

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]



````cli

az policy definition create --name 'https-traffic-only' --display-name 'Ensure https traffic only for storage account' --description 'Ensure https traffic only for storage account' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "https-traffic-only"

````

### <a name="clean-up-azure-cli-deployment"></a>Eliminare la distribuzione dell'interfaccia della riga di comando di Azure

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

- I modelli di Criteri di Azure di esempio aggiuntivi sono disponibili nella pagina [Templates for Azure Policy](../json-samples.md) (Modelli per Criteri di Azure).
