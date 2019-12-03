---
title: Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e l'interfaccia della riga di comando
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando il controllo degli accessi in base al ruolo di Azure (RBAC) e l'interfaccia della riga di comando di Azure.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0351721283df68fde910ae16b16d567954c3e6fb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707902"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e l'interfaccia della riga di comando

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] questo articolo descrive come assegnare i ruoli usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)
- [Bash in Azure cloud Shell o nell'interfaccia della riga di comando di](/azure/cloud-shell/overview) [Azure](/cli/azure)

## <a name="get-object-ids"></a>Ottenere gli ID oggetto

Per aggiungere o rimuovere assegnazioni di ruolo, potrebbe essere necessario specificare l'ID univoco di un oggetto. Il formato dell'ID è: `11111111-1111-1111-1111-111111111111`. È possibile ottenere l'ID usando il portale di Azure o l'interfaccia della riga di comando di Azure.

### <a name="user"></a>Utente

Per ottenere l'ID oggetto per un utente di Azure AD, è possibile usare [AZ ad User Show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Group

Per ottenere l'ID oggetto per un gruppo di Azure AD, è possibile usare [AZ ad Group Show](/cli/azure/ad/group#az-ad-group-show) o [AZ ad Group List](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Richiesta

Per ottenere l'ID oggetto per un'entità servizio Azure AD (identità usata da un'applicazione), è possibile usare il comando [AZ ad SP list](/cli/azure/ad/sp#az-ad-sp-list). Per un'entità servizio, usare l'ID oggetto e **non** l'ID applicazione.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

In RBAC, per concedere l'accesso, si aggiunge un'assegnazione di ruolo.

### <a name="user-at-a-resource-group-scope"></a>Utente in un ambito del gruppo di risorse

Per aggiungere un'assegnazione di ruolo per un utente in un ambito del gruppo di risorse, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

L'esempio seguente assegna il ruolo *collaboratore macchina virtuale* a *patlong\@utente contoso.com* nell'ambito del gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Utilizzo dell'ID ruolo univoco

Ci sono un paio di volte in cui un nome di ruolo potrebbe cambiare, ad esempio:

- Si usa il proprio ruolo personalizzato e si decide di modificare il nome.
- Si sta usando un ruolo di anteprima con **(anteprima)** nel nome. Quando il ruolo viene rilasciato, il ruolo viene rinominato.

> [!IMPORTANT]
> Una versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Anche se un ruolo viene rinominato, l'ID del ruolo non cambia. Se si usano script o automazione per creare le assegnazioni di ruolo, è consigliabile usare l'ID del ruolo univoco anziché il nome del ruolo. Di conseguenza, se un ruolo viene rinominato, è più probabile che gli script funzionino.

Per aggiungere un'assegnazione di ruolo usando l'ID del ruolo univoco anziché il nome del ruolo, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

L'esempio seguente assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) all'utente *patlong\@contoso.com* nell'ambito del gruppo di risorse *Pharma-Sales* . Per ottenere l'ID del ruolo univoco, è possibile usare [AZ Role definition list](/cli/azure/role/definition#az-role-definition-list) o vedere [ruoli predefiniti per le risorse di Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Gruppo nell'ambito di una sottoscrizione

Per aggiungere un'assegnazione di ruolo per un gruppo, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per informazioni su come ottenere l'ID oggetto del gruppo, vedere ottenere gli [ID oggetto](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Nell'esempio seguente viene assegnato il ruolo *lettore* al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito di sottoscrizione.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Gruppo nell'ambito di una risorsa

Per aggiungere un'assegnazione di ruolo per un gruppo, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per informazioni su come ottenere l'ID oggetto del gruppo, vedere ottenere gli [ID oggetto](#get-object-ids).

L'esempio seguente assegna il ruolo *collaboratore macchina virtuale* al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito di risorse per una rete virtuale denominata *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Applicazione in un ambito del gruppo di risorse

Per aggiungere un'assegnazione di ruolo per un'applicazione, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per informazioni su come ottenere l'ID oggetto dell'applicazione, vedere ottenere gli [ID oggetto](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Nell'esempio seguente il ruolo *collaboratore macchina virtuale* viene assegnato a un'applicazione con ID oggetto 44444444-4444-4444-4444-444444444444 nell'ambito del gruppo di risorse *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Utente nell'ambito di una sottoscrizione

Per aggiungere un'assegnazione di ruolo per un utente in un ambito di sottoscrizione, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per ottenere l'ID sottoscrizione, è possibile trovarlo nel pannello **sottoscrizioni** nella portale di Azure oppure è possibile usare il comando [AZ account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Nell'esempio seguente il ruolo *Reader* viene assegnato all'utente *annm\@example.com* in un ambito di sottoscrizione.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Utente presso un ambito del gruppo di gestione

Per aggiungere un'assegnazione di ruolo per un utente a un ambito del gruppo di gestione, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create). Per ottenere l'ID del gruppo di gestione, è possibile trovarlo nel pannello **gruppi di gestione** della portale di Azure oppure è possibile usare il comando [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Nell'esempio seguente il ruolo di *Lettura fatturazione* viene assegnato all'utente *Alain\@example.com* in un ambito del gruppo di gestione.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nuova entità servizio

Se si crea una nuova entità servizio e si tenta immediatamente di assegnare un ruolo a tale entità servizio, l'assegnazione di ruolo può non riuscire in alcuni casi. Se ad esempio si utilizza uno script per creare una nuova identità gestita e quindi si tenta di assegnare un ruolo a tale entità servizio, l'assegnazione del ruolo potrebbe non riuscire. Il motivo di questo errore è probabilmente un ritardo di replica. L'entità servizio viene creata in un'area; Tuttavia, l'assegnazione di ruolo potrebbe verificarsi in un'area diversa che non ha ancora replicato l'entità servizio. Per risolvere questo scenario, è necessario specificare il tipo di entità durante la creazione dell'assegnazione di ruolo.

Per aggiungere un'assegnazione di ruolo, usare [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create), specificare un valore per `--assignee-object-id`, quindi impostare `--assignee-principal-type` su `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

L'esempio seguente assegna il ruolo *collaboratore macchina virtuale* all'identità gestita del *test MSI* nell'ambito del gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Per rimuovere l'accesso nel controllo degli accessi in base al ruolo, è possibile rimuovere un'assegnazione di ruolo tramite [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Nell'esempio seguente viene rimossa l'assegnazione di ruolo *collaboratore macchina virtuale* dall'utente *patlong\@contoso.com* nel gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Nell'esempio seguente viene rimosso il ruolo *Reader* dal gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito della sottoscrizione. Per informazioni su come ottenere l'ID oggetto del gruppo, vedere ottenere gli [ID oggetto](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Nell'esempio seguente viene rimosso il ruolo *Lettura fatturazione* dall'utente *Alain\@example.com* nell'ambito del gruppo di gestione. Per ottenere l'ID del gruppo di gestione, è possibile usare il comando [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo usando l'interfaccia della riga di comando di Azure](role-assignments-list-cli.md)
- [Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse](../azure-resource-manager/cli-azure-resource-manager.md)
