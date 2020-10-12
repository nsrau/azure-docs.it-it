---
title: Elencare le definizioni dei ruoli di Azure-RBAC di Azure
description: Informazioni su come elencare i ruoli predefiniti e personalizzati di Azure usando portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9819b90ba390e8601cc33a17338ce9b16bf3b3cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84982493"
---
# <a name="list-azure-role-definitions"></a>Elencare le definizioni dei ruoli di Azure

Una definizione di ruolo è una raccolta di autorizzazioni che è possibile eseguire, ad esempio la lettura, la scrittura e l'eliminazione. generalmente chiamata ruolo. Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](overview.md) ha più [di 120 ruoli predefiniti](built-in-roles.md) oppure è possibile creare ruoli personalizzati. Questo articolo descrive come elencare i ruoli predefiniti e personalizzati che è possibile usare per concedere l'accesso alle risorse di Azure.

Per visualizzare l'elenco dei ruoli di amministratore per Azure Active Directory, vedere autorizzazioni per i ruoli di [amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-portal"></a>Portale di Azure

### <a name="list-all-roles"></a>Elencare tutti i ruoli

Attenersi alla seguente procedura per elencare tutti i ruoli nel portale di Azure.

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare un ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)** .

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

### <a name="list-a-role-definition"></a>Elencare una definizione di ruolo

Per elencare i dettagli di un ruolo specifico, usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-a-role-definition-in-json-format"></a>Elencare una definizione di ruolo in formato JSON

Per elencare un ruolo in formato JSON, usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-permissions-of-a-role-definition"></a>Elencare le autorizzazioni di una definizione di ruolo

Per elencare le autorizzazioni per un ruolo specifico, usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

Per elencare tutti i ruoli nell'interfaccia della riga di comando di Azure, usare [AZ Role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list
```

Nell'elenco seguente sono elencati il nome e la descrizione di tutte le definizioni di ruolo disponibili:

```azurecli
az role definition list --output json --query '[].{roleName:roleName, description:description}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role"
  },

  ...

]
```

Nell'esempio seguente vengono elencati tutti i ruoli predefiniti.

```azurecli
az role definition list --custom-role-only false --output json --query '[].{roleName:roleName, description:description, roleType:roleType}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role",
    "roleType": "BuiltInRole"
  },
  
  ...

]
```

### <a name="list-a-role-definition"></a>Elencare una definizione di ruolo

Per elencare i dettagli di un ruolo, usare [AZ Role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name {roleName}
```

Nell'elenco seguente è elencata la definizione del ruolo *Collaboratore*:

```azurecli
az role definition list --name "Contributor"
```

```json
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action",
          "Microsoft.Blueprint/blueprintAssignments/write",
          "Microsoft.Blueprint/blueprintAssignments/delete"
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

### <a name="list-permissions-of-a-role-definition"></a>Elencare le autorizzazioni di una definizione di ruolo

Nell'esempio seguente vengono elencate solo le *azioni* e le *notacte* del ruolo *collaboratore* .

```azurecli
az role definition list --name "Contributor" --output json --query '[].{actions:permissions[0].actions, notActions:permissions[0].notActions}'
```

```json
[
  {
    "actions": [
      "*"
    ],
    "notActions": [
      "Microsoft.Authorization/*/Delete",
      "Microsoft.Authorization/*/Write",
      "Microsoft.Authorization/elevateAccess/Action",
      "Microsoft.Blueprint/blueprintAssignments/write",
      "Microsoft.Blueprint/blueprintAssignments/delete"
    ]
  }
]
```

Nell'esempio seguente vengono elencate solo le azioni del ruolo *collaboratore macchina virtuale* .

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/locations/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/virtualMachineScaleSets/*",
    "Microsoft.Compute/disks/write",
    "Microsoft.Compute/disks/read",
    "Microsoft.Compute/disks/delete",
    "Microsoft.DevTestLab/schedules/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
    "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

    ...

    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Storage/storageAccounts/read",
    "Microsoft.Support/*"
  ]
]
```

## <a name="rest-api"></a>API REST

### <a name="list-role-definitions"></a>Elencare le definizioni di ruolo

