---
title: Creare ruoli personalizzati per le risorse di Azure tramite Azure PowerShell | Microsoft Docs
description: Informazioni su come creare ruoli personalizzati con il controllo degli accessi in base al ruolo per le risorse di Azure tramite Azure PowerShell. Viene illustrato come elencare, creare, aggiornare ed eliminare ruoli personalizzati.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 92c061a7f854b46ab5aee07aa5e648ace8f9ae52
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343843"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-powershell"></a>Creare ruoli personalizzati per le risorse di Azure tramite Azure PowerShell

Se i [ruoli predefiniti per le risorse di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Questo articolo descrive come creare e gestire ruoli personalizzati con Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per creare ruoli personalizzati è necessario:

- Avere le autorizzazioni per creare ruoli personalizzati, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator)
- Avere [Azure PowerShell](/powershell/azure/install-az-ps) installato in locale

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Per elencare i ruoli disponibili per l'assegnazione a un ambito, usare il comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). L'esempio seguente elenca tutti i ruoli disponibili per l'assegnazione nella sottoscrizione selezionata.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

L'esempio seguente elenca i soli ruoli personalizzati disponibili per l'assegnazione nella sottoscrizione selezionata.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Se la sottoscrizione selezionata non è nel `AssignableScopes` del ruolo, il ruolo personalizzato non sarà elencato.

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Per creare un ruolo personalizzato usare il comando [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition). Esistono due metodi per strutturare il ruolo: usare l’oggetto `PSRoleDefinition` o un modello JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Ottenere operazioni per un provider di risorse

Quando si creano ruoli personalizzati è importante conoscere tutte le operazioni possibili dei provider di risorse.
Per ottenere queste informazioni è possibile visualizzare l'elenco delle [operazioni dei provider di risorse](resource-provider-operations.md) o usare il comando [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation).
Ad esempio, per controllare tutte le operazioni disponibili per le macchine virtuali usare questo comando:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Creare un ruolo personalizzato con l'oggetto PSRoleDefinition

Quando si crea un ruolo personalizzato con PowerShell è possibile usare come punto di partenza uno dei [ruoli predefiniti](built-in-roles.md) o iniziare da zero. Nel primo esempio riportato in questa sezione si inizia con un ruolo predefinito e quindi lo si personalizza con più autorizzazioni. Modificare gli attributi e aggiungere gli attributi `Actions`, `NotActions` o `AssignableScopes` desiderati e quindi salvare le modifiche come nuovo ruolo.

L'esempio seguente inizia con il ruolo predefinito [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) per creare un ruolo personalizzato denominato *Virtual Machine Operator*. Il nuovo ruolo concede l'accesso a tutte le operazioni di lettura dei provider di risorse *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede l'accesso per avviare, riavviare e monitorare le macchine virtuali. Il ruolo personalizzato può essere usato in due sottoscrizioni.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

L'esempio illustra un altro modo di creare il ruolo personalizzato *Virtual Machine Operator*. Si inizia creando un nuovo oggetto `PSRoleDefinition`. Le operazioni di azione vengono specificate nella variabile `perms` e impostate nella proprietà `Actions`. La proprietà `NotActions` viene impostata tramite la lettura di `NotActions` dal ruolo predefinito [Collaboratore Macchina Virtuale](built-in-roles.md#virtual-machine-contributor). Poiché [Collaboratore Macchina Virtuale](built-in-roles.md#virtual-machine-contributor) non presenta alcun `NotActions`, questa riga non è obbligatoria, ma illustra come è possibile recuperare informazioni da un altro ruolo.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Creare un ruolo personalizzato con il modello JSON

È possibile usare un modello JSON come definizione di origine per il ruolo personalizzato. Nell'esempio seguente viene creato un ruolo personalizzato che consente di accedere in lettura all'archiviazione e alle risorse di calcolo, di accedere come supporto e di aggiungere tale ruolo a due sottoscrizioni. Creare un nuovo file `C:\CustomRoles\customrole1.json` con il seguente esempio. L'ID deve essere impostato su `null` all'inizio della creazione del ruolo quando viene generato in automatico un nuovo ID. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Per aggiungere il ruolo alle sottoscrizioni, eseguire il comando PowerShell seguente:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Così come per la creazione di un ruolo personalizzato, è possibile modificare un ruolo personalizzato esistente usando l'oggetto `PSRoleDefinition` o un modello JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aggiornare un ruolo personalizzato con l'oggetto PSRoleDefinition

Per modificare un ruolo personalizzato, usare innanzitutto il comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) per recuperare la definizione di ruolo. Successivamente, apportare le modifiche desiderate alla definizione del ruolo. Infine, usare il comando [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) per salvare la definizione di ruolo modificata.

Nell'esempio seguente viene aggiunta l'operazione `Microsoft.Insights/diagnosticSettings/*` al ruolo personalizzato *Operatore macchina virtuale* .

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

Nell'esempio seguente viene aggiunta una sottoscrizione di Azure agli ambiti assegnabili del ruolo personalizzato *Virtual Machine Operator* .

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Aggiornare un ruolo personalizzato con un modello JSON

Usando il modello JSON precedente, è possibile modificare un ruolo personalizzato esistente per aggiungere o rimuovere le azioni. Aggiornare il modello JSON e aggiungere l'azione di lettura per la rete, come illustrato nell'esempio seguente. Le definizioni riportate nel modello non vengono applicate in modo cumulativo a una definizione esistente, vale a dire che il ruolo verrà visualizzato esattamente come specificato nel modello. È anche necessario aggiornare il campo ID con l'ID del ruolo. Se non si è certi di quale sia questo valore, è possibile usare il cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) per ottenere queste informazioni.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Per aggiornare il ruolo esistente, eseguire il comando PowerShell seguente:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato usare il comando [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition).

Nell'esempio seguente viene rimosso il ruolo personalizzato *Operatore macchina virtuale* .

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: creare un ruolo personalizzato per le risorse di Azure tramite Azure PowerShell](tutorial-custom-role-powershell.md)
- [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
- [Operazioni di provider di risorse con Azure Resource Manager](resource-provider-operations.md)
