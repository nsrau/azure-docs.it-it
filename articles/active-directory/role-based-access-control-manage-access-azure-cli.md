---
title: Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: "Informazioni su come gestire il controllo degli accessi in base al ruolo (RBAC) con l'interfaccia della riga di comando di Azure, ad esempio ottenere un elenco dei ruoli e delle relative azioni, nonché assegnare i ruoli nell'ambito della sottoscrizione e dell'applicazione."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5c099a7fd8848c2934603ec9b2db8947885226f9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)


Con la funzionalità Controllo degli accessi in base al ruolo è possibile definire l'accesso per utenti, gruppi ed entità servizio assegnando loro dei ruoli in un determinato ambito. Questo articolo descrive come gestire l'accesso usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Se si intende usare l'interfaccia della riga di comando di Azure per gestire il controllo degli accessi in base al ruolo, sono necessari i prerequisiti seguenti:

* [Interfaccia della riga di comando di Azure 2.0](/cli/azure/overview). È possibile usarla nel browser con [Azure Cloud Shell](../cloud-shell/overview.md) oppure [installarla](/cli/azure/install-azure-cli) in macOS, Linux e Windows ed eseguirla dalla riga di comando.

## <a name="list-roles"></a>Elencare i ruoli

### <a name="list-role-definitions"></a>Elencare le definizioni di ruolo

