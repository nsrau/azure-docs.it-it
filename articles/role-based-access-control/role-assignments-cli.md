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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3420374e90790bd1ffe4c845c19de1bfed317302
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173742"
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

## <a name="list-a-role-definition"></a>Elencare una definizione di ruolo

Per elencare una definizione di ruolo, usare [AZ Role definition list](/cli/azure/role/definition#az-role-definition-list):

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

Nell'esempio seguente vengono elencate solo le *azioni* e le *notacte* del ruolo *collaboratore* :

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

Nell'esempio seguente vengono elencate solo le azioni del ruolo *collaboratore macchina virtuale* :

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

Per impostazione predefinita, verranno visualizzate solo le assegnazioni dirette con ambito alla sottoscrizione. Per visualizzare le assegnazioni con ambito di risorsa o gruppo, `--all` utilizzare e per visualizzare le assegnazioni ereditate, utilizzare. `--include-inherited`

Nell'esempio seguente vengono elencate le assegnazioni di ruolo assegnate direttamente all'utente *contoso.com\@di patlong* :

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Elencare le assegnazioni di ruolo in un ambito del gruppo di risorse

Per elencare le assegnazioni di ruolo esistenti in un ambito del gruppo di risorse, usare [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Nell'esempio seguente vengono elencate le assegnazioni di ruolo per il gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
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

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Elencare le assegnazioni di ruolo in un ambito di sottoscrizione

Per elencare tutte le assegnazioni di ruolo nell'ambito di una sottoscrizione, usare [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Per ottenere l'ID sottoscrizione, è possibile trovarlo nel pannello **sottoscrizioni** nella portale di Azure oppure è possibile usare il comando [AZ account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Elencare le assegnazioni di ruolo in un ambito del gruppo di gestione

Per elencare tutte le assegnazioni di ruolo in un ambito del gruppo di gestione, usare [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Per ottenere l'ID del gruppo di gestione, è possibile trovarlo nel pannello **gruppi di gestione** della portale di Azure oppure è possibile usare il comando [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>Concedi accesso

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Creare un'assegnazione di ruolo per un utente nell'ambito di un gruppo di risorse

Per concedere l'accesso a un utente in un ambito del gruppo di risorse, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

L'esempio seguente assegna il ruolo *collaboratore macchina virtuale* all' *utente\@patlong contoso.com* nell'ambito del gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Creare un'assegnazione di ruolo usando l'ID del ruolo univoco

Ci sono un paio di volte in cui un nome di ruolo potrebbe cambiare, ad esempio:

- Si usa il proprio ruolo personalizzato e si decide di modificare il nome.
- Si sta usando un ruolo di anteprima con **(anteprima)** nel nome. Quando il ruolo viene rilasciato, il ruolo viene rinominato.

> [!IMPORTANT]
> Una versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Anche se un ruolo viene rinominato, l'ID del ruolo non cambia. Se si usano script o automazione per creare le assegnazioni di ruolo, è consigliabile usare l'ID del ruolo univoco anziché il nome del ruolo. Di conseguenza, se un ruolo viene rinominato, è più probabile che gli script funzionino.

Per creare un'assegnazione di ruolo usando l'ID del ruolo univoco anziché il nome del ruolo, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

L'esempio seguente assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) all'utente *contoso.com\@di patlong* nell'ambito del gruppo di risorse *Pharma-Sales* . Per ottenere l'ID del ruolo univoco, è possibile usare [AZ Role definition list](/cli/azure/role/definition#az-role-definition-list) o vedere [ruoli predefiniti per le risorse di Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Creare un'assegnazione di ruolo per un gruppo

Per concedere l'accesso a un gruppo, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per ottenere l'ID del gruppo, è possibile usare [az ad group list](/cli/azure/ad/group#az-ad-group-list) o [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Nell'esempio seguente viene assegnato il ruolo *lettore* al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito di sottoscrizione.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

L'esempio seguente assegna il ruolo *collaboratore macchina virtuale* al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito di risorse per una rete virtuale denominata *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Creare un'assegnazione di ruolo per un'applicazione in un ambito del gruppo di risorse

Per concedere l'accesso a un'applicazione, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per ottenere l'ID oggetto dell'applicazione, è possibile usare [az ad app list](/cli/azure/ad/app#az-ad-app-list) o [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Nell'esempio seguente il ruolo *collaboratore macchina virtuale* viene assegnato a un'applicazione con ID oggetto 44444444-4444-4444-4444-444444444444 nell'ambito del gruppo di risorse *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Creare un'assegnazione di ruolo per un utente in un ambito di sottoscrizione

Per concedere l'accesso a un utente nell'ambito di una sottoscrizione, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per ottenere l'ID sottoscrizione, è possibile trovarlo nel pannello **sottoscrizioni** nella portale di Azure oppure è possibile usare il comando [AZ account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Nell'esempio seguente il ruolo *Reader* viene assegnato all'utente *example.com di\@annm* in un ambito di sottoscrizione.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Creare un'assegnazione di ruolo per un utente in un ambito del gruppo di gestione

Per concedere l'accesso a un utente in un ambito del gruppo di gestione, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per ottenere l'ID del gruppo di gestione, è possibile trovarlo nel pannello **gruppi di gestione** della portale di Azure oppure è possibile usare il comando [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

L'esempio seguente assegna il ruolo di *lettore fatturazione* a all'utente *Alain\@example.com* in un ambito del gruppo di gestione.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>Creare un'assegnazione di ruolo per una nuova entità servizio

Se si crea una nuova entità servizio e si tenta immediatamente di assegnare un ruolo a tale entità servizio, l'assegnazione di ruolo può non riuscire in alcuni casi. Se ad esempio si utilizza uno script per creare una nuova identità gestita e quindi si tenta di assegnare un ruolo a tale entità servizio, l'assegnazione del ruolo potrebbe non riuscire. Il motivo di questo errore è probabilmente un ritardo di replica. L'entità servizio viene creata in un'area; Tuttavia, l'assegnazione di ruolo potrebbe verificarsi in un'area diversa che non ha ancora replicato l'entità servizio. Per risolvere questo scenario, è necessario specificare il tipo di entità durante la creazione dell'assegnazione di ruolo.

Per creare un'assegnazione di ruolo, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create), specificare `--assignee-object-id`un valore per e `--assignee-principal-type` quindi `ServicePrincipal`impostare su.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L'esempio seguente assegna il ruolo *collaboratore macchina virtuale* all'identità gestita del *test MSI* nell'ambito del gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>Rimozione accesso

Per rimuovere l'accesso nel controllo degli accessi in base al ruolo, è possibile rimuovere un'assegnazione di ruolo tramite [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Nell'esempio seguente viene rimossa l'assegnazione di ruolo *collaboratore macchina virtuale* dall'utente *patlong\@contoso.com* nel gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Nell'esempio seguente viene rimosso il ruolo *Reader* dal gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito della sottoscrizione. Per ottenere l'ID del gruppo, è possibile usare [az ad group list](/cli/azure/ad/group#az-ad-group-list) o [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Nell'esempio seguente viene rimosso il ruolo *Lettura fatturazione* dall' *utente\@Alain example.com* nell'ambito del gruppo di gestione. Per ottenere l'ID del gruppo di gestione, è possibile usare il comando [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare un ruolo personalizzato per le risorse di Azure tramite l'interfaccia della riga di comando di Azure](tutorial-custom-role-cli.md)
- [Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse](../azure-resource-manager/cli-azure-resource-manager.md)
