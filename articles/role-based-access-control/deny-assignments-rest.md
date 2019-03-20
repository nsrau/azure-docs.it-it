---
title: Elencare le assegnazioni di rifiuto per le risorse di Azure usando l'API REST - Azure | Microsoft Docs
description: Informazioni su come elencare le assegnazioni di rifiuto per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo per le risorse di Azure e l'API REST.
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
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 59bcf2b33d203ae216b4965b963a727a6b34ae72
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998402"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Elencare le assegnazioni di rifiuto per le risorse di Azure usando l'API REST

Le [assegnazioni di rifiuto](deny-assignments.md) impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso. Questo articolo descrive come usare l'API REST a elenco negare le assegnazioni.

> [!NOTE]
> A questo punto, l'unico modo è possibile aggiungere il proprio negare assegnazioni consiste nell'usare progetti di architetture di Azure. Per altre informazioni, vedere [proteggere le nuove risorse con i blocchi di risorse di Azure progetti](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="prerequisites"></a>Prerequisiti

Per ottenere informazioni su un'assegnazione di negazione, è necessario disporre di:

- `Microsoft.Authorization/denyAssignments/read` autorizzazione di accesso, che è incluso nella maggior parte [ruoli predefiniti per le risorse di Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Elencare una singola assegnazione di rifiuto

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di rifiuto.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

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

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di rifiuto.

    | Filtro | DESCRIZIONE |
    | --- | --- |
    | (nessun filtro) | Elenca tutte le assegnazioni di ruolo in corrispondenza, al di sopra e al di sotto dell'ambito specificato. |
    | `$filter=atScope()` | Elenca le assegnazioni di rifiuto per il solo ambito specificato e quanto sopra. Le assegnazioni di rifiuto nei sottoambiti non sono incluse. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Elenca le assegnazioni di rifiuto con il nome specificato. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Elenca le assegnazioni di rifiuto all'ambito radice (/)

1. Eseguire con privilegi elevati l'accesso come descritto in [Eseguire con privilegi elevati l'accesso per l'amministratore globale in Azure Active Directory](elevate-access-global-admin.md).

1. Usare la richiesta seguente:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di rifiuto. È obbligatorio applicare un filtro.

    | Filtro | DESCRIZIONE |
    | --- | --- |
    | `$filter=atScope()` | Elenca le assegnazioni di rifiuto per il solo ambito radice. Le assegnazioni di rifiuto nei sottoambiti non sono incluse. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Elenca le assegnazioni di rifiuto con il nome specificato. |

1. Rimuove l'accesso con privilegi elevati.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle assegnazioni di rifiuto per le risorse di Azure](deny-assignments.md)
- [Eseguire con privilegi elevati l'accesso per l'amministratore globale in Azure Active Directory](elevate-access-global-admin.md)
- [Informazioni di riferimento sulle API REST](/rest/api/azure/)
