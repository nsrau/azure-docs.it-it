---
title: Gestire le risorse di Azure usando Azure PowerShell | Microsoft Docs
description: Usare Azure PowerShell e Azure Resource Manager per gestire le risorse. Viene illustrato come distribuire ed eliminare risorse.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: aa215555fdd39e9d756d5fc925282b1eaffc9ebf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390357"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Gestire le risorse di Azure usando Azure PowerShell

Informazioni su come usare Azure PowerShell con [Azure Resource Manager](resource-group-overview.md) per gestire le risorse di Azure. Per la gestione dei gruppi di risorse, vedere [gestire i gruppi di risorse di Azure usando Azure PowerShell](./manage-resource-groups-powershell.md).

Altri articoli sulla gestione delle risorse:

- [Gestire le risorse di Azure usando il portale di Azure](./manage-resources-portal.md)
- [Gestire le risorse di Azure usando l'interfaccia della riga di comando](./manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire le risorse in un gruppo di risorse esistente

È possibile distribuire le risorse di Azure direttamente usando Azure PowerShell o distribuire un modello di Gestione risorse per creare le risorse di Azure.

### <a name="deploy-a-resource"></a>Distribuire una risorsa

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

Lo script seguente crea la distribuzione di un modello di avvio rapido per creare un account di archiviazione. Per altre informazioni, vedere [Guida introduttiva: creare modelli di Azure Resource Manager usando Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](./resource-group-template-deploy.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorse

È possibile creare un gruppo di risorse e distribuire le risorse al gruppo. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuire le risorse a più sottoscrizioni o gruppi di risorse

In genere si distribuiscono tutte le risorse del modello in un unico gruppo di risorse, ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di sottoscrizioni e risorse diversi. Per altre informazioni, vedere [distribuire risorse di Azure a più sottoscrizioni o gruppi di risorse](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Eliminare le risorse

Lo script seguente mostra come eliminare un account di archiviazione.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Per altre informazioni su come Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [Azure Resource Manager eliminazione del gruppo di risorse](./resource-group-delete.md).

## <a name="move-resources"></a>Spostare le risorse

Lo script seguente illustra come rimuovere un account di archiviazione da un gruppo di risorse a un altro gruppo di risorse.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloccare le risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche, ad esempio la sottoscrizione di Azure, il gruppo di risorse o la risorsa. 

Lo script seguente blocca un account di archiviazione in modo che l'account non possa essere eliminato.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Lo script seguente ottiene tutti i blocchi per un account di archiviazione:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Lo script seguente elimina un blocco di un account di archiviazione:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Aggiungere tag alle risorse

L'assegnazione di tag consente di organizzare logicamente il gruppo di risorse e le risorse. Per informazioni, vedere [uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md#powershell).

## <a name="manage-access-to-resources"></a>Gestire l'accesso alle risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [gestire l'accesso con RBAC e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).
