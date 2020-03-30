---
title: Sicurezza e autenticazione di Griglia di eventi di Azure
description: Vengono descritti il servizio Griglia di eventi di Azure e i concetti correlati.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899282"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizzazione dell'accesso alle risorse di Griglia di eventi
Griglia di eventi di Azure consente di controllare il livello di accesso assegnato ai diversi utenti per eseguire svariate operazioni di gestione, ad esempio elencare sottoscrizioni di eventi, crearne di nuove e generare chiavi. Griglia di eventi usa a questo scopo il controllo degli accessi in base al ruolo di Azure.

## <a name="operation-types"></a>Tipi di operazioni

Griglia di eventi supporta le azioni seguenti:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Le ultime tre operazioni restituiscono informazioni potenzialmente riservate che vengono filtrate dalle normali operazioni di lettura. È consigliabile limitare l'accesso a queste operazioni. 

## <a name="built-in-roles"></a>Ruoli predefiniti

Griglia di eventi offre due ruoli predefiniti per la gestione delle sottoscrizioni di eventi. Sono importanti quando implementano [i domini](event-domains.md) di eventi perché concedono agli utenti le autorizzazioni necessarie per sottoscrivere gli argomenti nel dominio dell'evento. Questi ruoli sono finalizzati soprattutto alle sottoscrizioni di eventi e non concedono l'accesso per azioni come la creazione di argomenti.

È possibile [assegnare questi ruoli a un utente o a un gruppo](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Contributor**: gestire le operazioni di sottoscrizione di Griglia di eventiEventGrid EventSubscription Contributor : manage Event Grid subscription operations

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader**: leggere le sottoscrizioni di Griglia di eventiEventGrid EventSubscription Reader : read Event Grid subscriptions

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Ruoli personalizzati

Se è necessario specificare autorizzazioni diverse rispetto ai ruoli predefiniti, è possibile creare ruoli personalizzati.

Le seguenti sono definizioni di esempio del ruolo di Griglia di eventi che consentono agli utenti di eseguire diverse azioni. Questi ruoli personalizzati sono diversi dai ruoli predefiniti perché garantiscono un accesso più ampio rispetto alle sole sottoscrizioni di eventi.

**EventGridReadOnlyRole.json**: consente solo operazioni di sola lettura.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: consente le azioni di pubblicazione con restrizioni, ma non consente le azioni di eliminazione.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: consente tutte le azioni di Griglia di eventi.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

È possibile creare ruoli personalizzati con [PowerShell](../role-based-access-control/custom-roles-powershell.md), l'[interfaccia della riga di comando di Azure](../role-based-access-control/custom-roles-cli.md) e [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

Tutti gli eventi o i dati scritti su disco dal servizio Griglia di eventi vengono crittografati da una chiave gestita da Microsoft, assicurandosi che sia crittografata inattivi. Inoltre, il periodo di tempo massimo per cui gli eventi o i dati conservati sono 24 ore in conformità con il [criterio di ripetizione della griglia di](delivery-and-retry.md)eventi. Griglia di eventi eliminerà automaticamente tutti gli eventi o i dati dopo 24 ore o la time-to-live dell'evento, a seconda del valore minore.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni sulla griglia di eventiFor](overview.md) an introduction to Event Grid, see About Event Grid
