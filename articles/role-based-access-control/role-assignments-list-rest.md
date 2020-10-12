---
title: Elencare le assegnazioni di ruolo di Azure usando l'API REST-RBAC di Azure
description: Informazioni su come determinare le risorse a cui utenti, gruppi, entità servizio o identità gestite possono accedere usando l'API REST e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 634e1111c9374a1749e7dbb0666740ce2833a688
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84790977"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Elencare le assegnazioni di ruolo di Azure tramite l'API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Questo articolo descrive come elencare le assegnazioni di ruolo usando l'API REST.

> [!NOTE]
> Se l'organizzazione dispone di funzioni di gestione esternalizzate a un provider di servizi che usa la [gestione delle risorse delegate di Azure](../lighthouse/concepts/azure-delegated-resource-management.md), le assegnazioni di ruolo autorizzate dal provider di servizi non verranno visualizzate qui.

## <a name="list-role-assignments"></a>Elencare le assegnazioni di ruolo

In controllo degli accessi in base al ruolo di Azure è possibile elencare le assegnazioni di ruolo. Per elencare le assegnazioni di ruolo, usare una delle API REST per l'[elenco delle assegnazioni di ruolo](/rest/api/authorization/roleassignments/list). Per affinare i risultati, specificare un ambito e un filtro facoltativo.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. All'intero dell'URI sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Risorsa |

    Nell'esempio precedente Microsoft. Web è un provider di risorse che fa riferimento a un'istanza del servizio app. Analogamente, è possibile usare qualsiasi altro provider di risorse e specificare l'ambito. Per altre informazioni, vedere [provider di risorse di Azure e tipi](../azure-resource-manager/management/resource-providers-and-types.md) e [le operazioni supportate Azure Resource Manager provider di risorse](resource-provider-operations.md).  
     
1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtra | Descrizione |
    > | --- | --- |
    > | `$filter=atScope()` | Elenca le assegnazioni di ruolo solo per l'ambito specificato, escluse le assegnazioni di ruolo in ambito sottoambito. |
    > | `$filter=assignedTo('{objectId}')` | Elenca le assegnazioni di ruolo per un utente o un'entità servizio specificata.<br/>Se l'utente è membro di un gruppo che dispone di un'assegnazione di ruolo, viene elencata anche tale assegnazione di ruolo. Questo filtro è transitivo per i gruppi che significa che se l'utente è un membro di un gruppo e tale gruppo è membro di un altro gruppo che dispone di un'assegnazione di ruolo, viene elencata anche tale assegnazione di ruolo.<br/>Questo filtro accetta solo un ID oggetto per un utente o un'entità servizio. Non è possibile passare un ID oggetto per un gruppo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Elenca le assegnazioni di ruolo per l'utente o l'entità servizio specificata e per l'ambito specificato. |
    > | `$filter=principalId+eq+'{objectId}'` | Elenca le assegnazioni di ruolo per un utente, un gruppo o un'entità servizio specificati. |

La richiesta seguente elenca tutte le assegnazioni di ruolo per l'utente specificato nell'ambito della sottoscrizione:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

Il testo seguente è un esempio di output:

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo di Azure tramite l'API REST](role-assignments-rest.md)
- [Informazioni di riferimento sulle API REST](/rest/api/azure/)