Per elencare le definizioni dei ruoli, usare l'API REST per l' [elenco delle definizioni di ruolo](/rest/api/authorization/roledefinitions/list) . Per affinare i risultati, specificare un ambito e un filtro facoltativo.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. All'interno dell'URI, sostituire *{scope}* con l'ambito per il quale si desidera visualizzare l'elenco delle definizioni di ruolo.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Risorsa |

    Nell'esempio precedente Microsoft. Web è un provider di risorse che fa riferimento a un'istanza del servizio app. Analogamente, è possibile usare qualsiasi altro provider di risorse e specificare l'ambito. Per altre informazioni, vedere [provider di risorse di Azure e tipi](../azure-resource-manager/management/resource-providers-and-types.md) e [le operazioni supportate Azure Resource Manager provider di risorse](resource-provider-operations.md).  
     
1. Sostituire *{Filter}* con la condizione che si vuole applicare per filtrare l'elenco di definizioni di ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtra | Descrizione |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Elenca le definizioni dei ruoli per l'ambito specificato e per qualsiasi ambito. |
    > | `$filter=type+eq+'{type}'` | Elenca le definizioni dei ruoli del tipo specificato. Il tipo di ruolo può essere `CustomRole` o `BuiltInRole` . |

La richiesta seguente elenca le definizioni dei ruoli personalizzati nell'ambito della sottoscrizione:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=type+eq+'CustomRole'
```

Il testo seguente è un esempio di output:

```json
{
    "value": [
        {
            "properties": {
                "roleName": "Billing Reader Plus",
                "type": "CustomRole",
                "description": "Read billing data and download invoices",
                "assignableScopes": [
                    "/subscriptions/{subscriptionId1}"
                ],
                "permissions": [
                    {
                        "actions": [
                            "Microsoft.Authorization/*/read",
                            "Microsoft.Billing/*/read",
                            "Microsoft.Commerce/*/read",
                            "Microsoft.Consumption/*/read",
                            "Microsoft.Management/managementGroups/read",
                            "Microsoft.CostManagement/*/read",
                            "Microsoft.Billing/invoices/download/action",
                            "Microsoft.CostManagement/exports/*"
                        ],
                        "notActions": [
                            "Microsoft.CostManagement/exports/delete"
                        ]
                    }
                ],
                "createdOn": "2020-02-21T04:49:13.7679452Z",
                "updatedOn": "2020-02-21T04:49:13.7679452Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId1}",
            "type": "Microsoft.Authorization/roleDefinitions",
            "name": "{roleDefinitionId1}"
        }
    ]
}
```

### <a name="list-a-role-definition"></a>Elencare una definizione di ruolo

Per elencare i dettagli di un ruolo specifico, usare l'API REST per le [definizioni di ruolo-ottenere](/rest/api/authorization/roledefinitions/get) o le [definizioni dei ruoli-Get by ID](/rest/api/authorization/roledefinitions/getbyid) .

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Per una definizione di ruolo a livello di directory, è possibile usare questa richiesta:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Nell'URI sostituire *{scope}* con l'ambito per il quale si desidera elencare la definizione di ruolo.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Risorsa |
     
1. Sostituire *{roleDefinitionId}* con l'identificatore della definizione del ruolo.

Nella richiesta seguente è elencata la definizione del ruolo [Reader](built-in-roles.md#reader) :

```http
GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7?api-version=2015-07-01
```

Il testo seguente è un esempio di output:

```json
{
    "properties": {
        "roleName": "Reader",
        "type": "BuiltInRole",
        "description": "Lets you view everything, but not make any changes.",
        "assignableScopes": [
            "/"
        ],
        "permissions": [
            {
                "actions": [
                    "*/read"
                ],
                "notActions": []
            }
        ],
        "createdOn": "2015-02-02T21:55:09.8806423Z",
        "updatedOn": "2019-02-05T21:24:35.7424745Z",
        "createdBy": null,
        "updatedBy": null
    },
    "id": "/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Ruoli predefiniti di Azure](built-in-roles.md)
- [Ruoli personalizzati di Azure](custom-roles.md)
- [Elencare le assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-list-portal.md)
- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-portal.md)
