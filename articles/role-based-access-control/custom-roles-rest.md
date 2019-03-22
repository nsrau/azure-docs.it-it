---
title: Creare ruoli personalizzati per le risorse di Azure usando l'API REST - Azure | Microsoft Docs
description: Informazioni su come creare ruoli personalizzati con il controllo degli accessi in base al ruolo per le risorse di Azure tramite l'API REST. Viene illustrato come elencare, creare, aggiornare ed eliminare ruoli personalizzati.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cec75f757789be4f962cf2b0fbf6b9443a4453cc
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588195"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Creare ruoli personalizzati per le risorse di Azure tramite l'API REST

Se i [ruoli predefiniti per le risorse di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Questo articolo descrive come creare e gestire ruoli personalizzati con l'API REST.

## <a name="list-roles"></a>Elenco dei ruoli

Per elencare tutti i ruoli o ottenere informazioni su un singolo ruolo usando il relativo nome visualizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca). Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/read` nell'ambito. L'accesso a questa operazione viene concesso a diversi [ruoli predefiniti](built-in-roles.md).

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare i ruoli.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{filter}* con la condizione da applicare per filtrare l'elenco di ruoli.

    | Filtro | DESCRIZIONE |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Elencare i ruoli disponibili per l'assegnazione nell'ambito specificato e in tutti i relativi ambiti figlio. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Usare il form con codifica URL dell'esatto nome visualizzato del ruolo. Ad esempio: `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Ottenere informazioni su un ruolo

Per ottenere informazioni su un ruolo mediante il relativo identificatore di definizione del ruolo, usare l'API REST [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/read` nell'ambito. L'accesso a questa operazione viene concesso a diversi [ruoli predefiniti](built-in-roles.md).

Per ottenere informazioni su un singolo ruolo usando il relativo nome visualizzato, vedere la sezione [Elenco dei ruoli](custom-roles-rest.md#list-roles).

1. Usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) per ottenere l'identificatore del GUID del ruolo. Per quanto riguarda i ruoli predefiniti, è possibile ottenere l'identificatore anche da [Ruoli predefiniti](built-in-roles.md).

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare i ruoli.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{roleDefinitionId}* con l'identificatore del GUID della definizione di ruolo.

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Per creare un ruolo personalizzato, usare l'API REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate) (Definizione dei ruoli - Crea o Aggiorna). Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/write` in tutti gli `assignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator). 

1. Esaminare l'elenco delle [operazioni del provider di risorse](resource-provider-operations.md) disponibili per creare le autorizzazioni per il ruolo personalizzato.

1. Usare uno strumento GUID per generare un identificatore univoco che verrà usato per l'identificatore del ruolo personalizzato. Il formato dell'identificatore è: `00000000-0000-0000-0000-000000000000`

1. Iniziare con la richiesta e il corpo seguenti:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. All'interno dell'URI sostituire *{scope}* con il primo `assignableScopes` del ruolo personalizzato.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{roleDefinitionId}* con l'identificatore del GUID del ruolo personalizzato.

1. Nella proprietà `assignableScopes` nel corpo della richiesta sostituire *{roleDefinitionId}* con l'identificatore del GUID.

1. Sostituire *{subscriptionId}* con l'identificatore della sottoscrizione.

1. Nella proprietà `actions` aggiungere le operazioni di cui il ruolo consente l'esecuzione.

1. Nella proprietà `notActions` aggiungere le operazioni escluse dalle `actions` consentite.

1. Nelle proprietà `roleName` e `description` specificare un nome e una descrizione univoci per il ruolo. Per altre informazioni sulle proprietà, vedere [Ruoli personalizzati](custom-roles.md).

    Di seguito è illustrato un esempio di corpo della richiesta:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Per aggiornare un ruolo personalizzato, usare l'API REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate) (Definizione dei ruoli - Crea o Aggiorna). Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/write` in tutti gli `assignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator). 

1. Per ottenere informazioni sul ruolo personalizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per altre informazioni, vedere la sezione [Elenco dei ruoli](custom-roles-rest.md#list-roles) più indietro.

1. Iniziare con la richiesta seguente:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con il primo `assignableScopes` del ruolo personalizzato.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{roleDefinitionId}* con l'identificatore del GUID del ruolo personalizzato.

1. In base alle informazioni sul ruolo personalizzato, creare un corpo della richiesta con il formato seguente:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Aggiornare il corpo della richiesta con le modifiche che si vogliono apportare al ruolo personalizzato.

    Di seguito è illustrato un esempio di corpo della richiesta a cui è stata aggiunta una nuova azione di impostazioni di diagnostica:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare l'API REST [Role Definitions - Delete](/rest/api/authorization/roledefinitions/delete) (Definizione dei ruoli - Elimina). Per chiamare questa API, è necessario avere accesso all'operazione `Microsoft.Authorization/roleDefinitions/delete` in tutti gli `assignableScopes`. Tra i ruoli predefiniti, l'accesso a questa operazione viene concesso soltanto ai ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator). 

1. Per ottenere informazioni sull'identificatore del GUID del ruolo personalizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per altre informazioni, vedere la sezione [Elenco dei ruoli](custom-roles-rest.md#list-roles) più indietro.

1. Iniziare con la richiesta seguente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito in cui si vuole eliminare il ruolo personalizzato.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Risorsa |

1. Sostituire *{roleDefinitionId}* con l'identificatore del GUID del ruolo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

- [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e l'API REST](role-assignments-rest.md)
- [Informazioni di riferimento sulle API REST](/rest/api/azure/)