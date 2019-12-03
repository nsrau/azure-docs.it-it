---
title: Creare ruoli personalizzati o di aggiornamento per le risorse di Azure usando l'API REST-Azure | Microsoft Docs
description: Informazioni su come elencare, creare, aggiornare o eliminare ruoli personalizzati con il controllo degli accessi in base al ruolo (RBAC) per le risorse di Azure usando l'API REST.
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2e5dcd7593ba0992337396bc7c05cc30351644dc
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702972"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Creare o aggiornare i ruoli personalizzati per le risorse di Azure usando l'API REST

Se i [ruoli predefiniti per le risorse di Azure](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Questo articolo descrive come elencare, creare, aggiornare o eliminare i ruoli personalizzati usando l'API REST.

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Per elencare tutti i ruoli personalizzati in una directory, usare l'API REST per l' [elenco delle definizioni di ruolo](/rest/api/authorization/roledefinitions/list) .

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Sostituire *{Filter}* con il tipo di ruolo.

    | Filtra | Description |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrare in base al tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Elencare i ruoli personalizzati in un ambito

Per elencare i ruoli personalizzati in un ambito, usare l'API REST per l' [elenco delle definizioni di ruolo](/rest/api/authorization/roledefinitions/list) .

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare i ruoli.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Gruppi |

1. Sostituire *{Filter}* con il tipo di ruolo.

    | Filtra | Description |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrare in base al tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Elencare una definizione di ruolo personalizzata in base al nome

Per ottenere informazioni su un ruolo personalizzato in base al nome visualizzato, usare l'API REST per le [definizioni di ruolo](/rest/api/authorization/roledefinitions/get) .

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare i ruoli.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Gruppi |

1. Sostituire *{Filter}* con il nome visualizzato per il ruolo.

    | Filtra | Description |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Usare il form con codifica URL dell'esatto nome visualizzato del ruolo. Ad esempio: `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Elencare una definizione di ruolo personalizzata in base all'ID

Per ottenere informazioni su un ruolo personalizzato in base al relativo identificatore univoco, usare l'API REST per le [definizioni di ruolo](/rest/api/authorization/roledefinitions/get) .

1. Usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) per ottenere l'identificatore del GUID del ruolo.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare i ruoli.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Gruppi |

1. Sostituire *{roleDefinitionId}* con l'identificatore del GUID della definizione di ruolo.

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Per creare un ruolo personalizzato, usare l'API REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate) (Definizione dei ruoli - Crea o Aggiorna). Per chiamare questa API, è necessario aver eseguito l'accesso con un utente a cui è assegnato un ruolo con l'autorizzazione `Microsoft.Authorization/roleDefinitions/write` per tutti i `assignableScopes`. Tra i ruoli predefiniti, solo [proprietario](built-in-roles.md#owner) e [amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

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
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Gruppi |

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

Per aggiornare un ruolo personalizzato, usare l'API REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate) (Definizione dei ruoli - Crea o Aggiorna). Per chiamare questa API, è necessario aver eseguito l'accesso con un utente a cui è assegnato un ruolo con l'autorizzazione `Microsoft.Authorization/roleDefinitions/write` per tutti i `assignableScopes`. Tra i ruoli predefiniti, solo [proprietario](built-in-roles.md#owner) e [amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

1. Per ottenere informazioni sul ruolo personalizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per ulteriori informazioni, vedere la sezione precedente [elenco ruoli personalizzati](#list-custom-roles) .

1. Iniziare con la richiesta seguente:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con il primo `assignableScopes` del ruolo personalizzato.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Gruppi |

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

Per eliminare un ruolo personalizzato, usare l'API REST [Role Definitions - Delete](/rest/api/authorization/roledefinitions/delete) (Definizione dei ruoli - Elimina). Per chiamare questa API, è necessario aver eseguito l'accesso con un utente a cui è assegnato un ruolo con l'autorizzazione `Microsoft.Authorization/roleDefinitions/delete` per tutti i `assignableScopes`. Tra i ruoli predefiniti, solo [proprietario](built-in-roles.md#owner) e [amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

1. Per ottenere informazioni sull'identificatore del GUID del ruolo personalizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per ulteriori informazioni, vedere la sezione precedente [elenco ruoli personalizzati](#list-custom-roles) .

1. Iniziare con la richiesta seguente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito in cui si vuole eliminare il ruolo personalizzato.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Sottoscrizione |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Gruppo di risorse |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Gruppi |

1. Sostituire *{roleDefinitionId}* con l'identificatore del GUID del ruolo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

- [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e l'API REST](role-assignments-rest.md)
- [Informazioni di riferimento sulle API REST](/rest/api/azure/)