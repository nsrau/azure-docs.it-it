---
title: Creare o aggiornare i ruoli personalizzati di Azure usando l'interfaccia della riga di comando di Azure-RBAC
description: Informazioni su come elencare, creare, aggiornare o eliminare i ruoli personalizzati di Azure usando l'interfaccia della riga di comando di Azure e il controllo degli accessi in base al ruolo di Azure.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cac0116cf7a068e63cb54698f7273b8c063ff854
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734842"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Creare o aggiornare ruoli personalizzati di Azure tramite l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> L'aggiunta di un gruppo `AssignableScopes` di gestione a è attualmente in fase di anteprima.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se i [ruoli predefiniti di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Questo articolo descrive come elencare, creare, aggiornare o eliminare i ruoli personalizzati usando l'interfaccia della riga di comando di Azure.

Per un'esercitazione dettagliata su come creare un ruolo personalizzato, vedere [esercitazione: creare un ruolo personalizzato di Azure con l'interfaccia](tutorial-custom-role-cli.md)della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per creare ruoli personalizzati è necessario:

- Avere le autorizzazioni per creare ruoli personalizzati, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) o [interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Per elencare i ruoli personalizzati disponibili per l'assegnazione, usare [az role definition list](/cli/azure/role/definition#az-role-definition-list). Gli esempi seguenti elencano tutti i ruoli personalizzati nella sottoscrizione corrente.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Elencare una definizione di ruolo personalizzata

Per elencare una definizione di ruolo personalizzata, usare [AZ Role definition list](/cli/azure/role/definition#az-role-definition-list). Si tratta dello stesso comando che si utilizzerebbe per un ruolo predefinito.

```azurecli
az role definition list --name <role_name>
```

Nell'esempio seguente viene elencata la definizione del ruolo *operatore macchina virtuale* :

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Nell'esempio seguente vengono elencate solo le azioni del ruolo *operatore macchina virtuale* :

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Per creare un ruolo personalizzato, usare [az role definition create](/cli/azure/role/definition#az-role-definition-create). La definizione di ruolo può essere una descrizione JSON oppure il percorso di un file contenente una descrizione JSON.

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
    "Microsoft.ResourceHealth/availabilityStatuses/read",
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

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Per aggiornare un ruolo personalizzato, usare prima [az role definition list](/cli/azure/role/definition#az-role-definition-list) per recuperare la definizione di ruolo. Successivamente, apportare le modifiche desiderate alla definizione del ruolo. Usare infine [az role definition update](/cli/azure/role/definition#az-role-definition-update) per salvare la definizione di ruolo aggiornata.

```azurecli
az role definition update --role-definition <role_definition>
```

Nell'esempio seguente viene aggiunta l'operazione *Microsoft. Insights/diagnosticSettings/* a `Actions` e viene aggiunto un gruppo di `AssignableScopes` gestione a per il ruolo personalizzato *operatore macchina virtuale* . L'aggiunta di un gruppo `AssignableScopes` di gestione a è attualmente in fase di anteprima.

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
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Per specificare il ruolo da eliminare, usare il nome o l'ID del ruolo. Per determinare l'ID ruolo, usare [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Nell'esempio seguente viene eliminato il ruolo personalizzato *operatore macchina virtuale* .

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: creare un ruolo personalizzato di Azure con l'interfaccia della riga di comando di Azure](tutorial-custom-role-cli.md)
- [Ruoli personalizzati di Azure](custom-roles.md)
- [Operazioni del provider di risorse Azure Resource Manager](resource-provider-operations.md)