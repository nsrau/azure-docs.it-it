---
title: Aggiungere o rimuovere assegnazioni di ruolo di Azure usando Azure PowerShell-RBAC di Azure
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando Azure PowerShell e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
ms.openlocfilehash: c4082f7fc535807ec996034ba695549a51969a99
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182411"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Aggiungere o rimuovere assegnazioni di ruolo di Azure con Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Questo articolo descrive come assegnare i ruoli usando Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)
- [PowerShell in Azure cloud Shell](../cloud-shell/overview.md) o [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="steps-to-add-a-role-assignment"></a>Procedura per aggiungere un'assegnazione di ruolo

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo di Azure, aggiungere un'assegnazione di ruolo. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito. Per aggiungere un'assegnazione di ruolo, attenersi alla seguente procedura.

### <a name="step-1-determine-who-needs-access"></a>Passaggio 1: determinare chi necessita dell'accesso

È possibile assegnare un ruolo a un utente, un gruppo, un'entità servizio o un'identità gestita. Per aggiungere un'assegnazione di ruolo, potrebbe essere necessario specificare l'ID univoco dell'oggetto. Il formato dell'ID è il seguente: `11111111-1111-1111-1111-111111111111`. È possibile ottenere l'ID usando il portale di Azure o Azure PowerShell.

**Utente**

Per un utente Azure AD, ottenere il nome dell'entità utente, ad esempio *patlong \@ contoso.com* o l'ID oggetto utente. Per ottenere l'ID oggetto, è possibile usare [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Gruppo**

Per un gruppo di Azure AD, è necessario l'ID oggetto gruppo. Per ottenere l'ID oggetto, è possibile usare [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Entità servizio**

Per un'entità servizio Azure AD (identità usata da un'applicazione), è necessario l'ID oggetto dell'entità servizio. Per ottenere l'ID oggetto, è possibile usare [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Per un'entità servizio usare l'ID oggetto, **non** l'ID applicazione.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Identità gestita**

Per un'identità gestita assegnata dal sistema o assegnata dall'utente, è necessario l'ID oggetto. Per ottenere l'ID oggetto, è possibile usare [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-find-the-appropriate-role"></a>Passaggio 2: trovare il ruolo appropriato

Le autorizzazioni vengono raggruppate in ruoli. È possibile scegliere da un elenco di diversi [ruoli predefiniti di Azure](built-in-roles.md) oppure usare ruoli personalizzati. È consigliabile concedere l'accesso con il privilegio minimo necessario, quindi evitare di assegnare un ruolo più ampio.

Per elencare i ruoli e ottenere l'ID del ruolo univoco, è possibile usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Di seguito viene illustrato come elencare i dettagli di un particolare ruolo.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Per altre informazioni, vedere [elencare le definizioni dei ruoli di Azure](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>Passaggio 3: identificare l'ambito necessario

Azure offre quattro livelli di ambito: risorse, [gruppo di risorse](../azure-resource-manager/management/overview.md#resource-groups), sottoscrizione e [gruppo di gestione](../governance/management-groups/overview.md). È consigliabile concedere l'accesso con il privilegio minimo necessario, quindi evitare di assegnare un ruolo a un ambito più ampio. Per altre informazioni sull'ambito, vedere [Informazioni sull'ambito](scope-overview.md).

**Ambito risorsa**

Per ambito di risorse, è necessario l'ID risorsa per la risorsa. È possibile trovare l'ID risorsa esaminando le proprietà della risorsa nel portale di Azure. Un ID di risorsa ha il formato seguente.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Gruppo di risorse**

Per ambito del gruppo di risorse, è necessario il nome del gruppo di risorse. È possibile trovare il nome nella pagina dei **gruppi di risorse** nel portale di Azure oppure è possibile usare [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Ambito della sottoscrizione** 

Per ambito sottoscrizione, è necessario l'ID sottoscrizione. È possibile trovare l'ID nella pagina **sottoscrizioni** nella portale di Azure oppure è possibile usare [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Ambito del gruppo di gestione** 

Per ambito gruppo di gestione, è necessario il nome del gruppo di gestione. È possibile trovare il nome nella pagina **gruppi di gestione** nel portale di Azure oppure è possibile usare [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-add-role-assignment"></a>Passaggio 4: aggiungere l'assegnazione di ruolo

Per aggiungere un'assegnazione di ruolo, usare il comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . A seconda dell'ambito, il comando ha in genere uno dei formati seguenti.

**Ambito risorsa**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Gruppo di risorse**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Ambito della sottoscrizione** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Ambito del gruppo di gestione** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="add-role-assignment-examples"></a>Esempi di assegnazione di ruolo

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Aggiungere un'assegnazione di ruolo per tutti i contenitori BLOB in un ambito di risorse dell'account di archiviazione

Assegna il ruolo di [collaboratore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-contributor) a un'entità servizio con ID oggetto *55555555-5555-5555-5555-555555555555* in un ambito di risorse per un account di archiviazione denominato *storage12345*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Aggiungere un'assegnazione di ruolo per un ambito di risorsa contenitore BLOB specifico

Assegna il ruolo di [collaboratore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-contributor) a un'entità servizio con ID oggetto *55555555-5555-5555-5555-555555555555* in un ambito di risorse per un contenitore BLOB denominato *BLOB-container-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Aggiungere un'assegnazione di ruolo per un gruppo in un ambito di risorse della rete virtuale specifico

Assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) al gruppo *Pharma Sales Admins* con ID aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa in un ambito di risorse per una rete virtuale denominata *Pharma-Sales-Project-Network*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="add-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Aggiungere un'assegnazione di ruolo per un utente in un ambito del gruppo di risorse

Assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) all'utente *patlong \@ contoso.com* nell'ambito del gruppo di risorse *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

In alternativa, è possibile specificare il gruppo di risorse completo con il `-Scope` parametro:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Aggiungere l'assegnazione di ruolo per un utente usando l'ID del ruolo univoco in un ambito del gruppo di risorse

Ci sono un paio di volte in cui un nome di ruolo potrebbe cambiare, ad esempio:

- Si usa il proprio ruolo personalizzato e si decide di modificare il nome.
- Si sta usando un ruolo di anteprima con **(anteprima)** nel nome. Quando il ruolo viene rilasciato, il ruolo viene rinominato.

Anche se un ruolo viene rinominato, l'ID del ruolo non cambia. Se si usano script o automazione per creare le assegnazioni di ruolo, è consigliabile usare l'ID del ruolo univoco anziché il nome del ruolo. Di conseguenza, se un ruolo viene rinominato, è più probabile che gli script funzionino.

L'esempio seguente assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) all'utente *\@ contoso.com di patlong* nell'ambito del gruppo di risorse *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Aggiungere un'assegnazione di ruolo per un'applicazione in un ambito del gruppo di risorse

Assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) a un'applicazione con ID oggetto entità servizio 77777777-7777-7777-7777-777777777777 nell'ambito del gruppo di risorse *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Aggiungere un'assegnazione di ruolo per un utente in un ambito di sottoscrizione

Assegna il ruolo [Reader](built-in-roles.md#reader) all'utente *\@ example.com di annm* in un ambito di sottoscrizione.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Aggiunta dell'assegnazione di ruolo per un utente in un ambito del gruppo di gestione

Assegna il ruolo di [lettore fatturazione](built-in-roles.md#billing-reader) all'utente *Alain \@ example.com* in un ambito del gruppo di gestione.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

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

In controllo degli accessi in base al ruolo di Azure, rimuovere un'assegnazione di ruolo usando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Nell'esempio seguente viene rimossa l'assegnazione di ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) dall'utente *patlong \@ contoso.com* nel gruppo di risorse *Pharma-Sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Rimuove il ruolo [lettore](built-in-roles.md#reader) dal gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito della sottoscrizione.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Rimuove il ruolo [Lettura fatturazione](built-in-roles.md#billing-reader) dall'utente *Alain \@ example.com* nell'ambito del gruppo di gestione.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Se viene visualizzato il messaggio di errore: "le informazioni fornite non sono mappate a un'assegnazione di ruolo", assicurarsi di specificare anche `-Scope` i `-ResourceGroupName` parametri o. Per altre informazioni, vedere [risolvere i problemi relativi a RBAC di Azure](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo di Azure usando Azure PowerShell](role-assignments-list-powershell.md)
- [Esercitazione: Concedere a un gruppo l'accesso alle risorse di Azure con Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Gestire le risorse con Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)