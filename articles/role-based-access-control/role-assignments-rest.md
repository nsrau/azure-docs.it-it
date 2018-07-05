---
title: Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'API REST - Azure | Microsoft Docs
description: Informazioni su come gestire l'accesso per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e l'API REST. Vengono presentate anche la visualizzazione, la concessione e la rimozione dell'accesso.
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 859a410a4ff9204e8e52fbd2cc3b38823f4bb830
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435219"
---
# <a name="manage-access-using-rbac-and-the-rest-api"></a>Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'API REST

[Il controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Questo articolo descrive come gestire l'accesso per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e l'API REST.

## <a name="list-access"></a>Elencare l'accesso

Per visualizzare le informazioni sull'accesso nel controllo degli accessi in base al ruolo, elencare le assegnazioni di ruolo. Per elencare le assegnazioni di ruolo, usare una delle API REST per l'[elenco delle assegnazioni di ruolo](/rest/api/authorization/roleassignments/list). Per affinare i risultati, specificare un ambito e un filtro facoltativo. Per chiamare l'API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/read` nell'ambito specificato. L'accesso a questa operazione viene concesso a diversi [ruoli predefiniti](built-in-roles.md).

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. All'intero dell'URI sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo.

    | Scope | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di ruolo.

    | Filtro | DESCRIZIONE |
    | --- | --- |
    | `$filter=atScope()` | Elencare le assegnazioni di ruolo solo per l'ambito specificato, senza includere le assegnazioni di ruolo negli ambiti secondari. |
    | `$filter=principalId%20eq%20'{objectId}'` | Elencare le assegnazioni di ruolo per un utente, un gruppo o un'entità servizio specificata. |
    | `$filter=assignedTo('{objectId}')` | Elencare le assegnazioni di ruolo per un utente specificato, incluse quelle ereditate dai gruppi. |

## <a name="grant-access"></a>Concedere l'accesso

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo. Per creare un'assegnazione di ruolo, usare l'API REST per la [creazione di assegnazioni di ruolo](/rest/api/authorization/roleassignments/create) e specificare l'entità di sicurezza, la definizione del ruolo e l'ambito. Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/write`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator).

1. Usare l'API REST per l'[elenco delle definizioni del ruolo](/rest/api/authorization/roledefinitions/list) o vedere [Ruoli predefiniti](built-in-roles.md) per ottenere l'identificatore per la definizione del ruolo da assegnare.

1. Usare uno strumento GUID per generare un identificatore univoco che verrà usato per l'identificatore dell'assegnazione di ruolo. Il formato dell'identificatore è: `00000000-0000-0000-0000-000000000000`

1. Iniziare con la richiesta e il corpo seguenti:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. All'interno dell'URI sostituire *{scope}* con l'ambito per l'assegnazione di ruolo.

    | Scope | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{roleAssignmentName}* con l'identificatore GUID dell'assegnazione di ruolo.

1. All'interno del corpo della richiesta sostituire *{subscriptionId}* con l'identificatore della sottoscrizione.

1. Sostituire *{roleDefinitionId}* con l'identificatore della definizione del ruolo.

1. Sostituire *{principalId}* con l'identificatore di oggetto dell'utente, del gruppo o dell'entità servizio a cui verrà assegnato il ruolo.

## <a name="remove-access"></a>Rimuovere un accesso

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo. Per rimuovere un'assegnazione di ruolo, usare l'API REST per l'[eliminazione delle assegnazioni di ruolo](/rest/api/authorization/roleassignments/delete). Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/delete`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator).

1. Ottenere l'identificatore di assegnazione di ruolo (GUID). Questo identificatore viene restituito quando si crea l'assegnazione di ruolo per la prima volta oppure è possibile ottenerlo elencando le assegnazioni di ruolo.

1. Iniziare con la richiesta seguente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per la rimozione dell'assegnazione di ruolo.

    | Scope | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{roleAssignmentName}* con l'identificatore GUID dell'assegnazione di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire le risorse con i modelli e l'API REST di Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Informazioni di riferimento sulle API REST](/rest/api/azure/)
- [Creare ruoli personalizzati con l'API REST](custom-roles-rest.md)