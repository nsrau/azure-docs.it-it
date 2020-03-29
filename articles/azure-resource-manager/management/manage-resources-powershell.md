---
title: Gestire le risorse - Azure PowerShellManage resources - Azure PowerShell
description: Usare Azure PowerShell e Azure Resource Manager per gestire le risorse. Viene illustrato come distribuire ed eliminare risorse.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: de6f24ea66ef41b5ee4bfdda5948de9639f10a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485403"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Gestire le risorse di Azure tramite Azure PowerShell

Informazioni su come usare Azure PowerShell con Azure Resource Manager per gestire le risorse di Azure.Learn how to use Azure PowerShell with [Azure Resource Manager](overview.md) to manage your Azure resources. Per la gestione dei gruppi di risorse, vedere Gestire i gruppi di risorse di Azure tramite Azure PowerShell.For managing resource groups, see [Manage Azure resource groups by using Azure PowerShell.](manage-resource-groups-powershell.md)

Altri articoli sulla gestione delle risorse:

- [Gestire le risorse di Azure tramite il portale di AzureManage Azure resources by using the Azure portal](manage-resources-portal.md)
- [Gestire le risorse di Azure usando l'interfaccia della riga di comando di AzureManage Azure resources by using Azure CLI](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire risorse in un gruppo di risorse esistenteDeploy resources to an existing resource group

È possibile distribuire le risorse di Azure direttamente tramite Azure PowerShell o distribuire un modello di Resource Manager per creare risorse di Azure.You can deploy Azure resources directly by using Azure PowerShell, or deploy a Resource Manager template to create Azure resources.

### <a name="deploy-a-resource"></a>Distribuire una risorsaDeploy a resource

Lo script seguente crea un account di archiviazione.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Distribuire un modello

Lo script seguente crea distribuire un modello di guida rapida per creare un account di archiviazione. Per altre informazioni, vedere [Guida introduttiva: Creare modelli](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)di Azure Resource Manager usando Visual Studio Code .

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorseDeploy a resource group and resources

È possibile creare un gruppo di risorse e distribuire le risorse nel gruppo. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuire risorse a più sottoscrizioni o gruppi di risorseDeploy resources to multiple subscriptions or resource groups

In genere si distribuiscono tutte le risorse del modello in un unico gruppo di risorse, ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di sottoscrizioni e risorse diversi. Per altre informazioni, vedere Distribuire risorse di Azure in più sottoscrizioni o gruppi di [risorse.](../templates/cross-resource-group-deployment.md)

## <a name="delete-resources"></a>Eliminare le risorse

Lo script seguente mostra come eliminare un account di archiviazione.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Per altre informazioni su come Azure Resource Manager ordina l'eliminazione delle risorse, vedere Eliminazione del gruppo di risorse di Azure Resource Manager.For more information about how Azure Resource Manager orders the deletion of resources, see [Azure Resource Manager resource group deletion.](delete-resource-group.md)

## <a name="move-resources"></a>Spostare le risorse

Lo script seguente mostra come rimuovere un account di archiviazione da un gruppo di risorse a un altro gruppo di risorse.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloccare le risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente risorse critiche, ad esempio una sottoscrizione di Azure, un gruppo di risorse o una risorsa. 

Lo script seguente blocca un account di archiviazione in modo che non possa essere eliminato.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Lo script seguente ottiene tutti i blocchi per un account di archiviazione:The following script gets all locks for a storage account:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Lo script seguente elimina un blocco di un account di archiviazione:The following script deletes a lock of a storage account:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Per altre informazioni, vedere [Bloccare le risorse con Azure Resource Manager.For](lock-resources.md)more information, see Lock resources with Azure Resource Manager.

## <a name="tag-resources"></a>Aggiungere tag alle risorse

L'assegnazione di tag consente di organizzare il gruppo di risorse e le risorse in modo logico. Per informazioni, vedere Uso dei tag per organizzare le risorse di Azure.For information, see [Using tags to organize your Azure resources](tag-resources.md#powershell).

## <a name="manage-access-to-resources"></a>Gestire l'accesso alle risorse

Il controllo degli accessi in base al ruolo è il modo in cui si gestisce l'accesso alle risorse in [Azure.Role-based access control (RBAC)](../../role-based-access-control/overview.md) is the way that you manage access to resources in Azure. Per altre informazioni, vedere [Gestire l'accesso tramite RBAC e Azure PowerShell.For](../../role-based-access-control/role-assignments-powershell.md)more information, see Manage access using RBAC and Azure PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- Per apprendere Azure Resource Manager, vedere Panoramica di Azure Resource Manager.To learn Azure [Resource Manager,](overview.md)see Azure Resource Manager overview .
- Per informazioni sulla sintassi dei modelli di Resource Manager, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](../templates/template-syntax.md)
- Per informazioni su come sviluppare modelli, consultate le [esercitazioni dettagliate.](/azure/azure-resource-manager/)
- Per visualizzare gli schemi di modello di Azure Resource Manager, vedere [riferimento al modello](/azure/templates/).
