---
title: Elencare le assegnazioni di negazione per le risorse di Azure con l'API RESTList deny assignments for Azure resources with the REST API
description: Informazioni su come elencare le assegnazioni di negazione per utenti, gruppi e applicazioni usando il controllo degli accessi in base al ruolo per le risorse di Azure e l'API REST.
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
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063026"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Elencare le assegnazioni di rifiuto per le risorse di Azure usando l'API REST

Le [assegnazioni di rifiuto](deny-assignments.md) impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso. Questo articolo descrive come elencare le assegnazioni di negazione usando l'API REST.

> [!NOTE]
> Non è possibile creare direttamente le proprie assegnazioni di negazione. Per informazioni sulla modalità di creazione delle assegnazioni di negazione, vedere [Negare le assegnazioni](deny-assignments.md).

## <a name="prerequisites"></a>Prerequisiti

Per ottenere informazioni su un'assegnazione di negazione, è necessario disporre di:

- `Microsoft.Authorization/denyAssignments/read`autorizzazioni, inclusa nella maggior parte dei ruoli predefiniti per le risorse di [Azure.](built-in-roles.md)

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
    > | (nessun filtro) | Elenca tutte le assegnazioni di negazione in corrispondenza, sopra e sotto l'ambito specificato. |
    > | `$filter=atScope()` | Elenca le assegnazioni di negazione solo per l'ambito specificato e versioni successive. Le assegnazioni di rifiuto nei sottoambiti non sono incluse. |
    > | `$filter=assignedTo('{objectId}')` | Elenca le assegnazioni di negazione per l'utente o l'entità servizio specificata.<br/>Se l'utente è membro di un gruppo con un'assegnazione di negazione, viene elencata anche tale assegnazione di negazione. Questo filtro è transitivo per i gruppi, il che significa che se l'utente è membro di un gruppo e tale gruppo è membro di un altro gruppo che dispone di un'assegnazione di negazione, viene elencata anche tale assegnazione di negazione.<br/>Questo filtro accetta solo un ID oggetto per un utente o un'entità servizio. Non è possibile passare un ID oggetto per un gruppo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Elenca le assegnazioni di negazione per l'utente o l'entità servizio specificata e l'ambito specificato. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Elenca le assegnazioni di negazione con il nome specificato. |
    > | `$filter=principalId+eq+'{objectId}'` | Elenca le assegnazioni di negazione per l'utente, il gruppo o l'entità servizio specificata. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Elenca le assegnazioni di rifiuto all'ambito radice (/)

1. Eseguire con privilegi elevati l'accesso come descritto in [Eseguire con privilegi elevati l'accesso per l'amministratore globale in Azure Active Directory](elevate-access-global-admin.md).

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

- [Informazioni sulle assegnazioni di rifiuto per le risorse di Azure](deny-assignments.md)
- [Elevare l'accesso per un amministratore globale in Azure Active DirectoryElevate access for a Global Administrator in Azure Active Directory](elevate-access-global-admin.md)
- [Guida di riferimento all'API REST di AzureAzure REST API Reference](/rest/api/azure/)
