---
title: Informazioni sulle definizioni di ruolo in Controllo degli accessi in base al ruolo di Azure | Microsoft Docs
description: Informazioni sulle definizioni di ruolo in Controllo degli accessi in base al ruolo e su come definire ruoli personalizzati per la gestione delle autorizzazioni di accesso con granularità fine delle risorse in Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161814"
---
# <a name="understand-role-definitions"></a>Informazioni sulle definizioni di ruolo

Per capire come funziona un ruolo o creare un [ruolo personalizzato](custom-roles.md), è utile comprendere il modo in cui vengono definiti i ruoli. Questo articolo illustra in dettaglio le definizioni di ruolo e presenta alcuni esempi.

## <a name="role-definition-structure"></a>Struttura delle definizioni di ruolo

Una *definizione di ruolo* è una raccolta di autorizzazioni, talvolta semplicemente chiamata *ruolo*. Una definizione di ruolo elenca le operazioni che è possibile eseguire, ad esempio lettura, scrittura ed eliminazione, ma può anche elencare le operazioni non consentite. Una definizione di ruolo presenta la struttura seguente:

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Le operazioni vengono specificate con stringhe che hanno il formato seguente:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

La parte `{action}` di una stringa relativa a un'operazione specifica il tipo di operazioni che è possibile eseguire su un tipo di risorsa. In `{action}` possono ad esempio essere elencate le sottostringhe seguenti:

| Sottostringa azione    | Descrizione         |
| ------------------- | ------------------- |
| `*` | Il carattere jolly concede l'accesso a tutte le operazioni che corrispondono alla stringa. |
| `read` | Abilita le operazioni di lettura (GET). |
| `write` | Abilita le operazioni di scrittura (PUT, POST e PATCH). |
| `delete` | Abilita le operazioni di eliminazione (DELETE). |

