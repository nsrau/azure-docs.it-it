---
title: Creare o aggiornare i ruoli personalizzati di Azure usando l'API REST-RBAC di Azure
description: Informazioni su come elencare, creare, aggiornare o eliminare i ruoli personalizzati di Azure usando l'API REST e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
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
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b459f44308827308c28687db3c3fc33df470ea8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84790189"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>Creare o aggiornare i ruoli personalizzati di Azure usando l'API REST

> [!IMPORTANT]
> L'aggiunta di un gruppo di gestione a `AssignableScopes` è attualmente in fase di anteprima.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se i [ruoli predefiniti](built-in-roles.md) di Azure non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Questo articolo descrive come elencare, creare, aggiornare o eliminare i ruoli personalizzati usando l'API REST.

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Per elencare tutti i ruoli personalizzati in una directory, usare l'API REST per l' [elenco delle definizioni di ruolo](/rest/api/authorization/roledefinitions/list) .

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Sostituire *{Filter}* con il tipo di ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtrare in base al tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Elencare i ruoli personalizzati in un ambito

Per elencare i ruoli personalizzati in un ambito, usare l'API REST per l' [elenco delle definizioni di ruolo](/rest/api/authorization/roledefinitions/list) .

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare i ruoli.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Risorsa |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |

1. Sostituire *{Filter}* con il tipo di ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtrare in base al tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Elencare una definizione di ruolo personalizzata in base al nome

Per ottenere informazioni su un ruolo personalizzato in base al nome visualizzato, usare l'API REST per le [definizioni di ruolo](/rest/api/authorization/roledefinitions/get) .

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare i ruoli.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Risorsa |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |

1. Sostituire *{Filter}* con il nome visualizzato per il ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Usare il form con codifica URL dell'esatto nome visualizzato del ruolo. Ad esempio: `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Elencare una definizione di ruolo personalizzata in base all'ID

Per ottenere informazioni su un ruolo personalizzato in base al relativo identificatore univoco, usare l'API REST per le [definizioni di ruolo](/rest/api/authorization/roledefinitions/get) .

1. Usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) per ottenere l'identificatore del GUID del ruolo.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per il quale elencare i ruoli.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Risorsa |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |

1. Sostituire *{roleDefinitionId}* con l'identificatore GUID della definizione di ruolo.

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Per creare un ruolo personalizzato, usare l'API REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate) (Definizione dei ruoli - Crea o Aggiorna). Per chiamare questa API, è necessario aver eseguito l'accesso con un utente a cui è assegnato un ruolo che dispone dell' `Microsoft.Authorization/roleDefinitions/write` autorizzazione per tutti i `assignableScopes` . Tra i ruoli predefiniti, solo [proprietario](built-in-roles.md#owner) e [amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. All'interno dell'URI sostituire *{scope}* con il primo `assignableScopes` del ruolo personalizzato.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |

1. Sostituire *{roleDefinitionId}* con l'identificatore GUID del ruolo personalizzato.

1. Nel corpo della richiesta sostituire *{roleDefinitionId}* con l'identificatore GUID.

1. Se `assignableScopes` è una sottoscrizione o un gruppo di risorse, sostituire le istanze *{SubscriptionId}* o *{resourceGroup}* con gli identificatori.

1. Se `assignableScopes` è un gruppo di gestione, sostituire l'istanza *{GroupID}* con l'identificatore del gruppo di gestione. L'aggiunta di un gruppo di gestione a `assignableScopes` è attualmente in fase di anteprima.

1. Nella proprietà `actions` aggiungere le operazioni di cui il ruolo consente l'esecuzione.

1. Nella proprietà `notActions` aggiungere le operazioni escluse dalle `actions` consentite.

1. Nelle proprietà `roleName` e `description` specificare un nome e una descrizione univoci per il ruolo. Per altre informazioni sulle proprietà, vedere [ruoli personalizzati di Azure](custom-roles.md).

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Per aggiornare un ruolo personalizzato, usare l'API REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate) (Definizione dei ruoli - Crea o Aggiorna). Per chiamare questa API, è necessario aver eseguito l'accesso con un utente a cui è assegnato un ruolo che dispone dell' `Microsoft.Authorization/roleDefinitions/write` autorizzazione per tutti i `assignableScopes` . Tra i ruoli predefiniti, solo [proprietario](built-in-roles.md#owner) e [amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

1. Per ottenere informazioni sul ruolo personalizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per ulteriori informazioni, vedere la sezione precedente [elenco ruoli personalizzati](#list-custom-roles) .

1. Iniziare con la richiesta seguente:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con il primo `assignableScopes` del ruolo personalizzato.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |

1. Sostituire *{roleDefinitionId}* con l'identificatore GUID del ruolo personalizzato.

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare l'API REST [Role Definitions - Delete](/rest/api/authorization/roledefinitions/delete) (Definizione dei ruoli - Elimina). Per chiamare questa API, è necessario aver eseguito l'accesso con un utente a cui è assegnato un ruolo che dispone dell' `Microsoft.Authorization/roleDefinitions/delete` autorizzazione per tutti i `assignableScopes` . Tra i ruoli predefiniti, solo [proprietario](built-in-roles.md#owner) e [amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

1. Per ottenere informazioni sull'identificatore del GUID del ruolo personalizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per ulteriori informazioni, vedere la sezione precedente [elenco ruoli personalizzati](#list-custom-roles) .

1. Iniziare con la richiesta seguente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito in cui si vuole eliminare il ruolo personalizzato.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |

1. Sostituire *{roleDefinitionId}* con l'identificatore GUID del ruolo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

- [Ruoli personalizzati di Azure](custom-roles.md)
- [Aggiungere o rimuovere assegnazioni di ruolo di Azure tramite l'API REST](role-assignments-rest.md)
- [Informazioni di riferimento sull'API REST di Azure](/rest/api/azure/)
