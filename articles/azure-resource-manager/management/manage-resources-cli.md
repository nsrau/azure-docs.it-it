---
title: Gestire le risorse - Interfaccia della riga di comando di AzureManage resources - Azure CLI
description: Usare l'interfaccia della riga di comando di Azure e Azure Resource Manager per gestire le risorse. Viene illustrato come distribuire ed eliminare risorse.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: f00c1725201b0e49f80fec64e5d69b375ec7a233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485533"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Gestire le risorse di Azure usando l'interfaccia della riga di comando di AzureManage Azure resources by using Azure CLI

Informazioni su come usare l'interfaccia della riga di comando di Azure con Azure Resource Manager per gestire le risorse di Azure.Learn how to use Azure CLI with [Azure Resource Manager](overview.md) to manage your Azure resources. Per la gestione dei gruppi di risorse, vedere Gestire i gruppi di risorse di Azure usando l'interfaccia della riga di comando di Azure.For managing resource groups, see [Manage Azure resource groups by using Azure CLI.](manage-resource-groups-cli.md)

Altri articoli sulla gestione delle risorse:

- [Gestire le risorse di Azure tramite il portale di AzureManage Azure resources by using the Azure portal](manage-resources-portal.md)
- [Gestire le risorse di Azure tramite Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire risorse in un gruppo di risorse esistenteDeploy resources to an existing resource group

È possibile distribuire le risorse di Azure direttamente usando l'interfaccia della riga di comando di Azure o distribuire un modello di Resource Manager per creare risorse di Azure.You can deploy Azure resources directly by using Azure CLI, or deploy a Resource Manager template to create Azure resources.

### <a name="deploy-a-resource"></a>Distribuire una risorsaDeploy a resource

Lo script seguente crea un account di archiviazione.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Distribuire un modello

Lo script seguente crea distribuire un modello di guida rapida per creare un account di archiviazione. Per altre informazioni, vedere [Guida introduttiva: Creare modelli](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)di Azure Resource Manager usando Visual Studio Code .

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorseDeploy a resource group and resources

È possibile creare un gruppo di risorse e distribuire le risorse nel gruppo. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuire risorse a più sottoscrizioni o gruppi di risorseDeploy resources to multiple subscriptions or resource groups

In genere si distribuiscono tutte le risorse del modello in un unico gruppo di risorse, ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di sottoscrizioni e risorse diversi. Per altre informazioni, vedere Distribuire risorse di Azure in più sottoscrizioni o gruppi di [risorse.](../templates/cross-resource-group-deployment.md)

## <a name="delete-resources"></a>Eliminare le risorse

Lo script seguente mostra come eliminare un account di archiviazione.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Per altre informazioni su come Azure Resource Manager ordina l'eliminazione delle risorse, vedere Eliminazione del gruppo di risorse di Azure Resource Manager.For more information about how Azure Resource Manager orders the deletion of resources, see [Azure Resource Manager resource group deletion.](delete-resource-group.md)

## <a name="move-resources"></a>Spostare le risorse

Lo script seguente mostra come rimuovere un account di archiviazione da un gruppo di risorse a un altro gruppo di risorse.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloccare le risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente risorse critiche, ad esempio una sottoscrizione di Azure, un gruppo di risorse o una risorsa. 

Lo script seguente blocca un account di archiviazione in modo che non possa essere eliminato.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Lo script seguente ottiene tutti i blocchi per un account di archiviazione:The following script gets all locks for a storage account:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Lo script seguente elimina un blocco di un account di archiviazione:The following script deletes a lock of a storage account:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Per altre informazioni, vedere [Bloccare le risorse con Azure Resource Manager.For](lock-resources.md)more information, see Lock resources with Azure Resource Manager.

## <a name="tag-resources"></a>Aggiungere tag alle risorse

L'assegnazione di tag consente di organizzare il gruppo di risorse e le risorse in modo logico. Per informazioni, vedere Uso dei tag per organizzare le risorse di Azure.For information, see [Using tags to organize your Azure resources](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Gestire l'accesso alle risorse

Il controllo degli accessi in base al ruolo è il modo in cui si gestisce l'accesso alle risorse in [Azure.Role-based access control (RBAC)](../../role-based-access-control/overview.md) is the way that you manage access to resources in Azure. Per altre informazioni, vedere [Gestire l'accesso tramite RBAC e l'interfaccia della riga di comando](../../role-based-access-control/role-assignments-cli.md)di Azure.For more information, see Manage access using RBAC and Azure CLI.

## <a name="next-steps"></a>Passaggi successivi

- Per apprendere Azure Resource Manager, vedere Panoramica di Azure Resource Manager.To learn Azure [Resource Manager,](overview.md)see Azure Resource Manager overview .
- Per informazioni sulla sintassi dei modelli di Resource Manager, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](../templates/template-syntax.md)
- Per informazioni su come sviluppare modelli, consultate le [esercitazioni dettagliate.](/azure/azure-resource-manager/)
- Per visualizzare gli schemi di modello di Azure Resource Manager, vedere [riferimento al modello](/azure/templates/).
