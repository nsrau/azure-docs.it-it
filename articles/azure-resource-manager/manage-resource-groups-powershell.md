---
title: Gestire i gruppi di Azure Resource Manager usando Azure PowerShell | Microsoft Docs
description: Usare Azure PowerShell per gestire i gruppi di Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 8ae86d8bc7914a7a9c41eee93bb16b2f774993b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550496"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gestire gruppi di risorse di Azure Resource Manager usando Azure PowerShell

Informazioni su come usare Azure PowerShell con [Azure Resource Manager](resource-group-overview.md) per gestire i gruppi di risorse di Azure. Per gestire le risorse di Azure, vedere [la gestione di Azure Resource Manager usando Azure PowerShell](./manage-resources-powershell.md).

Altri articoli sulla gestione dei gruppi di risorse:

- [Gestire i gruppi di risorse di Azure usando il portale di Azure](./manage-resources-portal.md)
- [Gestire i gruppi di risorse di Azure utilizzando CLI di Azure](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica in cui vengono archiviati i metadati.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

Lo script di PowerShell seguente crea un gruppo di risorse e quindi Mostra il gruppo di risorse.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

Lo script di PowerShell seguente elenca i gruppi di risorse nella sottoscrizione.

```azurepowershell-interactive
Get-AzResourceGroup
```

Per ottenere un gruppo di risorse:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Eliminare gruppi di risorse

Il seguente script PowerShell consente di eliminare un gruppo di risorse:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Per altre informazioni sulla modalità Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [eliminazione del gruppo di risorse Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire risorse in un gruppo di risorse

Visualizzare [distribuire risorse in un gruppo di risorse](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Per convalidare la distribuzione di un gruppo di risorse, vedere [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorse

È possibile creare un gruppo di risorse e distribuire le risorse nel gruppo usando un modello di Resource Manager. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Eseguire nuovamente la distribuzione se non è riuscita

Questa funzionalità è detta anche *Rollback in caso di errore*. Per altre informazioni, vedere [ridistribuire quando si verifica un errore di distribuzione](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Spostare in un gruppo di risorse o sottoscrizione

È possibile spostare le risorse nel gruppo a un altro gruppo di risorse. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](./resource-group-move-resources.md#move-resources).

## <a name="lock-resource-groups"></a>Gruppi di risorse di blocco

Il blocco impedisce ad altri utenti nell'organizzazione modifichino o eliminino accidentalmente risorse critiche, come sottoscrizione di Azure, gruppo di risorse o risorsa. 

Lo script seguente consente di bloccare un gruppo di risorse in modo che non è possibile eliminare il gruppo di risorse.

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

## <a name="tag-resource-groups"></a>Gruppi di risorse di tag

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere [usando i tag per organizzare le risorse di Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Esportare i gruppi di risorse in modelli

Dopo aver impostato correttamente il gruppo di risorse, è possibile visualizzare il modello di Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

- Automatizzare le distribuzioni future della soluzione perché il modello contiene l'infrastruttura completa.
- Informazioni su sintassi del modello esaminando in oggetto notazione JSON (JavaScript) che rappresenta la soluzione.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Per altre informazioni, vedere [esportazione di gruppi di risorse](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [gestire l'accesso tramite RBAC e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per altre informazioni sulla sintassi del modello di Resource Manager, vedere [comprendere la struttura e sintassi di modelli Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere la [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere [riferimento a un modello](/azure/templates/).