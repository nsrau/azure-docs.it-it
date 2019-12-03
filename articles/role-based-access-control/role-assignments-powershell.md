---
title: Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e Azure PowerShell
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando il controllo degli accessi in base al ruolo di Azure (RBAC) e Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9c16ec978a0689059c60903182337936960c7a4b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707839"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] questo articolo descrive come assegnare i ruoli usando Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)
- [PowerShell in Azure cloud Shell](/azure/cloud-shell/overview) o [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Ottenere gli ID oggetto

Per aggiungere o rimuovere assegnazioni di ruolo, potrebbe essere necessario specificare l'ID univoco di un oggetto. Il formato dell'ID è: `11111111-1111-1111-1111-111111111111`. È possibile ottenere l'ID usando il portale di Azure o Azure PowerShell.

### <a name="user"></a>Utente

Per ottenere l'ID oggetto per un utente di Azure AD, è possibile usare [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Group

Per ottenere l'ID oggetto per un gruppo di Azure AD, è possibile usare [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Richiesta

Per ottenere l'ID oggetto per un'entità servizio Azure AD (identità usata da un'applicazione), è possibile usare [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Per un'entità servizio, usare l'ID oggetto e **non** l'ID applicazione.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

In RBAC, per concedere l'accesso, si aggiunge un'assegnazione di ruolo.

### <a name="user-at-a-resource-group-scope"></a>Utente in un ambito del gruppo di risorse

Per aggiungere un'assegnazione di ruolo per un utente in un ambito del gruppo di risorse, usare [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Utilizzo dell'ID ruolo univoco

Ci sono un paio di volte in cui un nome di ruolo potrebbe cambiare, ad esempio:

- Si usa il proprio ruolo personalizzato e si decide di modificare il nome.
- Si sta usando un ruolo di anteprima con **(anteprima)** nel nome. Quando il ruolo viene rilasciato, il ruolo viene rinominato.

> [!IMPORTANT]
> Una versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Anche se un ruolo viene rinominato, l'ID del ruolo non cambia. Se si usano script o automazione per creare le assegnazioni di ruolo, è consigliabile usare l'ID del ruolo univoco anziché il nome del ruolo. Di conseguenza, se un ruolo viene rinominato, è più probabile che gli script funzionino.

Per aggiungere un'assegnazione di ruolo usando l'ID del ruolo univoco anziché il nome del ruolo, usare [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

L'esempio seguente assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) a *alain\@utente example.com* nell'ambito del gruppo di risorse *Pharma-Sales* . Per ottenere l'ID del ruolo univoco, è possibile usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) o vedere [ruoli predefiniti per le risorse di Azure](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Gruppo nell'ambito di una risorsa

Per aggiungere un'assegnazione di ruolo per un gruppo in un ambito di risorse, usare [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Per informazioni su come ottenere l'ID oggetto del gruppo, vedere ottenere gli [ID oggetto](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Applicazione nell'ambito di una sottoscrizione

Per aggiungere un'assegnazione di ruolo per un'applicazione in un ambito di sottoscrizione, usare [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Per informazioni su come ottenere l'ID oggetto dell'applicazione, vedere ottenere gli [ID oggetto](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Utente presso un ambito del gruppo di gestione

Per aggiungere un'assegnazione di ruolo per un utente in un ambito del gruppo di gestione, usare [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Per ottenere l'ID del gruppo di gestione, è possibile trovarlo nel pannello **gruppi di gestione** nel portale di Azure oppure è possibile usare [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Nel controllo degli accessi in base al ruolo, per rimuovere un accesso, è possibile rimuovere un'assegnazione di ruolo tramite [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Nell'esempio seguente viene rimossa l'assegnazione di ruolo *collaboratore macchina virtuale* dall'utente *Alain\@example.com* nel gruppo di risorse *Pharma-Sales* :

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Nell'esempio seguente viene rimossa la < role_name > Role da < object_id > in un ambito di sottoscrizione.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

Nell'esempio seguente viene rimossa la < role_name > Role da < object_id > nell'ambito del gruppo di gestione.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Se viene visualizzato il messaggio di errore: "le informazioni fornite non sono mappate a un'assegnazione di ruolo", assicurarsi di specificare anche i parametri `-Scope` o `-ResourceGroupName`. Per altre informazioni, vedere [risolvere i problemi relativi a RBAC per le risorse di Azure](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo usando RBAC di Azure e Azure PowerShell](role-assignments-list-powershell.md)
- [Esercitazione: concedere a un gruppo l'accesso alle risorse di Azure usando RBAC e Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Esercitazione: creare un ruolo personalizzato per le risorse di Azure usando Azure PowerShell](tutorial-custom-role-powershell.md)
- [Gestire le risorse con Azure PowerShell](../azure-resource-manager/manage-resources-powershell.md)
