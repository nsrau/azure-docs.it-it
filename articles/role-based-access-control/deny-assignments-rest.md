---
title: Elencare le assegnazioni di Azure negate usando l'API REST-RBAC di Azure
description: Informazioni su come elencare le assegnazioni di rifiuto di Azure per utenti, gruppi e applicazioni usando l'API REST e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791912"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>Elencare le assegnazioni di Azure Deny usando l'API REST

Le [assegnazioni di rifiuto di Azure](deny-assignments.md) impediscono agli utenti di eseguire azioni specifiche delle risorse di Azure anche se un'assegnazione di ruolo concede loro l'accesso. Questo articolo descrive come elencare le assegnazioni di rifiuto usando l'API REST.

> [!NOTE]
> Non è possibile creare direttamente le assegnazioni di rifiuto. Per informazioni sul modo in cui vengono create le assegnazioni Deny, vedere le [assegnazioni di rifiuto di Azure](deny-assignments.md).

## <a name="prerequisites"></a>Prerequisiti

Per ottenere informazioni su un'assegnazione Deny, è necessario disporre di:

- `Microsoft.Authorization/denyAssignments/read`autorizzazione, inclusa nella maggior parte dei [ruoli predefiniti di Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Elencare una singola assegnazione di rifiuto

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di rifiuto.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscription |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{deny-assignment-id}* con l'identificatore dell'assegnazione di rifiuto da recuperare.

## <a name="list-multiple-deny-assignments"></a>Elencare più assegnazioni di rifiuto

1. Iniziare con una delle richieste seguenti:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Parametri facoltativi:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di rifiuto.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscription |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di rifiuto.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | (nessun filtro) | Elenca tutte le assegnazioni Deny in corrispondenza di, sopra e sotto l'ambito specificato. |
    > | `$filter=atScope()` | Elenca le assegnazioni Deny solo per l'ambito specificato e versioni successive. Le assegnazioni di rifiuto nei sottoambiti non sono incluse. |
    > | `$filter=assignedTo('{objectId}')` | Elenca le assegnazioni Deny per l'utente o l'entità servizio specificata.<br/>Se l'utente è membro di un gruppo che dispone di un'assegnazione Deny, viene elencata anche l'assegnazione Deny. Questo filtro è transitivo per i gruppi, il che significa che se l'utente è un membro di un gruppo e tale gruppo è membro di un altro gruppo che dispone di un'assegnazione Deny, viene anche elencata l'assegnazione di negazione.<br/>Questo filtro accetta solo un ID oggetto per un utente o un'entità servizio. Non è possibile passare un ID oggetto per un gruppo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Elenca le assegnazioni Deny per l'utente o l'entità servizio specificata e per l'ambito specificato. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Elenca le assegnazioni Deny con il nome specificato. |
    > | `$filter=principalId+eq+'{objectId}'` | Elenca le assegnazioni Deny per l'utente, il gruppo o l'entità servizio specificati. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Elenca le assegnazioni di rifiuto all'ambito radice (/)

1. Elevare l'accesso come descritto in [elevare l'accesso per gestire tutti i gruppi di gestione e le sottoscrizioni di Azure](elevate-access-global-admin.md).

1. Usare la richiesta seguente:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di rifiuto. È obbligatorio applicare un filtro.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=atScope()` | Elenca le assegnazioni di rifiuto per il solo ambito radice. Le assegnazioni di rifiuto nei sottoambiti non sono incluse. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Elenca le assegnazioni di rifiuto con il nome specificato. |

1. Rimuove l'accesso con privilegi elevati.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle assegnazioni di rifiuto di Azure](deny-assignments.md)
- [Elevare i privilegi di accesso per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure](elevate-access-global-admin.md)
- [Informazioni di riferimento sull'API REST di Azure](/rest/api/azure/)
