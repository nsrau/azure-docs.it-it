---
title: "Esercitazione: Concedere l'accesso per un gruppo tramite il controllo degli accessi in base al ruolo e Azure PowerShell | Microsoft Docs"
description: Usare il controllo degli accessi in base al ruolo per consentire a un gruppo di visualizzare tutte le aree di una sottoscrizione e di gestire tutti gli elementi in un gruppo di risorse con Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: ad6884067a756412efe27ce275387ff5ceb1b298
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105672"
---
# <a name="tutorial-grant-access-for-a-group-using-rbac-and-azure-powershell"></a>Esercitazione: Concedere l'accesso per un gruppo tramite il controllo degli accessi in base al ruolo e Azure PowerShell

[Il controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. In questa esercitazione si consente a un gruppo di visualizzare tutte le aree di una sottoscrizione e di gestire tutti gli elementi in un gruppo di risorse con Azure PowerShell.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Concedere l'accesso per un gruppo in ambiti diversi
> * Elencare l'accesso
> * Rimuovere un accesso

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Autorizzazioni per creare gruppi in Azure Active Directory (oppure avere un gruppo esistente)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Assegnazioni di ruoli

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito. Di seguito sono indicate le due assegnazioni di ruolo che verranno eseguite in questa esercitazione:

| Entità di sicurezza | Definizione di ruolo | Scope |
| --- | --- | --- |
| Gruppo<br>(Gruppo dell'esercitazione per il controllo degli accessi in base al ruolo) | [Lettore](built-in-roles.md#reader) | Sottoscrizione |
| Gruppo<br>(Gruppo dell'esercitazione per il controllo degli accessi in base al ruolo)| [Collaboratore](built-in-roles.md#contributor) | Gruppo di risorse<br>(rbac-tutorial-resource-group) |

   ![Assegnazioni di ruolo per un gruppo](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Creare un gruppo

Per assegnare un ruolo sono necessari un utente, un gruppo o un entità servizio. Se il gruppo non è già disponibile, è possibile crearne uno.

- In Azure Cloud Shell creare un nuovo gruppo con il comando [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Se non si hanno le autorizzazioni per creare gruppi, è possibile provare l'[Esercitazione: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e Azure PowerShell](tutorial-role-assignments-user-powershell.md).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Si usa un gruppo di risorse per illustrare come assegnare un ruolo a un ambito di gruppo di risorse.

1. Ottenere un elenco delle località dell'area con il comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Selezionare una località vicina e assegnarla a una variabile.

   ```azurepowershell
   $location = "westus"
   ```

1. Creare un nuovo gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Concedere l'accesso

Per concedere l'accesso al gruppo, si usa il comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) per assegnare un ruolo. È necessario specificare l'entità di sicurezza, la definizione del ruolo e l'ambito.

1. Ottenere l'ID oggetto del gruppo con il comando [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Salvare l'ID oggetto del gruppo in una variabile.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Ottenere l'ID della sottoscrizione usando il comando [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Salvare l'ambito della sottoscrizione in una variabile.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Assegnare il ruolo [Lettore](built-in-roles.md#reader) al gruppo nell'ambito della sottoscrizione.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Assegnare il ruolo [Collaboratore](built-in-roles.md#contributor) al gruppo nell'ambito del gruppo di risorse.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Elencare l'accesso

1. Per verificare l'accesso alla sottoscrizione, usare il comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) per elencare le assegnazioni dei ruoli.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Nell'output si può vedere che il ruolo di lettore è stato assegnato al gruppo dell'esercitazione per il controllo degli accessi in base al ruolo nell'ambito della sottoscrizione.

1. Per verificare l'accesso al gruppo di risorse, usare il comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) per elencare le assegnazioni dei ruoli.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Nell'output si può vedere che sia il ruolo di collaboratore che quello di lettore sono stati assegnati al gruppo dell'esercitazione per il controllo degli accessi in base al ruolo. Il ruolo di collaboratore è nell'ambito rbac-tutorial-resource-group, mentre il ruolo di lettore viene ereditato nell'ambito della sottoscrizione.

## <a name="optional-list-access-using-the-azure-portal"></a>(Facoltativo) Elencare gli accessi con il portale di Azure

1. Per visualizzare le assegnazioni dei ruoli nel portale di Azure, passare al pannello **Controllo di accesso (IAM)** della sottoscrizione.

    ![Assegnazione di ruoli per un gruppo nell'ambito della sottoscrizione](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Visualizzare il pannello **Controllo di accesso (IAM)** del gruppo di risorse.

    ![Assegnazioni dei ruoli per un gruppo nell'ambito del gruppo di risorse](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Rimuovere un accesso

Per rimuovere l'accesso per utenti, gruppi e applicazioni, usare [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) per rimuovere l'assegnazione di un ruolo.

1. Usare il comando seguente per rimuovere l'assegnazione del ruolo di collaboratore per il gruppo nell'ambito del gruppo di risorse.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Usare il comando seguente per rimuovere l'assegnazione del ruolo di lettore per il gruppo nell'ambito della sottoscrizione.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create con questa esercitazione, eliminare il gruppo di risorse e il gruppo.

1. Eliminare il gruppo di risorse usando il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Quando viene chiesto di confermare, digitare **Y**. L'eliminazione richiederà alcuni secondi.

1. Eliminare il gruppo usando il comando [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Se viene visualizzato un errore quando si tenta di eliminare il gruppo, è anche possibile eliminare il gruppo dal portale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e PowerShell](role-assignments-powershell.md)
