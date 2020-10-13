---
title: Gestire le risorse-interfaccia della riga di comando di Azure
description: Usare l'interfaccia della riga di comando di Azure e Azure Resource Manager per gestire le risorse. Viene illustrato come distribuire ed eliminare risorse.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9111a16a4264c4b225eaf0888fc09bd1bba07ef4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372120"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Gestire le risorse di Azure usando l'interfaccia della riga di comando

Informazioni su come usare l'interfaccia della riga di comando di Azure con [Azure Resource Manager](overview.md) per gestire le risorse di Azure. Per la gestione dei gruppi di risorse, vedere [gestire i gruppi di risorse di Azure usando l'interfaccia della riga di](manage-resource-groups-cli.md)comando

Altri articoli sulla gestione delle risorse:

- [Gestire le risorse di Azure usando il portale di Azure](manage-resources-portal.md)
- [Gestire le risorse di Azure tramite Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire le risorse in un gruppo di risorse esistente

È possibile distribuire direttamente le risorse di Azure usando l'interfaccia della riga di comando di Azure o distribuire un modello di Gestione risorse per creare risorse di Azure.

### <a name="deploy-a-resource"></a>Distribuire una risorsa

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

Lo script seguente crea la distribuzione di un modello di avvio rapido per creare un account di archiviazione. Per altre informazioni, vedere [Guida introduttiva: creare modelli di Azure Resource Manager usando Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorse

È possibile creare un gruppo di risorse e distribuire le risorse al gruppo. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](../templates/deploy-to-subscription.md#resource-groups).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuire le risorse a più sottoscrizioni o gruppi di risorse

In genere si distribuiscono tutte le risorse del modello in un unico gruppo di risorse, ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di sottoscrizioni e risorse diversi. Per altre informazioni, vedere [distribuire risorse di Azure a più sottoscrizioni o gruppi di risorse](../templates/cross-scope-deployment.md).

## <a name="delete-resources"></a>Eliminare le risorse

Lo script seguente mostra come eliminare un account di archiviazione.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Per altre informazioni su come Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [Azure Resource Manager eliminazione del gruppo di risorse](delete-resource-group.md).

## <a name="move-resources"></a>Spostare le risorse

Lo script seguente illustra come rimuovere un account di archiviazione da un gruppo di risorse a un altro gruppo di risorse.

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

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche, ad esempio la sottoscrizione di Azure, il gruppo di risorse o la risorsa. 

Lo script seguente blocca un account di archiviazione in modo che l'account non possa essere eliminato.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Lo script seguente ottiene tutti i blocchi per un account di archiviazione:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Lo script seguente elimina un blocco di un account di archiviazione:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Per altre informazioni, vedere [bloccare le risorse con Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Aggiungere tag alle risorse

L'assegnazione di tag consente di organizzare logicamente il gruppo di risorse e le risorse. Per informazioni, vedere [uso dei tag per organizzare le risorse di Azure](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Gestire l'accesso alle risorse

Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure tramite l'interfaccia](../../role-based-access-control/role-assignments-cli.md)della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](overview.md).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../templates/template-syntax.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](../index.yml).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).
