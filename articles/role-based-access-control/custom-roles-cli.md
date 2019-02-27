---
title: Creare ruoli personalizzati per le risorse di Azure tramite l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare ruoli personalizzati con il controllo degli accessi in base al ruolo per le risorse di Azure tramite l'interfaccia della riga di comando di Azure. Viene illustrato come elencare, creare, aggiornare ed eliminare ruoli personalizzati.
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b768f6e240c354369246a6d978ed3e8dd2f58f92
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338139"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Creare ruoli personalizzati per le risorse di Azure tramite l'interfaccia della riga di comando di Azure

Se i [ruoli predefiniti per le risorse di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Questo articolo descrive come creare e gestire ruoli personalizzati con l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per creare ruoli personalizzati è necessario:

- Avere le autorizzazioni per creare ruoli personalizzati, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator)
- Avere l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) installata in locale

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

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Per specificare il ruolo da eliminare, usare il nome o l'ID del ruolo. Per determinare l'ID ruolo, usare [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Nell'esempio seguente il ruolo personalizzato *Virtual Machine Operator* (Operatore macchina virtuale) viene rimosso.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: creare un ruolo personalizzato per le risorse di Azure tramite l'interfaccia della riga di comando di Azure](tutorial-custom-role-cli.md)
- [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
- [Operazioni di provider di risorse con Azure Resource Manager](resource-provider-operations.md)