---
title: Elencare le assegnazioni di ruolo usando RBAC di Azure e Azure PowerShell
description: Informazioni su come determinare le risorse a cui utenti, gruppi, entità servizio o identità gestite possono accedere usando il controllo degli accessi in base al ruolo di Azure (RBAC) e Azure PowerShell.
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
ms.openlocfilehash: c30f1246dccbe14445b0f7db8584e37c0f4be6ab
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710412"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Elencare le assegnazioni di ruolo usando RBAC di Azure e Azure PowerShell

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] in questo articolo viene descritto come elencare le assegnazioni di ruolo utilizzando Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- [PowerShell in Azure cloud Shell](/azure/cloud-shell/overview) o [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Elencare le assegnazioni di ruolo per la sottoscrizione corrente

Il modo più semplice per ottenere un elenco di tutte le assegnazioni di ruolo nella sottoscrizione corrente (incluse le assegnazioni di ruolo ereditate dai gruppi di gestione e radice) consiste nell'usare [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) senza parametri.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Elencare i ruoli assegnati a una sottoscrizione

Per elencare tutte le assegnazioni di ruolo nell'ambito di una sottoscrizione, usare [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Per ottenere l'ID sottoscrizione, è possibile trovarlo nel pannello **sottoscrizioni** nella portale di Azure oppure è possibile usare [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente

Per elencare tutti i ruoli assegnati a un utente specifico, usare [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Per elencare tutti i ruoli assegnati a un utente specifico e i ruoli assegnati ai gruppi a cui appartiene l'utente, usare [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Elencare le assegnazioni di ruolo per un gruppo di risorse

Per elencare tutte le assegnazioni di ruolo in un ambito del gruppo di risorse, usare [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Elencare le assegnazioni di ruolo per un gruppo di gestione

Per elencare tutte le assegnazioni di ruolo in un ambito del gruppo di gestione, usare [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Per ottenere l'ID del gruppo di gestione, è possibile trovarlo nel pannello **gruppi di gestione** nel portale di Azure oppure è possibile usare [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Elencare le assegnazioni di ruolo per l'amministratore e i coamministratori del servizio classici

Per elencare le assegnazioni di ruolo per l'amministratore e i coamministratori della sottoscrizione classici, usare [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e Azure PowerShell](role-assignments-powershell.md)
