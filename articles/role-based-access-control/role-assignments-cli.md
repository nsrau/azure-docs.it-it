---
title: Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come gestire l'accesso alle risorse di Azure per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e l'interfaccia della riga di comando di Azure. Vengono presentate anche la visualizzazione, la concessione e la rimozione dell'accesso.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1cc3d3eca4063a8120851a9d3de1a85292eacb11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344564"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e l'interfaccia della riga di comando di Azure

Il [controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse di Azure. Questo articolo descrive come gestire l'accesso per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per gestire l'accesso, è necessario uno degli elementi seguenti:

* [Bash in Azure Cloud Shell](/azure/cloud-shell/overview)
* [Interfaccia della riga di comando di Azure](/cli/azure)

## <a name="list-roles"></a>Elenco dei ruoli

Per elencare tutte le definizioni di ruolo disponibili, usare [az role definition list](/cli/azure/role/definition#az-role-definition-list):

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

Nell'elenco seguente sono elencate tutte le definizioni di ruolo predefinite:

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

## <a name="list-a-role-definition"></a>Elenco di una definizione di ruolo

Per elencare una definizione di ruolo, usare [elenco di definizioni di ruolo az](/cli/azure/role/definition#az-role-definition-list):

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

### <a name="list-actions-of-a-role"></a>Elencare le azioni di un ruolo

Nell'esempio seguente vengono elencate solo le *azioni* e *notActions* del *collaboratore* ruolo:

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

Nell'esempio seguente vengono elencate solo le azioni del *collaboratore macchina virtuale* ruolo:

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

## <a name="list-access"></a>Elencare l'accesso

Per visualizzare le informazioni sull'accesso nel controllo degli accessi in base al ruolo, elencare le assegnazioni di ruolo.

### <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente

Per elencare le assegnazioni di ruolo per un utente specifico, usare [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Per impostazione predefinita, verranno visualizzate solo le assegnazioni che rientrano nell'ambito della sottoscrizione. Per visualizzare le assegnazioni che rientrano nell'ambito della risorsa o del gruppo, usare `--all`.

L'esempio seguente elenca le assegnazioni di ruolo che vengono assegnate direttamente al *patlong\@contoso.com* utente:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Elencare le assegnazioni di ruolo per un gruppo di risorse

Per elencare le assegnazioni di ruolo esistenti per un gruppo di risorse, usare [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

L'esempio seguente elenca le assegnazioni di ruolo per il *pharma-sales* gruppo di risorse:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>Concedere l'accesso

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo.

### <a name="create-a-role-assignment-for-a-user"></a>Creare un'assegnazione di ruolo per un utente

Per creare un'assegnazione di ruolo per un utente nell'ambito di un gruppo di risorse, usare [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

L'esempio seguente assegna il *collaboratore macchina virtuale* ruolo *patlong\@contoso.com* utente nel *pharma-sales* ambito di gruppo di risorse:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Creare un'assegnazione di ruolo usando l'ID univoco del ruolo

Esistono un paio di volte quando un nome di ruolo potrebbe cambiare, ad esempio:

- Si usa il proprio ruolo personalizzato e si decide di modificare il nome.
- Si usa un ruolo di anteprima avente **(anteprima)** nel nome. Quando viene rilasciato il ruolo, il ruolo viene rinominato.

> [!IMPORTANT]
> Una versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Anche se viene rinominato un ruolo, l'ID del ruolo rimane invariato. Se si utilizzano script o automazione per creare assegnazioni di ruolo, è consigliabile usare l'ID univoco del ruolo anziché il nome del ruolo. Pertanto, se viene rinominato un ruolo, gli script sono più possibilità di lavorare.

Per creare un'assegnazione di ruolo usando l'ID univoco del ruolo anziché il nome del ruolo, usare [assegnazione di ruolo az creare](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

L'esempio seguente assegna il [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) ruolo *patlong\@contoso.com* utente nel *pharma-sales* ambito di gruppo di risorse. Per ottenere l'ID univoco del ruolo, è possibile usare [elenco di definizioni di ruolo az](/cli/azure/role/definition#az-role-definition-list) oppure vedere [ruoli predefiniti per le risorse di Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Creare un'assegnazione di ruolo per un gruppo

Per creare un'assegnazione di ruolo per un gruppo, usare [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Nell'esempio seguente viene assegnato il ruolo *Lettore* al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 nell'ambito della sottoscrizione. Per ottenere l'ID del gruppo, è possibile usare [az ad group list](/cli/azure/ad/group#az-ad-group-list) o [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

Nell'esempio seguente viene assegnato il ruolo *Collaboratore Macchina virtuale* al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito di risorse per una rete virtuale denominata *pharma-sales-project-network*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Creare un'assegnazione di ruolo per un'applicazione

Per creare un'assegnazione di ruolo per un'applicazione, usare [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L'esempio seguente assegna il *collaboratore macchina virtuale* ruolo a un'applicazione con ID oggetto 44444444-4444-4444-4444-444444444444 le *pharma-sales* ambito di gruppo di risorse. Per ottenere l'ID oggetto dell'applicazione, è possibile usare [az ad app list](/cli/azure/ad/app#az-ad-app-list) o [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>Rimuovere un accesso

Per rimuovere l'accesso nel controllo degli accessi in base al ruolo, è possibile rimuovere un'assegnazione di ruolo tramite [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

L'esempio seguente rimuove il *collaboratore macchina virtuale* assegnazione di ruolo dal *patlong\@contoso.com* utente sulla *pharma-sales* gruppo di risorse:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Nell'esempio seguente il ruolo *Lettore* viene rimosso dal gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 nell'ambito della sottoscrizione. Per ottenere l'ID del gruppo, è possibile usare [az ad group list](/cli/azure/ad/group#az-ad-group-list) o [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare un ruolo personalizzato per le risorse di Azure tramite l'interfaccia della riga di comando di Azure](tutorial-custom-role-cli.md)
- [Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse](../azure-resource-manager/cli-azure-resource-manager.md)
