---
title: Elencare le assegnazioni di ruolo usando il controllo degli accessi in base al ruolo di Azure e l'API RESTList role assignments using Azure RBAC and the REST API
description: Informazioni su come determinare a quali risorse hanno accesso utenti, gruppi, entità servizio o identità gestite usando il controllo degli accessi in base al ruolo di Azure e l'API REST.
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
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062151"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Elencare le assegnazioni di ruolo usando il controllo degli accessi in base al ruolo di Azure e l'API RESTList role assignments using Azure RBAC and the REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Questo articolo descrive come elencare le assegnazioni di ruolo usando l'API REST.

> [!NOTE]
> Se l'organizzazione ha esternalizzato le funzioni di gestione a un provider di servizi che utilizza la gestione delle risorse delegate di [Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)le assegnazioni di ruolo autorizzate da tale provider di servizi non verranno visualizzate qui.

## <a name="list-role-assignments"></a>Elencare le assegnazioni di ruolo

Per visualizzare le informazioni sull'accesso nel controllo degli accessi in base al ruolo, elencare le assegnazioni di ruolo. Per elencare le assegnazioni di ruolo, usare una delle API REST per l'[elenco delle assegnazioni di ruolo](/rest/api/authorization/roleassignments/list). Per affinare i risultati, specificare un ambito e un filtro facoltativo.

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

    Nell'esempio precedente, microsoft.web è un provider di risorse che fa riferimento a un'istanza del servizio app. Analogamente, è possibile utilizzare qualsiasi altro provider di risorse e specificare l'ambito. Per altre informazioni, vedere [Tipi e provider di risorse](../azure-resource-manager/management/resource-providers-and-types.md) di Azure e Operazioni del provider di risorse di Azure Resource [Manager](resource-provider-operations.md)supportate.  
     
1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=atScope()` | Elenca le assegnazioni di ruolo solo per l'ambito specificato, escluse le assegnazioni di ruolo nei sottoambiti. |
    > | `$filter=assignedTo('{objectId}')` | Elenca le assegnazioni di ruolo per un utente o un'entità servizio specificata.<br/>Se l'utente è membro di un gruppo che dispone di un'assegnazione di ruolo, viene elencata anche tale assegnazione di ruolo. Questo filtro è transitivo per i gruppi, il che significa che se l'utente è membro di un gruppo e tale gruppo è membro di un altro gruppo che dispone di un'assegnazione di ruolo, tale assegnazione di ruolo è elencata anche.<br/>Questo filtro accetta solo un ID oggetto per un utente o un'entità servizio. Non è possibile passare un ID oggetto per un gruppo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Elenca le assegnazioni di ruolo per l'utente o l'entità servizio specificata e nell'ambito specificato. |
    > | `$filter=principalId+eq+'{objectId}'` | Elenca le assegnazioni di ruolo per un utente, un gruppo o un'entità servizio specificata. |

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo usando controllo degli accessi in base al ruolo di Azure e l'API RESTAdd or remove role assignments using Azure RBAC and the REST API](role-assignments-rest.md)
- [Guida di riferimento all'API REST di AzureAzure REST API Reference](/rest/api/azure/)
