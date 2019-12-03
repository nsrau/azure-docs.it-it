---
title: Elencare le assegnazioni di ruolo usando RBAC di Azure e l'API REST
description: Informazioni su come determinare le risorse a cui utenti, gruppi, entità servizio o identità gestite possono accedere usando il controllo degli accessi in base al ruolo (RBAC) di Azure e l'API REST.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e20edcb5e2406c216711a2e0f696ef06e19fe21e
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710399"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Elencare le assegnazioni di ruolo usando RBAC di Azure e l'API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] questo articolo descrive come elencare le assegnazioni di ruolo usando l'API REST.

## <a name="list-role-assignments"></a>Elencare le assegnazioni di ruolo

Per visualizzare le informazioni sull'accesso nel controllo degli accessi in base al ruolo, elencare le assegnazioni di ruolo. Per elencare le assegnazioni di ruolo, usare una delle API REST per l'[elenco delle assegnazioni di ruolo](/rest/api/authorization/roleassignments/list). Per affinare i risultati, specificare un ambito e un filtro facoltativo.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. All'intero dell'URI sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    | `subscriptions/{subscriptionId1}` | Sottoscrizione |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Gruppi |

    Nell'esempio precedente Microsoft. Web è un provider di risorse che fa riferimento a un'istanza del servizio app. Analogamente, è possibile usare qualsiasi altro provider di risorse e specificare l'ambito. Per altre informazioni, vedere [provider di risorse di Azure e tipi](../azure-resource-manager/resource-manager-supported-services.md) e [le operazioni supportate Azure Resource Manager provider di risorse](resource-provider-operations.md).  
     
1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di ruolo.

    | Filtra | Description |
    | --- | --- |
    | `$filter=atScope()` | Elenca le assegnazioni di ruolo solo per l'ambito specificato, escluse le assegnazioni di ruolo in ambito sottoambito. |
    | `$filter=principalId%20eq%20'{objectId}'` | Elenca le assegnazioni di ruolo per un utente, un gruppo o un'entità servizio specificati. |
    | `$filter=assignedTo('{objectId}')` | Elenca le assegnazioni di ruolo per un utente o un'entità servizio specificata. Se l'utente è membro di un gruppo che dispone di un'assegnazione di ruolo, viene elencata anche tale assegnazione di ruolo. Questo filtro è transitivo per i gruppi che significa che se l'utente è un membro di un gruppo e tale gruppo è membro di un altro gruppo che dispone di un'assegnazione di ruolo, viene elencata anche tale assegnazione di ruolo. Questo filtro accetta solo un ID oggetto per un utente o un'entità servizio. Non è possibile passare un ID oggetto per un gruppo. |

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e l'API REST](role-assignments-rest.md)
- [Informazioni di riferimento sulle API REST](/rest/api/azure/)
