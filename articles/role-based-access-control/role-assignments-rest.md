---
title: Gestire l'accesso alle risorse di Azure con RBAC e l'API REST-Azure | Microsoft Docs
description: Informazioni su come gestire l'accesso alle risorse di Azure per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e l'API REST. Vengono presentate anche la visualizzazione, la concessione e la rimozione dell'accesso.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996464"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e l'API REST

Il [controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse di Azure. Questo articolo descrive come gestire l'accesso per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e l'API REST.

## <a name="list-access"></a>Elencare l'accesso

Per visualizzare le informazioni sull'accesso nel controllo degli accessi in base al ruolo, elencare le assegnazioni di ruolo. Per elencare le assegnazioni di ruolo, usare una delle API REST per l'[elenco delle assegnazioni di ruolo](/rest/api/authorization/roleassignments/list). Per affinare i risultati, specificare un ambito e un filtro facoltativo.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. All'intero dell'URI sostituire *{scope}* con l'ambito per il quale elencare le assegnazioni di ruolo.

    | Ambito | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    | `subscriptions/{subscriptionId1}` | Sottoscrizione |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

    Nell'esempio precedente Microsoft. Web è un provider di risorse che fa riferimento a un'istanza del servizio app. Analogamente, è possibile usare qualsiasi altro provider di risorse e specificare l'ambito. Per altre informazioni, vedere [provider di risorse di Azure e tipi](../azure-resource-manager/resource-manager-supported-services.md) e [le operazioni supportate Azure Resource Manager provider di risorse](resource-provider-operations.md).  
     
1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco delle assegnazioni di ruolo.

    | Applica filtro | Descrizione |
    | --- | --- |
    | `$filter=atScope()` | Elenca le assegnazioni di ruolo solo per l'ambito specificato, escluse le assegnazioni di ruolo in ambito sottoambito. |
    | `$filter=principalId%20eq%20'{objectId}'` | Elenca le assegnazioni di ruolo per un utente, un gruppo o un'entità servizio specificati. |
    | `$filter=assignedTo('{objectId}')` | Elenca le assegnazioni di ruolo per un utente o un'entità servizio specificata. Se l'utente è membro di un gruppo che dispone di un'assegnazione di ruolo, viene elencata anche tale assegnazione di ruolo. Questo filtro è transitivo per i gruppi che significa che se l'utente è un membro di un gruppo e tale gruppo è membro di un altro gruppo che dispone di un'assegnazione di ruolo, viene elencata anche tale assegnazione di ruolo. Questo filtro accetta solo un ID oggetto per un utente o un'entità servizio. Non è possibile passare un ID oggetto per un gruppo. |

## <a name="grant-access"></a>Concedi accesso

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
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per l'assegnazione di ruolo.

    | Ambito | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    | `subscriptions/{subscriptionId1}` | Sottoscrizione |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Risorsa |

1. Sostituire *{roleAssignmentName}* con l'identificatore GUID dell'assegnazione di ruolo.

1. Nel corpo della richiesta sostituire *{scope}* con l'ambito per l'assegnazione di ruolo.

    | Ambito | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    | `subscriptions/{subscriptionId1}` | Sottoscrizione |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Risorsa |

1. Sostituire *{roleDefinitionId}* con l'identificatore della definizione del ruolo.

1. Sostituire *{principalId}* con l'identificatore di oggetto dell'utente, del gruppo o dell'entità servizio a cui verrà assegnato il ruolo.

## <a name="remove-access"></a>Rimuovi accesso

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo. Per rimuovere un'assegnazione di ruolo, usare l'API REST per l'[eliminazione delle assegnazioni di ruolo](/rest/api/authorization/roleassignments/delete). Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/delete`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator).

1. Ottenere l'identificatore di assegnazione di ruolo (GUID). Questo identificatore viene restituito quando si crea l'assegnazione di ruolo per la prima volta oppure è possibile ottenerlo elencando le assegnazioni di ruolo.

1. Iniziare con la richiesta seguente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per la rimozione dell'assegnazione di ruolo.

    | Ambito | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    | `subscriptions/{subscriptionId1}` | Sottoscrizione |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Risorsa |

1. Sostituire *{roleAssignmentName}* con l'identificatore GUID dell'assegnazione di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire le risorse con i modelli e l'API REST di Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Informazioni di riferimento sulle API REST](/rest/api/azure/)
- [Creare ruoli personalizzati per le risorse di Azure tramite l'API REST](custom-roles-rest.md)
