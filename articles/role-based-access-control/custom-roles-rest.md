---
title: Creare o aggiornare ruoli personalizzati per le risorse di Azure con l'API RESTCreate or update custom roles for Azure resources with the REST API
description: Informazioni su come elencare, creare, aggiornare o eliminare ruoli personalizzati con il controllo degli accessi in base al ruolo per le risorse di Azure usando l'API REST.
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
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062197"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Creare o aggiornare ruoli personalizzati per le risorse di Azure usando l'API RESTCreate or update custom roles for Azure resources using the REST API

> [!IMPORTANT]
> L'aggiunta di `AssignableScopes` un gruppo di gestione è attualmente in anteprima.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se i [ruoli predefiniti per](built-in-roles.md) le risorse di Azure non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Questo articolo descrive come elencare, creare, aggiornare o eliminare ruoli personalizzati usando l'API REST.

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Per elencare tutti i ruoli personalizzati in una directory, usare l'API REST [Definizioni ruolo - Elenco.To](/rest/api/authorization/roledefinitions/list) list all custom roles in a directory, use the Role Definitions - List REST API.

1. Iniziare con la richiesta seguente:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. *Sostituirlo* con il tipo di ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtrare in base al tipo CustomRoleFilter based on the CustomRole type |

## <a name="list-custom-roles-at-a-scope"></a>Elencare ruoli personalizzati in un ambitoList custom roles at a scope

Per elencare i ruoli personalizzati in un ambito, usare l'API REST [Definizioni ruolo - Elenco.To](/rest/api/authorization/roledefinitions/list) list custom roles at a scope, use the Role Definitions - List REST API.

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

1. *Sostituirlo* con il tipo di ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtrare in base al tipo CustomRoleFilter based on the CustomRole type |

## <a name="list-a-custom-role-definition-by-name"></a>Elencare una definizione di ruolo personalizzata in base al nomeList a custom role definition by name

Per ottenere informazioni su un ruolo personalizzato in base al nome visualizzato, usare l'API REST [Definizioni ruolo - Ottieni.](/rest/api/authorization/roledefinitions/get)

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

1. *Sostituirlo* con il nome visualizzato per il ruolo.

    > [!div class="mx-tableFixed"]
    > | Filtro | Descrizione |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Usare il form con codifica URL dell'esatto nome visualizzato del ruolo. Ad esempio: `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Elencare una definizione di ruolo personalizzato in base all'IDList a custom role definition by ID

Per ottenere informazioni su un ruolo personalizzato in base al relativo identificatore univoco, usare l'API REST [Definizioni ruolo - Ottieni.To](/rest/api/authorization/roledefinitions/get) get information about a custom role by its unique identifier, use the Role Definitions - Get REST API.

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

1. *Sostituirlo* con l'identificatore GUID della definizione del ruolo.

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Per creare un ruolo personalizzato, usare l'API REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate) (Definizione dei ruoli - Crea o Aggiorna). Per chiamare questa API, è necessario accedere con un utente `Microsoft.Authorization/roleDefinitions/write` a cui `assignableScopes`è assegnato un ruolo che dispone dell'autorizzazione per tutti i file . Dei ruoli predefiniti, solo [Proprietario](built-in-roles.md#owner) e [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

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

1. *Sostituirlo* con l'identificatore GUID del ruolo personalizzato.

1. All'interno del corpo della richiesta, sostituire l'id GUID *roleDefinitionId* con l'identificatore GUID.

1. Se `assignableScopes` si tratta di una sottoscrizione o di un gruppo di risorse, sostituire le istanze di *"subscriptionId"* o *"resourceGroup"* con gli identificatori.

1. Se `assignableScopes` si tratta di un gruppo di gestione, sostituire l'istanza di * groupId * con l'identificatore del gruppo di gestione. L'aggiunta di `assignableScopes` un gruppo di gestione è attualmente in anteprima.

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Per aggiornare un ruolo personalizzato, usare l'API REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate) (Definizione dei ruoli - Crea o Aggiorna). Per chiamare questa API, è necessario accedere con un utente `Microsoft.Authorization/roleDefinitions/write` a cui `assignableScopes`è assegnato un ruolo che dispone dell'autorizzazione per tutti i file . Dei ruoli predefiniti, solo [Proprietario](built-in-roles.md#owner) e [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

1. Per ottenere informazioni sul ruolo personalizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per altre informazioni, vedere la sezione [Elenco ruoli personalizzati](#list-custom-roles) precedente.

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

1. *Sostituirlo* con l'identificatore GUID del ruolo personalizzato.

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

Per eliminare un ruolo personalizzato, usare l'API REST [Role Definitions - Delete](/rest/api/authorization/roledefinitions/delete) (Definizione dei ruoli - Elimina). Per chiamare questa API, è necessario accedere con un utente `Microsoft.Authorization/roleDefinitions/delete` a cui `assignableScopes`è assegnato un ruolo che dispone dell'autorizzazione per tutti i file . Dei ruoli predefiniti, solo [Proprietario](built-in-roles.md#owner) e [Amministratore accesso utenti](built-in-roles.md#user-access-administrator) includono questa autorizzazione.

1. Per ottenere informazioni sull'identificatore del GUID del ruolo personalizzato, usare l'API REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) (Definizione dei ruoli - Elenca) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) (Definizione dei ruoli - Ottieni). Per altre informazioni, vedere la sezione [Elenco ruoli personalizzati](#list-custom-roles) precedente.

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

1. *Sostituirlo* con l'identificatore GUID del ruolo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

- [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e l'API REST](role-assignments-rest.md)
- [Guida di riferimento all'API REST di AzureAzure REST API Reference](/rest/api/azure/)
