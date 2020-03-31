---
title: Elencare le definizioni di ruolo in Controllo degli accessi in base al ruolo di Azure usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Documenti Microsoft
description: Informazioni su come elencare i ruoli predefiniti e personalizzati in Controllo degli accessi in base al ruolo di Azure usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062996"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Definizioni di ruolo di elenco in Controllo degli accessi in base al ruolo di AzureList role definitions

Una definizione di ruolo è una raccolta di autorizzazioni che possono essere eseguite, ad esempio lettura, scrittura ed eliminazione. generalmente chiamata ruolo. Il controllo degli [accessi in base al ruolo](overview.md) di Azure include oltre 120 ruoli [predefiniti](built-in-roles.md) oppure è possibile creare ruoli personalizzati. Questo articolo descrive come elencare i ruoli predefiniti e personalizzati che è possibile usare per concedere l'accesso alle risorse di Azure.This article describes how to list the built-in and custom roles that you can use to grant access to Azure resources.

Per visualizzare l'elenco dei ruoli di amministratore per Azure Active Directory, vedere Autorizzazioni dei ruoli di [amministratore in Azure Active Directory.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="azure-portal"></a>Portale di Azure

### <a name="list-all-roles"></a>Elencare tutti i ruoli

Seguire questi passaggi per elencare tutti i ruoli nel portale di Azure.Follow these steps to list all roles in the Azure portal.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi selezionare un ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic su **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

   È possibile visualizzare il numero di utenti e gruppi assegnati a ogni ruolo nell'ambito corrente.

   ![Elenco dei ruoli](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Elencare tutti i ruoli

Per elencare tutti i ruoli in Azure PowerShell, usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Elencare una definizione di ruoloList a role definition

Per elencare i dettagli di un ruolo specifico, utilizzare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Elencare una definizione di ruolo in formato JSONList a role definition in JSON format

Per elencare un ruolo in formato JSON, utilizzare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Elencare le autorizzazioni di una definizione di ruoloList permissions of a role definition

Per elencare le autorizzazioni per un ruolo specifico, utilizzare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

### <a name="list-all-roles"></a>Elencare tutti i ruoli

Per elencare tutti i ruoli nell'interfaccia della riga di comando di Azure, usare l'elenco di [definizione](/cli/azure/role/definition#az-role-definition-list)dei ruoli az .

```azurecli
az role definition list
```

Nell'elenco seguente sono elencati il nome e la descrizione di tutte le definizioni di ruolo disponibili:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Nell'esempio seguente vengono elencati tutti i ruoli predefiniti.

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-a-role-definition"></a>Elencare una definizione di ruoloList a role definition

Per elencare i dettagli di un ruolo, utilizzare [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name <role_name>
```

Nell'elenco seguente è elencata la definizione del ruolo *Collaboratore*:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Elencare le autorizzazioni di una definizione di ruoloList permissions of a role definition

Nell'esempio seguente vengono elencate solo le *azioni* e *notAzioni* del ruolo *Collaboratore.*

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

Nell'esempio seguente sono elencate solo le azioni del ruolo *Collaboratore macchina virtuale.*

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="rest-api"></a>API REST

### <a name="list-role-definitions"></a>Elencare le definizioni di ruolo

Per elencare le definizioni di ruolo, usare l'API REST [Definizioni ruolo - Elenco.To](/rest/api/authorization/roledefinitions/list) list role definitions, use the Role Definitions - List REST API. Per affinare i risultati, specificare un ambito e un filtro facoltativo.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. All'interno dell'URI, sostituire *l'ambito* con l'ambito per il quale si desidera elencare le definizioni di ruolo.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Risorsa |

    Nell'esempio precedente, microsoft.web è un provider di risorse che fa riferimento a un'istanza del servizio app. Analogamente, è possibile utilizzare qualsiasi altro provider di risorse e specificare l'ambito. Per altre informazioni, vedere [Tipi e provider di risorse](../azure-resource-manager/management/resource-providers-and-types.md) di Azure e Operazioni del provider di risorse di Azure Resource [Manager](resource-provider-operations.md)supportate.  
     
1. Sostituire *il filtro* con la condizione che si desidera applicare per filtrare l'elenco delle definizioni di ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Elenca le definizioni di ruolo per l'ambito specificato e gli eventuali ambiti secondari. |
    > | `$filter=type+eq+'{type}'` | Elenca le definizioni di ruolo del tipo specificato. Il tipo di `CustomRole` `BuiltInRole`ruolo può essere o . |

### <a name="list-a-role-definition"></a>Elencare una definizione di ruoloList a role definition

Per elencare i dettagli di un ruolo specifico, usare l'API REST [Definizioni di](/rest/api/authorization/roledefinitions/get) ruolo - Get o [Role - Get By Id](/rest/api/authorization/roledefinitions/getbyid) REST.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Per una definizione di ruolo a livello di directory, è possibile utilizzare questa richiesta:For a directory-level role definition, you can use this request:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI, sostituire *l'ambito* con l'ambito per il quale si desidera elencare la definizione del ruolo.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Risorsa |
     
1. Sostituire *{roleDefinitionId}* con l'identificatore della definizione del ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Ruoli predefiniti per le risorse di Azure](built-in-roles.md)
- [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
- [Elencare le assegnazioni di ruolo usando controllo degli accessi in base al ruolo di Azure e il portale di AzureList role assignments using Azure RBAC and the Azure portal](role-assignments-list-portal.md)
- [Aggiungere o rimuovere assegnazioni di ruolo tramite controllo degli accessi in base al ruolo di Azure e il portale di AzureAdd or remove role assignments using Azure RBAC and the Azure portal](role-assignments-portal.md)
