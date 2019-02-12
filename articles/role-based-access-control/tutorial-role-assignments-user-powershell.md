---
title: "Esercitazione: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e Azure PowerShell | Microsoft Docs"
description: Usare il controllo degli accessi in base al ruolo per consentire a un utente di visualizzare tutte le aree di una sottoscrizione e di gestire tutti gli elementi in un gruppo di risorse con Azure PowerShell.
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
ms.openlocfilehash: 27b48e1d6aabf9cde7152bfb0dbf3e58bc619107
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696779"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-azure-powershell"></a>Esercitazione: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e Azure PowerShell

[Il controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. In questa esercitazione si consente a un utente di visualizzare tutte le aree di una sottoscrizione e di gestire tutti gli elementi in un gruppo di risorse con Azure PowerShell.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Concedere l'accesso a un utente in ambiti diversi
> * Elencare l'accesso
> * Rimuovere un accesso

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Autorizzazioni per creare utenti in Azure Active Directory (oppure avere un utente esistente)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Assegnazioni di ruoli

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito. Di seguito sono indicate le due assegnazioni di ruolo che verranno eseguite in questa esercitazione:

| Entità di sicurezza | Definizione di ruolo | Scope |
| --- | --- | --- |
| Utente<br>(Utente dell'esercitazione sul controllo degli accessi in base al ruolo) | [Lettore](built-in-roles.md#reader) | Sottoscrizione |
| Utente<br>(Utente dell'esercitazione sul controllo degli accessi in base al ruolo)| [Collaboratore](built-in-roles.md#contributor) | Gruppo di risorse<br>(rbac-tutorial-resource-group) |

   ![Assegnazione dei ruoli a un utente](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>Creare un utente

Per assegnare un ruolo sono necessari un utente, un gruppo o un entità servizio. Se l'utente non è già disponibile, è possibile crearne uno.

1. In Azure Cloud Shell creare una password conforme ai propri requisiti di complessità delle password.

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. Creare un nuovo utente per il proprio dominio mediante il comando [New-AzureADUser](/powershell/module/azuread/new-azureaduser).

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

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

Per concedere l'accesso all'utente, si usa il comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) per assegnare un ruolo. È necessario specificare l'entità di sicurezza, la definizione del ruolo e l'ambito.

1. Ottenere l'ID della sottoscrizione usando il comando [Get-AzSubscription](/powershell/module/az.profile/get-azsubscription).

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

1. Assegnare il ruolo [Lettore](built-in-roles.md#reader) all'utente nell'ambito della sottoscrizione.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. Assegnare il ruolo [Collaboratore](built-in-roles.md#contributor) all'utente nell'ambito del gruppo di risorse.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>Elencare l'accesso

1. Per verificare l'accesso alla sottoscrizione, usare il comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) per elencare le assegnazioni dei ruoli.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Nell'output si può vedere che il ruolo di lettore è stato assegnato all'utente dell'esercitazione sul controllo degli accessi in base al ruolo nell'ambito della sottoscrizione.

1. Per verificare l'accesso al gruppo di risorse, usare il comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) per elencare le assegnazioni dei ruoli.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Nell'output si può vedere che sia il ruolo di collaboratore che quello di lettore sono stati assegnati all'utente dell'esercitazione sul controllo degli accessi in base al ruolo. Il ruolo di collaboratore è nell'ambito rbac-tutorial-resource-group, mentre il ruolo di lettore viene ereditato nell'ambito della sottoscrizione.

## <a name="optional-list-access-using-the-azure-portal"></a>(Facoltativo) Elencare gli accessi con il portale di Azure

1. Per visualizzare le assegnazioni dei ruoli nel portale di Azure, passare al pannello **Controllo di accesso (IAM)** della sottoscrizione.

    ![Assegnazione di ruoli a un utente nell'ambito della sottoscrizione](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. Visualizzare il pannello **Controllo di accesso (IAM)** del gruppo di risorse.

    ![Assegnazioni dei ruoli a un utente nell'ambito del gruppo di risorse](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>Rimuovere un accesso

Per rimuovere l'accesso per utenti, gruppi e applicazioni, usare [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) per rimuovere l'assegnazione di un ruolo.

1. Usare il comando seguente per rimuovere l'assegnazione del ruolo di collaboratore per l'utente nell'ambito del gruppo di risorse.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Usare il comando seguente per rimuovere l'assegnazione del ruolo di lettore per l'utente nell'ambito della sottoscrizione.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create con questa esercitazione, eliminare il gruppo di risorse e l'utente.

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

1. Eliminare l'utente usando il comando [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser).

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e PowerShell](role-assignments-powershell.md)
