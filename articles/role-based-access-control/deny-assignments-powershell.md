---
title: Elencare le assegnazioni di negazione per le risorse di Azure con Azure PowerShellList deny assignments for Azure resources with Azure PowerShell
description: Informazioni su come elencare gli utenti, i gruppi, le entità servizio e le identità gestite a cui è stato negato l'accesso a specifiche azioni delle risorse di Azure in ambiti specifici tramite Azure PowerShell.Learn how to list the users, groups, service principals, and managed identities that have been denied access to specific Azure resource actions at particular scopes using Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137385"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Elencare le assegnazioni di negazione per le risorse di Azure usando Azure PowerShellList deny assignments for Azure resources using Azure PowerShell

Le [assegnazioni di rifiuto](deny-assignments.md) impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso. Questo articolo descrive come elencare le assegnazioni di negazione usando Azure PowerShell.This article describes how to list deny assignments using Azure PowerShell.

> [!NOTE]
> Non è possibile creare direttamente le proprie assegnazioni di negazione. Per informazioni sulla modalità di creazione delle assegnazioni di negazione, vedere [Negare le assegnazioni](deny-assignments.md).

## <a name="prerequisites"></a>Prerequisiti

Per ottenere informazioni su un'assegnazione di negazione, è necessario disporre di:

- `Microsoft.Authorization/denyAssignments/read`autorizzazioni, inclusa nella maggior parte dei ruoli predefiniti per le risorse di AzurePermission, which is included in most [built-in roles for Azure resources](built-in-roles.md)
- [PowerShell in Azure Cloud Shell](/azure/cloud-shell/overview) o Azure [PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Visualizzare le assegnazioni di rifiuto

### <a name="list-all-deny-assignments"></a>Elenca tutte le assegnazioni di negazione

Per elencare tutte le assegnazioni di negazione per la sottoscrizione corrente, utilizzare [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Elencare le assegnazioni di negazione in un ambito di gruppo di risorseList deny assignments at a resource group scope

Per elencare tutte le assegnazioni di negazione in un ambito di gruppo di risorse, utilizzare [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Elencare le assegnazioni di negazione in un ambito di sottoscrizioneList deny assignments at a subscription scope

Per elencare tutte le assegnazioni di negazione in un ambito di sottoscrizione, utilizzare [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Per ottenere l'ID sottoscrizione, è possibile trovarlo nel pannello **Sottoscrizioni** nel portale di Azure oppure usare [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle assegnazioni di rifiuto per le risorse di Azure](deny-assignments.md)
- [Elencare le assegnazioni di negazione per le risorse di Azure usando il portale di AzureList deny assignments for Azure resources using the Azure portal](deny-assignments-portal.md)
- [Elencare le assegnazioni di rifiuto per le risorse di Azure usando l'API REST](deny-assignments-rest.md)