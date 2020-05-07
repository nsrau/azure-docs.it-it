---
title: Elencare le assegnazioni di ruolo di Azure tramite l'interfaccia della riga di comando di Azure
description: Informazioni su come determinare le risorse a cui utenti, gruppi, entità servizio o identità gestite possono accedere usando l'interfaccia della riga di comando di Azure e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f4b635d6867c36b8b0f385320e3720bea41b54d1
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735743"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Elencare le assegnazioni di ruolo di Azure tramite l'interfaccia della riga

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Questo articolo descrive come elencare le assegnazioni di ruolo usando l'interfaccia della riga di comando di Azure.

> [!NOTE]
> Se l'organizzazione dispone di funzioni di gestione esternalizzate a un provider di servizi che usa la [gestione delle risorse delegate di Azure](../lighthouse/concepts/azure-delegated-resource-management.md), le assegnazioni di ruolo autorizzate dal provider di servizi non verranno visualizzate qui.

## <a name="prerequisites"></a>Prerequisiti

- [Bash in Azure cloud Shell o nell'interfaccia della riga di comando di](/azure/cloud-shell/overview) [Azure](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente

Per elencare le assegnazioni di ruolo per un utente specifico, usare [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Per impostazione predefinita, verranno visualizzate solo le assegnazioni di ruolo per la sottoscrizione corrente. Per visualizzare le assegnazioni di ruolo per la sottoscrizione corrente e di seguito `--all` , aggiungere il parametro. Per visualizzare le assegnazioni di ruolo ereditate `--include-inherited` , aggiungere il parametro.

Nell'esempio seguente vengono elencate le assegnazioni di ruolo assegnate direttamente all'utente *contoso.com\@di patlong* :

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

## <a name="list-role-assignments-for-a-resource-group"></a>Elencare le assegnazioni di ruolo per un gruppo di risorse

Per elencare le assegnazioni di ruolo esistenti in un ambito del gruppo di risorse, usare [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

Nell'esempio seguente vengono elencate le assegnazioni di ruolo per il gruppo di risorse *Pharma-Sales* :

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

## <a name="list-role-assignments-for-a-subscription"></a>Elencare i ruoli assegnati a una sottoscrizione

Per elencare tutte le assegnazioni di ruolo nell'ambito di una sottoscrizione, usare [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Per ottenere l'ID sottoscrizione, è possibile trovarlo nel pannello **sottoscrizioni** nella portale di Azure oppure è possibile usare il comando [AZ account list](/cli/azure/account#az-account-list).

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Esempio:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Elencare le assegnazioni di ruolo per un gruppo di gestione

Per elencare tutte le assegnazioni di ruolo in un ambito del gruppo di gestione, usare [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Per ottenere l'ID del gruppo di gestione, è possibile trovarlo nel pannello **gruppi di gestione** della portale di Azure oppure è possibile usare il comando [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Esempio:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Elencare le assegnazioni di ruolo per un'identità gestita

1. Ottenere l'ID oggetto dell'identità gestita assegnata dal sistema o dall'utente.

    Per ottenere l'ID oggetto di un'identità gestita assegnata dall'utente, è possibile usare [AZ ad SP list](/cli/azure/ad/sp#az-ad-sp-list) o [AZ Identity list](/cli/azure/identity#az-identity-list).

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Per ottenere l'ID oggetto di un'identità gestita assegnata dal sistema, è possibile usare [AZ ad SP list](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Per elencare le assegnazioni di ruolo, usare [AZ Role Assignment list](/cli/azure/role/assignment#az-role-assignment-list).

    Per impostazione predefinita, verranno visualizzate solo le assegnazioni di ruolo per la sottoscrizione corrente. Per visualizzare le assegnazioni di ruolo per la sottoscrizione corrente e di seguito `--all` , aggiungere il parametro. Per visualizzare le assegnazioni di ruolo ereditate `--include-inherited` , aggiungere il parametro.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo in Azure tramite l'interfaccia della riga di comando](role-assignments-cli.md)
