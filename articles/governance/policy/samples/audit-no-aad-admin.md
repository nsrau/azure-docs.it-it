---
title: Esempio - Controllare l'assenza di amministratore per Azure Active Directory
description: Questa definizione di criteri di esempio verifica quando non è stato assegnato alcun amministratore di Azure Active Directory al server SQL.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 769d860195df5d681d7426d33d70adf8851eed3a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274213"
---
# <a name="sample---audit-no-azure-active-directory-administrator"></a>Esempio - Verificare l'assenza di un amministratore di Azure Active Directory

Controlla quando non vi è nessun amministratore di Azure Active Directory assegnato a SQL Server.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modello di esempio

[!code-json[main](../../../../policy-templates/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.json "Audit SQL DB Level Audit Setting")]

È possibile distribuire questo modello usando il [portale di Azure](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con l'[interfaccia della riga di comando di Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Eseguire la distribuzione con il portale

[![DDistribuire l'esempio in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FSQL%2Faudit-if-no-sql-active-directory-admin%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "audit-if-no-sql-active-directory-admin" -DisplayName "Audit If no AAD Admin" -description "Aduit If there is no AAD Admin assigned to this server" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
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
az policy definition create --name 'audit-if-no-sql-active-directory-admin' --display-name 'Audit If no AAD Admin' --description 'Aduit If there is no AAD Admin assigned to this server' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-if-no-sql-active-directory-admin" 
```

### <a name="clean-up-azure-cli-deployment"></a>Eliminare la distribuzione dell'interfaccia della riga di comando di Azure

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere altri esempi in [Esempi di Criteri di Azure](index.md)