Per elencare tutte le definizioni di ruolo disponibili, usare [az role definition list](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list
```

Nell'elenco seguente sono elencati il nome e la descrizione di tutte le definizioni di ruolo disponibili:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
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

Nell'elenco seguente sono elencate tutte le definizioni di ruolo predefinite:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description, "type":.properties.type}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "type": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>Elencare le azioni di una definizione di ruolo

Per elencare le azioni di una definizione di ruolo, usare [az role definition list](/cli/azure/role/definition#az_role_definition_list):

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
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "properties": {
      "additionalProperties": {
        "createdBy": null,
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedBy": null,
        "updatedOn": "2016-12-14T02:04:45.1393855Z"
      },
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything except access to resources.",
      "permissions": [
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
      ],
      "roleName": "Contributor",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Nell'elenco seguente sono elencate le *azioni consentite* e *non consentite* del ruolo *Collaboratore*:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.properties.permissions[0].actions, "notActions":.properties.permissions[0].notActions}'
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

Nell'elenco seguente sono elencate le azioni consentite del ruolo *Collaboratore Macchina virtuale*:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .properties.permissions[0].actions'
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

## <a name="list-access"></a>Elencare l'accesso

### <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente

Per elencare le assegnazioni di ruolo per un utente specifico, usare [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --assignee <assignee>
```

Per impostazione predefinita, verranno visualizzate solo le assegnazioni che rientrano nell'ambito della sottoscrizione. Per visualizzare le assegnazioni che rientrano nell'ambito della risorsa o del gruppo, usare `--all`.

Nell'esempio seguente sono elencate le assegnazioni di ruolo che vengono assegnate direttamente all'utente *patlong@contoso.com*:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.properties.principalName, "roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Elencare le assegnazioni di ruolo per un gruppo di risorse

Per elencare le assegnazioni di ruolo esistenti per un gruppo di risorse, usare [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Nell'esempio seguente sono elencate le assegnazioni di ruolo per il gruppo di risorse *pharma-sales-projectforecast*:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="assign-access"></a>Assegnare l'accesso

### <a name="assign-a-role-to-a-user"></a>Assegnare un ruolo a un utente

Per assegnare un ruolo a un utente nell'ambito di un gruppo di risorse, usare [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

Nell'esempio seguente viene assegnato il ruolo *Collaboratore Macchina virtuale* all'utente *patlong@contoso.com* nell'ambito del gruppo di risorse *pharma-sales-projectforecast*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="assign-a-role-to-a-group"></a>Assegnare un ruolo a un gruppo

Per assegnare un ruolo a un gruppo, usare [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Nell'esempio seguente viene assegnato il ruolo *Lettore* al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 nell'ambito della sottoscrizione. Per ottenere l'ID del gruppo, è possibile usare [az ad group list](/cli/azure/ad/group#az_ad_group_list) o [az ad group show](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

Nell'esempio seguente viene assegnato il ruolo *Collaboratore Macchina virtuale* al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito di risorse per una rete virtuale denominata *pharma-sales-project-network*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="assign-a-role-to-an-application"></a>Assegnare un ruolo a un'applicazione

Per assegnare un ruolo a un'applicazione, usare [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Nell'esempio seguente viene assegnato il ruolo *Collaboratore Macchina virtuale* a un'applicazione con ID oggetto 44444444-4444-4444-4444-444444444444 nell'ambito del gruppo di risorse *pharma-sales-projectforecast*. Per ottenere l'ID oggetto dell'applicazione, è possibile usare [az ad app list](/cli/azure/ad/app#az_ad_app_list) o [az ad app show](/cli/azure/ad/app#az_ad_app_show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Rimuovere l'accesso

### <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Per rimuovere un'assegnazione di ruolo, usare [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

Nell'esempio seguente l'assegnazione di ruolo *Collaboratore Macchina virtuale* viene rimossa dall'utente *patlong@contoso.com* nel gruppo di risorse *pharma-sales-projectforecast*:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

Nell'esempio seguente il ruolo *Lettore* viene rimosso dal gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 nell'ambito della sottoscrizione. Per ottenere l'ID del gruppo, è possibile usare [az ad group list](/cli/azure/ad/group#az_ad_group_list) o [az ad group show](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Ruoli personalizzati

### <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Per elencare i ruoli disponibili per l'assegnazione a un ambito, usare [az role definition list](/cli/azure/role/definition#az_role_definition_list).

Con entrambi gli esempi vengono elencati tutti i ruoli personalizzati nella sottoscrizione corrente:

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.properties.roleName, "type":.properties.type}'
```

```azurecli
az role definition list --output json | jq '.[] | if .properties.type == "CustomRole" then {"roleName":.properties.roleName, "type":.properties.type} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Per creare un ruolo personalizzato, usare [az role definition create](/cli/azure/role/definition#az_role_definition_create). La definizione di ruolo può essere una descrizione JSON oppure il percorso di un file contenente una descrizione JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

Nell'esempio seguente viene creato il ruolo personalizzato denominato *Virtual Machine Operator* (Operatore macchina virtuale). Con questo ruolo personalizzato viene assegnato l'accesso a tutte le operazioni di lettura dei provider di risorse *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network*, nonché l'accesso per avviare, riavviare ed eseguire il monitoraggio delle macchine virtuali. Questo ruolo personalizzato può essere usato in due sottoscrizioni. In questo esempio viene usato un file JSON come input.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Per aggiornare un ruolo personalizzato, usare prima [az role definition list](/cli/azure/role/definition#az_role_definition_list) per recuperare la definizione di ruolo. Successivamente, apportare le modifiche desiderate alla definizione del ruolo. Usare infine [az role definition update](/cli/azure/role/definition#az_role_definition_update) per salvare la definizione di ruolo aggiornata.

```azurecli
az role definition update --role-definition <role_definition>
```

Nell'esempio seguente l'operazione *Microsoft.Insights/diagnosticSettings/* viene aggiunta ad *Azioni* del ruolo personalizzato *Virtual Machine Operator* (Operatore macchina virtuale).

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare [az role definition delete](/cli/azure/role/definition#az_role_definition_delete). Per specificare il ruolo da eliminare, usare il nome o l'ID del ruolo. Per determinare l'ID ruolo, usare [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Nell'esempio seguente il ruolo personalizzato *Virtual Machine Operator* (Operatore macchina virtuale) viene rimosso:

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

