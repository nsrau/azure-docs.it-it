---
title: Aggiungere o rimuovere assegnazioni di ruolo con RBAC e l'API REST
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando il controllo degli accessi in base al ruolo (RBAC) di Azure e l'API REST.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063003"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Aggiungere o rimuovere assegnazioni di ruolo usando RBAC di Azure e l'API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Questo articolo descrive come assegnare i ruoli usando l'API REST.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere o rimuovere assegnazioni di ruolo, è necessario disporre di:

- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) o [Proprietario](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

In RBAC, per concedere l'accesso, si aggiunge un'assegnazione di ruolo. Per aggiungere un'assegnazione di ruolo, usare le [assegnazioni di ruolo-crea](/rest/api/authorization/roleassignments/create) API REST e specificare l'entità di sicurezza, la definizione del ruolo e l'ambito. Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleAssignments/write`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator).

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
    > | Scope | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Risorsa |

    Nell'esempio precedente Microsoft. Web è un provider di risorse che fa riferimento a un'istanza del servizio app. Analogamente, è possibile usare qualsiasi altro provider di risorse e specificare l'ambito. Per altre informazioni, vedere [provider di risorse di Azure e tipi](../azure-resource-manager/management/resource-providers-and-types.md) e [le operazioni supportate Azure Resource Manager provider di risorse](resource-provider-operations.md).  

1. Sostituire *{roleAssignmentName}* con l'identificatore GUID dell'assegnazione di ruolo.

1. Nel corpo della richiesta sostituire *{scope}* con l'ambito per l'assegnazione di ruolo.

    > [!div class="mx-tableFixed"]
    > | Scope | Tipo |
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
    > | Scope | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Risorsa |

1. Sostituire *{roleAssignmentName}* con l'identificatore GUID dell'assegnazione di ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo usando RBAC di Azure e l'API REST](role-assignments-list-rest.md)
- [Distribuire le risorse con i modelli e l'API REST di Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Informazioni di riferimento sull'API REST di Azure](/rest/api/azure/)
- [Creare ruoli personalizzati per le risorse di Azure tramite l'API REST](custom-roles-rest.md)
