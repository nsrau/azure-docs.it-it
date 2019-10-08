---
title: Gestire i gruppi di Azure Resource Manager tramite Azure PowerShell | Microsoft Docs
description: Usare Azure PowerShell per gestire i gruppi di Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 59c83cb1becf8869d0e8710121816d1bb75e621e
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001573"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gestire i gruppi di risorse Azure Resource Manager tramite Azure PowerShell

Informazioni su come usare Azure PowerShell con [Azure Resource Manager](resource-group-overview.md) per gestire i gruppi di risorse di Azure. Per la gestione delle risorse di Azure, vedere [gestire le risorse di Azure usando Azure PowerShell](./manage-resources-powershell.md).

Altri articoli sulla gestione dei gruppi di risorse:

- [Gestire i gruppi di risorse di Azure usando il portale di Azure](./manage-resources-portal.md)
- [Gestire i gruppi di risorse di Azure usando l'interfaccia della riga di comando](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica una posizione per il gruppo di risorse, si specifica dove vengono archiviati tali metadati.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

Lo script di PowerShell seguente crea un gruppo di risorse e quindi Mostra il gruppo di risorse.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

Il seguente script di PowerShell elenca i gruppi di risorse nella sottoscrizione.

```azurepowershell-interactive
Get-AzResourceGroup
```

Per ottenere un gruppo di risorse:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Elimina gruppi di risorse

Il seguente script di PowerShell Elimina un gruppo di risorse:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Per altre informazioni su come Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [Azure Resource Manager eliminazione del gruppo di risorse](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire le risorse in un gruppo di risorse esistente

Vedere [distribuire le risorse in un gruppo di risorse esistente](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Per convalidare la distribuzione di un gruppo di risorse, vedere [test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorse

È possibile creare un gruppo di risorse e distribuire le risorse nel gruppo usando un modello di Gestione risorse. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Eseguire nuovamente la distribuzione se non è riuscita

Questa funzionalità è nota anche come *rollback in errore*. Per ulteriori informazioni, vedere [ridistribuzione quando la distribuzione ha esito negativo](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Passare a un altro gruppo di risorse o a una sottoscrizione

È possibile spostare le risorse del gruppo in un altro gruppo di risorse. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](./resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Blocca gruppi di risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche, ad esempio la sottoscrizione di Azure, il gruppo di risorse o la risorsa. 

Lo script seguente blocca un gruppo di risorse in modo che il gruppo di risorse non possa essere eliminato.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Lo script seguente ottiene tutti i blocchi per un gruppo di risorse:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Contrassegnare i gruppi di risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere [uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Esportare gruppi di risorse nei modelli

Dopo aver configurato il gruppo di risorse, è possibile visualizzare un modello di Gestione risorse per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

- Automatizzare le distribuzioni future della soluzione perché il modello contiene l'intera infrastruttura.
- Per informazioni sulla sintassi del modello, vedere il JavaScript Object Notation (JSON) che rappresenta la soluzione.

Per esportare tutte le risorse in un gruppo di risorse, usare il cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) e specificare il nome del gruppo di risorse.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Salva il modello come file locale.

Invece di esportare tutte le risorse nel gruppo di risorse, è possibile selezionare le risorse da esportare.

Per esportare una risorsa, passare l'ID risorsa.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Per esportare più di una risorsa, passare gli ID risorsa in una matrice.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Quando si esporta il modello, è possibile specificare se i parametri vengono usati nel modello. Per impostazione predefinita, i parametri per i nomi delle risorse sono inclusi ma non hanno un valore predefinito. È necessario passare il valore del parametro durante la distribuzione.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

Nella risorsa viene usato il parametro per il nome.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Se si usa il parametro `-IncludeParameterDefaultValue` quando si esporta il modello, il parametro di modello include un valore predefinito impostato sul valore corrente. È possibile utilizzare il valore predefinito oppure sovrascrivere il valore predefinito passando un valore diverso.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Se si usa il parametro `-SkipResourceNameParameterization` quando si esporta il modello, i parametri per i nomi delle risorse non sono inclusi nel modello. Al contrario, il nome della risorsa viene impostato direttamente sulla risorsa sul valore corrente. Non è possibile personalizzare il nome durante la distribuzione.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Per altre informazioni, vedere [esportazione a più risorse e a modello in portale di Azure](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [gestire l'accesso con RBAC e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).