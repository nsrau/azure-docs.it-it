---
title: Esercitazione - Concedere l'accesso alle risorse di Azure a un utente usando il controllo degli accessi in base al ruolo e il modello di Resource Manager | Microsoft Docs
description: Informazioni su come concedere l'accesso alle risorse di Azure a un utente usando il controllo degli accessi in base al ruolo tramite il modello di Azure Resource Manager.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control,azure-resource-manager
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: e99a9d2cfa38c9b2ea74f9075b18f81006b34881
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65802750"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Esercitazione: Concedere a un utente l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il modello di Resource Manager

Il [controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse di Azure. Questa esercitazione illustra come creare un gruppo di risorse e concede un accesso a un utente in modo che possa creare e gestire macchine virtuali nel gruppo di risorse. Questa esercitazione è incentrata sul processo di distribuzione di un modello di Resource Manager per concedere l'accesso. Per altre informazioni sullo sviluppo di modelli di Resource Manager, vedere la [documentazione di Resource Manager](/azure/azure-resource-manager/) e le [informazioni di riferimento sui modelli](/azure/templates/microsoft.authorization/allversions
).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Concedere l'accesso per un utente nell'ambito di un gruppo di risorse
> * Convalidare la distribuzione
> * Eseguire la pulizia

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere e rimuovere assegnazioni di ruoli, l'utente deve disporre di:

* autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)

## <a name="grant-access"></a>Concedere l'accesso

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Altri modelli correlati all'autorizzazione di Azure sono reperibili [qui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Per distribuire il modello, selezionare **Prova** per aprire Azure Cloud Shell e quindi incollare lo script PowerShell seguente nella finestra della shell. Per incollare il codice, fare clic con il pulsante destro del mouse nella finestra della shell e quindi selezionare **Incolla**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Aprire il gruppo di risorse creato nella procedura precedente. Il nome predefinito è il nome del progetto con **rg** aggiunto.
1. Selezionare **Controllo di accesso (IAM)** dal menu a sinistra.
1. Selezionare le **Assegnazioni di ruoli**. 
1. In **Nome**, immettere l'indirizzo di posta elettronica digitato nella procedura precedente. Verrà visualizzato che l'utente con l'indirizzo di posta elettronica ha il ruolo **Collaboratore macchina virtuale**.

## <a name="clean-up"></a>Eseguire la pulizia

Per rimuovere il gruppo di risorse creato nella procedura precedente, selezionare **Prova** per aprire Azure Cloud Shell e quindi incollare lo script PowerShell seguente nella finestra della shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Concedere a un utente l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e Azure PowerShell](tutorial-role-assignments-user-powershell.md)