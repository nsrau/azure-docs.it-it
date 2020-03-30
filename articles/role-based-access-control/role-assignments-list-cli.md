---
title: Elencare le assegnazioni di ruolo usando Controllo degli accessi in base al ruolo di Azure e l'interfaccia della riga di comando di AzureList role assignments
description: Informazioni su come determinare a quali risorse hanno accesso utenti, gruppi, entità servizio o identità gestite tramite il controllo degli accessi in base al ruolo di Azure e l'interfaccia della riga di comando di Azure.Learn how to determine what resources users, groups, service principals, or managed identities have access to using Azure role-based access control (RBAC) and Azure CLI.
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
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385062"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Elencare le assegnazioni di ruolo usando Controllo degli accessi in base al ruolo di Azure e l'interfaccia della riga di comando di AzureList role assignments

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Questo articolo descrive come elencare le assegnazioni di ruolo usando l'interfaccia della riga di comando di Azure.This article describes how to list role assignments using Azure CLI.

> [!NOTE]
> Se l'organizzazione ha esternalizzato le funzioni di gestione a un provider di servizi che utilizza la gestione delle risorse delegate di [Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)le assegnazioni di ruolo autorizzate da tale provider di servizi non verranno visualizzate qui.

## <a name="prerequisites"></a>Prerequisiti

- [Bash in Azure Cloud Shell](/azure/cloud-shell/overview) o [cliTOride di Azure](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente

Per elencare le assegnazioni di ruolo per un utente specifico, usare [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Per impostazione predefinita, verranno visualizzate solo le assegnazioni di ruolo per la sottoscrizione corrente. Per visualizzare le assegnazioni di ruolo per `--all` la sottoscrizione corrente e di seguito, aggiungere il parametro . Per visualizzare le assegnazioni di `--include-inherited` ruolo ereditate, aggiungere il parametro.

Nell'esempio seguente sono elencate le assegnazioni di ruolo assegnate direttamente all'utente *\@di contoso.com di pattuglia:*

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

Per elencare le assegnazioni di ruolo esistenti nell'ambito di un gruppo di risorse, utilizzare [l'elenco di assegnazione](/cli/azure/role/assignment#az-role-assignment-list)dei ruoli az:

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

Nell'esempio seguente sono elencate le assegnazioni di ruolo per il gruppo di risorse *pharma-sales:*

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

Per elencare tutte le assegnazioni di ruolo in un ambito di sottoscrizione, utilizzare [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). Per ottenere l'ID sottoscrizione, è possibile trovarlo nel pannello **Sottoscrizioni** nel portale di Azure oppure usare l'elenco di [account az.](/cli/azure/account#az-account-list)

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Esempio:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Elencare le assegnazioni di ruolo per un gruppo di gestione

Per elencare tutte le assegnazioni di ruolo nell'ambito di un gruppo di gestione, utilizzare [az role assignmentlist](/cli/azure/role/assignment#az-role-assignment-list). Per ottenere l'ID del gruppo di gestione, è possibile trovarlo nel pannello Gruppi di **gestione** nel portale di Azure oppure usare l'elenco dei gruppi di [gestione account az.](/cli/azure/account/management-group#az-account-management-group-list)

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Esempio:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Elencare le assegnazioni di ruolo per un'identità gestitaList role assignments for a managed identity

1. Ottenere l'ID oggetto dell'identità gestita assegnata dal sistema o dall'utente.

    Per ottenere l'ID oggetto di un'identità gestita assegnata dall'utente, è possibile utilizzare [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) o az identity [list](/cli/azure/identity#az-identity-list).

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Per ottenere l'ID oggetto di un'identità gestita assegnata dal sistema, è possibile utilizzare [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Per elencare le assegnazioni di ruolo, utilizzare [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

    Per impostazione predefinita, verranno visualizzate solo le assegnazioni di ruolo per la sottoscrizione corrente. Per visualizzare le assegnazioni di ruolo per `--all` la sottoscrizione corrente e di seguito, aggiungere il parametro . Per visualizzare le assegnazioni di `--include-inherited` ruolo ereditate, aggiungere il parametro.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo usando Controllo degli accessi in base al ruolo di Azure e l'interfaccia della riga di comando di AzureAdd or remove role assignments using Azure RBAC and](role-assignments-cli.md)
