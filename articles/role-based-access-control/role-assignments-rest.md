---
title: Aggiungere o rimuovere assegnazioni di ruolo con RBAC e l'API RESTAdd or remove role assignments with RBAC and the REST API
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando il controllo degli accessi in base al ruolo di Azure e l'API REST.
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
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063003"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Aggiungere o rimuovere assegnazioni di ruolo usando controllo degli accessi in base al ruolo di Azure e l'API RESTAdd or remove role assignments using Azure RBAC and the REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Questo articolo descrive come assegnare ruoli usando l'API REST.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:To add or remove role assignments, you must have:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

In RBAC, per concedere l'accesso, si aggiunge un'assegnazione di ruolo. Per aggiungere un'assegnazione di ruolo, usare l'API REST [Role Assignments - Create](/rest/api/authorization/roleassignments/create) e specificare l'entità di sicurezza, la definizione del ruolo e l'ambito. Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/write`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator).

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

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Risorsa |

    Nell'esempio precedente, microsoft.web è un provider di risorse che fa riferimento a un'istanza del servizio app. Analogamente, è possibile utilizzare qualsiasi altro provider di risorse e specificare l'ambito. Per altre informazioni, vedere [Tipi e provider di risorse](../azure-resource-manager/management/resource-providers-and-types.md) di Azure e Operazioni del provider di risorse di Azure Resource [Manager](resource-provider-operations.md)supportate.  

1. Sostituire *{roleAssignmentName}* con l'identificatore GUID dell'assegnazione di ruolo.

1. All'interno del corpo della richiesta, sostituire *l'ambito* con l'ambito per l'assegnazione di ruolo.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Risorsa |

1. Sostituire *{roleDefinitionId}* con l'identificatore della definizione del ruolo.

1. Sostituire *{principalId}* con l'identificatore di oggetto dell'utente, del gruppo o dell'entità servizio a cui verrà assegnato il ruolo.

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo. Per rimuovere un'assegnazione di ruolo, usare l'API REST per l'[eliminazione delle assegnazioni di ruolo](/rest/api/authorization/roleassignments/delete). Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/delete`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator).

1. Ottenere l'identificatore di assegnazione di ruolo (GUID). Questo identificatore viene restituito quando si crea l'assegnazione di ruolo per la prima volta oppure è possibile ottenerlo elencando le assegnazioni di ruolo.

1. Iniziare con la richiesta seguente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per la rimozione dell'assegnazione di ruolo.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Risorsa |

1. Sostituire *{roleAssignmentName}* con l'identificatore GUID dell'assegnazione di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo usando il controllo degli accessi in base al ruolo di Azure e l'API RESTList role assignments using Azure RBAC and the REST API](role-assignments-list-rest.md)
- [Distribuire le risorse con i modelli e l'API REST di Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Guida di riferimento all'API REST di AzureAzure REST API Reference](/rest/api/azure/)
- [Creare ruoli personalizzati per le risorse di Azure tramite l'API REST](custom-roles-rest.md)
