---
title: Gestire gruppi di Azure Resource Manager usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Usare l'interfaccia della riga di comando di Azure per gestire i gruppi di risorse tramite Azure Resource Manager. Viene illustrato come creare, elencare ed eliminare gruppi di risorse.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: d6a6ea0425bc9dd08e7eedb520b4fa1334ad242f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390434"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gestire Azure Resource Manager gruppi di risorse usando l'interfaccia della riga di comando di Azure

Informazioni su come usare l'interfaccia della riga di comando di Azure con [Azure Resource Manager](resource-group-overview.md) per gestire i gruppi di risorse di Azure. Per gestire le risorse di Azure, vedere [gestire le risorse di Azure con l'interfaccia della riga di comando di Azure](./manage-resources-cli.md)

Altri articoli sulla gestione dei gruppi di risorse:

- [Gestire i gruppi di risorse di Azure usando il portale di Azure](./manage-resources-portal.md)
- [Gestire i gruppi di risorse di Azure usando Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica una posizione per il gruppo di risorse, si specifica dove vengono archiviati tali metadati.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

Lo script dell'interfaccia della riga di comando seguente crea un gruppo di risorse e quindi Mostra il gruppo di risorse.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

Il seguente script dell'interfaccia della riga di comando elenca i gruppi di risorse nella sottoscrizione.

```azurecli-interactive
az group list
```

Per ottenere un gruppo di risorse:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Elimina gruppi di risorse

Il seguente script dell'interfaccia della riga di comando Elimina un gruppo di risorse:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Per altre informazioni su come Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [Azure Resource Manager eliminazione del gruppo di risorse](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire le risorse in un gruppo di risorse esistente

Vedere [distribuire le risorse in un gruppo di risorse esistente](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorse

È possibile creare un gruppo di risorse e distribuire le risorse nel gruppo usando un modello di Gestione risorse. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Eseguire nuovamente la distribuzione se non è riuscita

Questa funzionalità è nota anche come *rollback in errore*. Per ulteriori informazioni, vedere [ridistribuzione quando la distribuzione ha esito negativo](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Passare a un altro gruppo di risorse o a una sottoscrizione

È possibile spostare le risorse del gruppo in un altro gruppo di risorse. Per altre informazioni, vedere [spostare le risorse](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Blocca gruppi di risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche, ad esempio la sottoscrizione di Azure, il gruppo di risorse o la risorsa. 

Lo script seguente blocca un gruppo di risorse in modo che il gruppo di risorse non possa essere eliminato.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Lo script seguente ottiene tutti i blocchi per un gruppo di risorse:

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

Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Contrassegnare i gruppi di risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere [uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Esportare gruppi di risorse nei modelli

Dopo aver configurato correttamente il gruppo di risorse, potrebbe essere necessario visualizzare il modello di Gestione risorse per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

- Automatizzare le distribuzioni future della soluzione perché il modello contiene tutte le infrastrutture complete.
- Per informazioni sulla sintassi del modello, vedere il JavaScript Object Notation (JSON) che rappresenta la soluzione.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Lo script Visualizza il modello nella console.  Copiare il codice JSON e salvarlo come file.

Per altre informazioni, vedere [esportazione a più risorse e a modello in portale di Azure](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorse

[Il controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [gestire l'accesso con RBAC e l'interfaccia della riga](../role-based-access-control/role-assignments-cli.md)di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](./resource-group-overview.md).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](./resource-group-authoring-templates.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](/azure/azure-resource-manager/).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).