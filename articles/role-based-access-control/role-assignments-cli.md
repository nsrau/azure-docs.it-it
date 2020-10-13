---
title: Aggiungere o rimuovere assegnazioni di ruolo di Azure tramite l'interfaccia della riga di comando di Azure-RBAC
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando l'interfaccia della riga di comando di Azure e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperfq1
ms.openlocfilehash: 16ead03af14da70b5aaedc21118488c6dd3012c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597659"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Aggiungere o rimuovere assegnazioni di ruolo di Azure tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Questo articolo descrive come assegnare i ruoli usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)
- [Bash in Azure cloud Shell o nell'interfaccia della riga di comando di](/azure/cloud-shell/overview) [Azure](/cli/azure)

## <a name="steps-to-add-a-role-assignment"></a>Procedura per aggiungere un'assegnazione di ruolo

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo di Azure, aggiungere un'assegnazione di ruolo. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito. Per aggiungere un'assegnazione di ruolo, attenersi alla seguente procedura.

### <a name="step-1-determine-who-needs-access"></a>Passaggio 1: determinare chi necessita dell'accesso

È possibile assegnare un ruolo a un utente, un gruppo, un'entità servizio o un'identità gestita. Per aggiungere un'assegnazione di ruolo, potrebbe essere necessario specificare l'ID univoco dell'oggetto. Il formato dell'ID è il seguente: `11111111-1111-1111-1111-111111111111`. È possibile ottenere l'ID usando il portale di Azure o l'interfaccia della riga di comando di Azure.

**Utente**

Per un utente Azure AD, ottenere il nome dell'entità utente, ad esempio *patlong \@ contoso.com* o l'ID oggetto utente. Per ottenere l'ID oggetto, è possibile usare [AZ ad User Show](/cli/azure/ad/user#az_ad_user_show).

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Gruppo**

Per un gruppo di Azure AD, è necessario l'ID oggetto gruppo. Per ottenere l'ID oggetto, è possibile usare [AZ ad Group Show](/cli/azure/ad/group#az_ad_group_show) o [AZ ad Group List](/cli/azure/ad/group#az_ad_group_list).

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Entità servizio**

Per un'entità servizio Azure AD (identità usata da un'applicazione), è necessario l'ID oggetto dell'entità servizio. Per ottenere l'ID oggetto, è possibile usare [AZ ad SP list](/cli/azure/ad/sp#az_ad_sp_list). Per un'entità servizio usare l'ID oggetto, **non** l'ID applicazione.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Identità gestita**

Per un'identità gestita assegnata dal sistema o assegnata dall'utente, è necessario l'ID oggetto. Per ottenere l'ID oggetto, è possibile usare [AZ ad SP list](/cli/azure/ad/sp#az_ad_sp_list).

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Per elencare solo le identità gestite assegnate dall'utente, è possibile usare [AZ Identity list](/cli/azure/identity#az_identity_list).

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>Passaggio 2: trovare il ruolo appropriato

Le autorizzazioni vengono raggruppate in ruoli. È possibile scegliere da un elenco di diversi [ruoli predefiniti di Azure](built-in-roles.md) oppure usare ruoli personalizzati. È consigliabile concedere l'accesso con il privilegio minimo necessario, quindi evitare di assegnare un ruolo più ampio.

Per elencare i ruoli e ottenere l'ID del ruolo univoco, è possibile usare [AZ Role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

Di seguito viene illustrato come elencare i dettagli di un particolare ruolo.

```azurecli
az role definition list --name "{roleName}"
```

Per altre informazioni, vedere [elencare le definizioni dei ruoli di Azure](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>Passaggio 3: identificare l'ambito necessario

Azure offre quattro livelli di ambito: risorse, [gruppo di risorse](../azure-resource-manager/management/overview.md#resource-groups), sottoscrizione e [gruppo di gestione](../governance/management-groups/overview.md). È consigliabile concedere l'accesso con il privilegio minimo necessario, quindi evitare di assegnare un ruolo a un ambito più ampio. Per altre informazioni sull'ambito, vedere [Informazioni sull'ambito](scope-overview.md).

**Ambito risorsa**

Per ambito di risorse, è necessario l'ID risorsa per la risorsa. È possibile trovare l'ID risorsa esaminando le proprietà della risorsa nel portale di Azure. Un ID di risorsa ha il formato seguente.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Gruppo di risorse**

Per ambito del gruppo di risorse, è necessario il nome del gruppo di risorse. È possibile trovare il nome nella pagina **gruppi di risorse** nel portale di Azure oppure è possibile usare il comando [AZ Group List](/cli/azure/group#az_group_list).

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Ambito della sottoscrizione** 

Per ambito sottoscrizione, è necessario l'ID sottoscrizione. È possibile trovare l'ID nella pagina **sottoscrizioni** nella portale di Azure oppure è possibile usare il comando [AZ account list](/cli/azure/account#az_account_list).

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Ambito del gruppo di gestione** 

Per ambito gruppo di gestione, è necessario il nome del gruppo di gestione. È possibile trovare il nome nella pagina **gruppi di gestione** nel portale di Azure oppure è possibile usare il comando [AZ Account Management-Group List](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>Passaggio 4: aggiungere l'assegnazione di ruolo

Per aggiungere un'assegnazione di ruolo, usare il comando [AZ Role Assignment create](/cli/azure/role/assignment#az_role_assignment_create) . A seconda dell'ambito, il comando ha in genere uno dei formati seguenti.

**Ambito risorsa**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Gruppo di risorse**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Ambito della sottoscrizione** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Ambito del gruppo di gestione** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Di seguito viene illustrato un esempio dell'output quando si assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) a un utente in un ambito del gruppo di risorse.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="add-role-assignment-examples"></a>Esempi di assegnazione di ruolo

### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Aggiungere un'assegnazione di ruolo per un ambito di risorsa contenitore BLOB specifico

Assegna il ruolo di [collaboratore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-contributor) a un'entità servizio con ID oggetto *55555555-5555-5555-5555-555555555555* in un ambito di risorse per un contenitore BLOB denominato *BLOB-container-01*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Aggiungere un'assegnazione di ruolo per tutti i contenitori BLOB in un ambito di risorse dell'account di archiviazione

Assegna il ruolo di [collaboratore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-contributor) a un'entità servizio con ID oggetto *55555555-5555-5555-5555-555555555555* in un ambito di risorse per un account di archiviazione denominato *storage12345*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Aggiungere un'assegnazione di ruolo per un gruppo in un ambito di risorse della rete virtuale specifico

Assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito di risorse per una rete virtuale denominata *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>Aggiungere un'assegnazione di ruolo per un utente in un ambito del gruppo di risorse

Assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) all'utente *patlong \@ contoso.com* nell'ambito del gruppo di risorse *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Aggiungere l'assegnazione di ruolo per un utente usando l'ID del ruolo univoco in un ambito del gruppo di risorse

Ci sono un paio di volte in cui un nome di ruolo potrebbe cambiare, ad esempio:

- Si usa il proprio ruolo personalizzato e si decide di modificare il nome.
- Si sta usando un ruolo di anteprima con **(anteprima)** nel nome. Quando il ruolo viene rilasciato, il ruolo viene rinominato.

Anche se un ruolo viene rinominato, l'ID del ruolo non cambia. Se si usano script o automazione per creare le assegnazioni di ruolo, è consigliabile usare l'ID del ruolo univoco anziché il nome del ruolo. Di conseguenza, se un ruolo viene rinominato, è più probabile che gli script funzionino.

L'esempio seguente assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) all'utente * \@ contoso.com di patlong* nell'ambito del gruppo di risorse *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>Aggiungere un'assegnazione di ruolo per tutti i contenitori BLOB in un ambito del gruppo di risorse

Assegna il ruolo di [collaboratore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-contributor) a un'entità servizio con ID oggetto *55555555-5555-5555-5555-555555555555* nell'ambito del gruppo di risorse *esempio-storage-RG* .

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

In alternativa, è possibile specificare il gruppo di risorse completo con il `--scope` parametro:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Aggiungere un'assegnazione di ruolo per un'applicazione in un ambito del gruppo di risorse

Assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) a un'applicazione con ID oggetto entità servizio 44444444-4444-4444-4444-444444444444 nell'ambito del gruppo di risorse *Pharma-Sales* .

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>Aggiungere un'assegnazione di ruolo per una nuova entità servizio in un ambito del gruppo di risorse

Se si crea una nuova entità servizio e si prova ad assegnare subito un ruolo a tale entità, in alcuni casi l'assegnazione di ruolo può avere esito negativo. Se ad esempio si utilizza uno script per creare una nuova identità gestita e quindi si tenta di assegnare un ruolo a tale entità servizio, l'assegnazione del ruolo potrebbe non riuscire. Il motivo di questo errore è probabilmente un ritardo di replica. L'entità servizio viene creata in un'area. L'assegnazione di ruolo potrebbe tuttavia verificarsi in un'area diversa che non ha ancora replicato l'entità servizio. Per risolvere questo scenario, è necessario specificare il tipo di entità durante la creazione dell'assegnazione di ruolo.

Per aggiungere un'assegnazione di ruolo, usare [AZ Role Assignment create](/cli/azure/role/assignment#az_role_assignment_create), specificare un valore per `--assignee-object-id` e quindi impostare `--assignee-principal-type` su `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

L'esempio seguente assegna il ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) all'identità gestita del *test MSI* nell'ambito del gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Aggiungere un'assegnazione di ruolo per un utente in un ambito di sottoscrizione

Assegna il ruolo [Reader](built-in-roles.md#reader) all'utente * \@ example.com di annm* in un ambito di sottoscrizione.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>Aggiungere un'assegnazione di ruolo per un gruppo in un ambito di sottoscrizione

Assegna il ruolo [lettore](built-in-roles.md#reader) al gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 nell'ambito di una sottoscrizione.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>Aggiungere un'assegnazione di ruolo per tutti i contenitori BLOB in un ambito di sottoscrizione

Assegna il ruolo [lettore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-reader) all'utente *Alain \@ example.com* in un ambito di sottoscrizione.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Aggiunta dell'assegnazione di ruolo per un utente in un ambito del gruppo di gestione

Assegna il ruolo di [lettore fatturazione](built-in-roles.md#billing-reader) all'utente *Alain \@ example.com* in un ambito del gruppo di gestione.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-role-assignment"></a>Rimuovere l'assegnazione di ruolo

In controllo degli accessi in base al ruolo di Azure, rimuovere un'assegnazione di ruolo usando [AZ Role Assignment Delete](/cli/azure/role/assignment#az_role_assignment_delete).

Nell'esempio seguente viene rimossa l'assegnazione di ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) dall'utente *patlong \@ contoso.com* nel gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Rimuove il ruolo [lettore](built-in-roles.md#reader) dal gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito della sottoscrizione.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Rimuove il ruolo [Lettura fatturazione](built-in-roles.md#billing-reader) dall'utente *Alain \@ example.com* nell'ambito del gruppo di gestione.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo di Azure tramite l'interfaccia della riga](role-assignments-list-cli.md)
- [Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse](../azure-resource-manager/cli-azure-resource-manager.md)
