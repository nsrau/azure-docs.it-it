---
title: Gestire i gruppi di risorse - Interfaccia della riga di comando di AzureManage resource groups - Azure CLI
description: Usare l'interfaccia della riga di comando di Azure per gestire i gruppi di risorse tramite Azure Resource Manager.Use Azure CLI to manage your resource groups through Azure Resource Manager. Viene illustrato come creare, elencare ed eliminare gruppi di risorse.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248333"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gestire i gruppi di risorse di Azure Resource Manager usando l'interfaccia della riga di comando di AzureManage Azure Resource Manager resource groups by using Azure CLI

Informazioni su come usare l'interfaccia della riga di comando di Azure con Azure Resource Manager per gestire i gruppi di risorse di Azure.Learn how to use Azure CLI with [Azure Resource Manager](overview.md) to manage your Azure resource groups. Per la gestione delle risorse di Azure, vedere Gestire le risorse di Azure usando l'interfaccia della riga di comando di Azure.For managing Azure resources, see [Manage Azure resources by using Azure CLI.](manage-resources-cli.md)

Altri articoli sulla gestione dei gruppi di risorse:Other articles about managing resource groups:

- [Gestire i gruppi di risorse di Azure tramite il portale di AzureManage Azure resource groups by using the Azure portal](manage-resources-portal.md)
- [Gestire i gruppi di risorse di Azure tramite Azure PowerShellManage Azure resource groups by using Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorseWhat is a resource group

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica una posizione per il gruppo di risorse, si specifica dove vengono archiviati tali metadati.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

Lo script dell'interfaccia della riga di comando seguente crea un gruppo di risorse e quindi visualizza il gruppo di risorse.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

Lo script CLI seguente elenca i gruppi di risorse nella sottoscrizione.

```azurecli-interactive
az group list
```

Per ottenere un gruppo di risorse:To get one resource group:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Eliminare i gruppi di risorse

Lo script CLI seguente elimina un gruppo di risorse:The following CLI script deletes a resource group:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Per altre informazioni su come Azure Resource Manager ordina l'eliminazione delle risorse, vedere Eliminazione del gruppo di risorse di Azure Resource Manager.For more information about how Azure Resource Manager orders the deletion of resources, see [Azure Resource Manager resource group deletion.](delete-resource-group.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire risorse in un gruppo di risorse esistenteDeploy resources to an existing resource group

Vedere [Distribuire risorse in un gruppo di risorse esistente.](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorseDeploy a resource group and resources

È possibile creare un gruppo di risorse e distribuire le risorse al gruppo usando un modello di Resource Manager.You can create a resource group and deploy resources to the group by using a Resource Manager template. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Eseguire nuovamente la distribuzione se non è riuscita

Questa funzionalità è nota anche come *Rollback in caso di errore*. Per ulteriori informazioni, vedere [Ridistribuire quando la distribuzione non riesce.](../templates/rollback-on-error.md)

## <a name="move-to-another-resource-group-or-subscription"></a>Passare a un altro gruppo di risorse o sottoscrizione

È possibile spostare le risorse del gruppo in un altro gruppo di risorse. Per ulteriori informazioni, consultate [Spostare le risorse.](manage-resources-cli.md#move-resources)

## <a name="lock-resource-groups"></a>Bloccare i gruppi di risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente risorse critiche, ad esempio una sottoscrizione di Azure, un gruppo di risorse o una risorsa. 

Lo script seguente blocca un gruppo di risorse in modo che il gruppo di risorse non possa essere eliminato.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Lo script seguente ottiene tutti i blocchi per un gruppo di risorse:The following script gets all locks for a resource group:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Lo script seguente elimina un blocco:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Per altre informazioni, vedere [Bloccare le risorse con Azure Resource Manager.For](lock-resources.md)more information, see Lock resources with Azure Resource Manager.

## <a name="tag-resource-groups"></a>Contrassegnare i gruppi di risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere Uso dei tag per organizzare le risorse di Azure.For information, see [Using tags to organize your Azure resources](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Esportare gruppi di risorse in modelliExport resource groups to templates

Dopo aver impostato correttamente il gruppo di risorse, è possibile visualizzare il modello Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

- Automatizza le distribuzioni future della soluzione perché il modello contiene tutta l'infrastruttura completa.
- È possibile ottenere la sintassi del modello esaminando la notazione di oggetto JavaScript (JSON) che rappresenta la soluzione.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Lo script visualizza il modello sulla console.  Copiare il codice JSON e salvarlo come file.

La funzionalità del modello di esportazione non supporta l'esportazione delle risorse di Azure Data Factory.The export template feature doesn't support exporting Azure Data Factory resources. Per informazioni su come esportare le risorse di Data Factory, vedere [Copiare o clonare una data factory in Azure Data Factory.](https://aka.ms/exportTemplateViaAdf)

Per esportare le risorse create tramite il modello di distribuzione classica, è necessario [eseguirne la migrazione al modello](https://aka.ms/migrateclassicresourcetoarm)di distribuzione di Resource Manager.

Per altre informazioni, vedere Esportazione di risorse singole e multirisorse nel modello nel portale di Azure.For more information, see [Single and multi-resource export to template in Azure portal.](../templates/export-template-portal.md)

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorseManage access to resource groups

Il controllo degli accessi in base al ruolo è il modo in cui si gestisce l'accesso alle risorse in [Azure.Role-based access control (RBAC)](../../role-based-access-control/overview.md) is the way that you manage access to resources in Azure. Per altre informazioni, vedere [Gestire l'accesso tramite RBAC e l'interfaccia della riga di comando](../../role-based-access-control/role-assignments-cli.md)di Azure.For more information, see Manage access using RBAC and Azure CLI.

## <a name="next-steps"></a>Passaggi successivi

- Per apprendere Azure Resource Manager, vedere Panoramica di Azure Resource Manager.To learn Azure [Resource Manager,](overview.md)see Azure Resource Manager overview .
- Per informazioni sulla sintassi dei modelli di Resource Manager, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](../templates/template-syntax.md)
- Per informazioni su come sviluppare modelli, consultate le [esercitazioni dettagliate.](/azure/azure-resource-manager/)
- Per visualizzare gli schemi di modello di Azure Resource Manager, vedere [riferimento al modello](/azure/templates/).