Di seguito è riportata la definizione del ruolo [Collaboratore](built-in-roles.md#contributor) in formato JSON. L'operazione identificata dal carattere jolly (`*`) in `actions` indica che l'entità di sicurezza assegnata a questo ruolo può eseguire qualsiasi azione, anche quelle definite in futuro in seguito all'aggiunta di nuovi tipi di risorse da parte di Azure. Le operazioni sotto `notActions` vengono sottratte a `actions`. Nel caso del ruolo [Collaboratore](built-in-roles.md#contributor), `notActions` rimuove la capacità di gestire e assegnare l'accesso alle risorse.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Operazioni di gestione

Il controllo degli accessi in base al ruolo per le operazioni di gestione è specificato nelle sezioni `actions` e `notActions` di una definizione di ruolo. Di seguito sono riportati alcuni esempi di operazioni di gestione in Azure:

- Gestire l'accesso a un account di archiviazione
- Creare, aggiornare o eliminare un contenitore BLOB
- Eliminare un gruppo di risorse e tutte le risorse incluse nel gruppo

L'accesso in modalità di gestione non viene ereditato nei dati. Questa separazione impedisce ai ruoli con caratteri jolly (`*`) di avere accesso illimitato ai dati. Se ad esempio un utente ha un ruolo [Lettore](built-in-roles.md#reader) in una sottoscrizione, può visualizzare l'account di archiviazione, ma non può visualizzare i dati sottostanti.

## <a name="actions"></a>actions

L'autorizzazione `actions` specifica le operazioni di gestione a cui il ruolo concede l'accesso. Si tratta di una raccolta di stringhe di operazione che identificano operazioni a protezione diretta dei provider di risorse di Azure. Di seguito sono riportati alcuni esempi di operazioni di gestione che possono essere usate in `actions`.

| Stringa operazione    | Descrizione         |
| ------------------- | ------------------- |
| `*/read` | Concede l'accesso a operazioni di lettura per tutti i tipi di risorse di tutti i provider di risorse di Azure.|
| `Microsoft.Compute/*` | Concede l'accesso a tutte le operazioni per tutti i tipi di risorse nel provider di risorse Microsoft.Compute.|
| `Microsoft.Network/*/read` | Concede l'accesso alle operazioni di lettura per tutti i tipi di risorse nel provider di risorse Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | Concede l'accesso a tutte le operazioni delle macchine virtuali e ai relativi tipi di risorse figlio.|
| `microsoft.web/sites/restart/Action` | Concede l'accesso per il riavvio di un'app Web.|

## <a name="notactions"></a>notActions

L'autorizzazione `notActions` specifica le operazioni di gestione che sono escluse dall'autorizzazione `actions`. Usare l'autorizzazione `notActions` se il set di operazioni che si vuole consentire viene definito più facilmente escludendo le operazioni non consentite. L'accesso concesso da un ruolo (ovvero le autorizzazioni effettive) viene calcolato sottraendo le operazioni `notActions` alle operazioni `actions`.

> [!NOTE]
> Se a un utente si assegna un ruolo che esclude un'operazione in `notActions` e quindi si assegna un secondo ruolo che concede l'accesso alla stessa operazione, l'utente può eseguire tale operazione. `notActions` non è una regola di negazione. È semplicemente un modo comodo per creare un set di operazioni consentite quando è necessario escludere operazioni specifiche.
>

## <a name="assignablescopes"></a>assignableScopes

La sezione `assignableScopes` specifica gli ambiti (gruppi di gestione, attualmente in anteprima, sottoscrizioni, gruppi di risorse o risorse) in cui il ruolo è disponibile per l'assegnazione. È possibile rendere disponibile il ruolo per l'assegnazione solo nelle sottoscrizioni o nei gruppi di risorse che lo richiedono, in modo da non complicare l'esperienza utente per le altre sottoscrizioni o gli altri gruppi di risorse. È necessario usare almeno un gruppo di gestione, una sottoscrizione, un gruppo di risorse o un ID di risorsa.

Per i ruoli predefiniti, `assignableScopes` è impostato sull'ambito radice (`"/"`), per indicare che il ruolo è disponibile per l'assegnazione in tutti gli ambiti. Non è possibile usare l'ambito radice nei ruoli personalizzati. Se si esegue questo tentativo, si ottiene un errore di autorizzazione.

Ecco alcuni esempi di ambiti assegnabili validi:

| Scenario | Esempio |
|----------|---------|
| Ruolo disponibile per l'assegnazione in una singola sottoscrizione | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Ruolo disponibile per l'assegnazione in due sottoscrizioni | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Ruolo disponibile per l'assegnazione solo nel gruppo di risorse Network | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Ruolo disponibile per l'assegnazione in tutti gli ambiti | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes e ruoli personalizzati

La sezione `assignableScopes` per un ruolo personalizzato controlla anche chi può creare, eliminare, modificare o visualizzare il ruolo personalizzato.

| Attività | Operazione | Descrizione |
| --- | --- | --- |
| Creare o eliminare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/write` | Gli utenti a cui viene concessa questa operazione su tutti gli ambiti `assignableScopes` del ruolo personalizzato possono creare (o eliminare) ruoli personalizzati da usare in tali ambiti. Ad esempio, i ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) di sottoscrizioni, gruppi di risorse e risorse. |
| Modificare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/write` | Gli utenti a cui viene concessa questa autorizzazione su tutti gli ambiti `assignableScopes` del ruolo personalizzato possono modificare ruoli personalizzati in tali ambiti. Ad esempio, i ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) di sottoscrizioni, gruppi di risorse e risorse. |
| Visualizzare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/read` | Gli utenti a cui viene concessa questa operazione a livello di ambito possono visualizzare i ruoli personalizzati disponibili per l'assegnazione in tale ambito. Tutti i ruoli predefiniti consentono la disponibilità dei ruoli personalizzati per l'assegnazione. |

## <a name="role-definition-examples"></a>Esempi di definizioni di ruolo

L'esempio seguente mostra la definizione del ruolo [Lettore](built-in-roles.md#reader), come visualizzata tramite l'interfaccia della riga di comando di Azure:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

L'esempio seguente mostra un ruolo personalizzato per il monitoraggio e il riavvio di macchine virtuali, come visualizzato tramite Azure PowerShell:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Vedere anche 

* [Ruoli predefiniti](built-in-roles.md)
* [Ruoli personalizzati](custom-roles.md)
* [Operazioni di provider di risorse con Azure Resource Manager](resource-provider-operations.md)
