---
title: Sicurezza e autenticazione di Griglia di eventi di Azure
description: Vengono descritti il servizio Griglia di eventi di Azure e i concetti correlati.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 5a1e4af17c2f4335ed26490bfc2408c66f4aee6b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328726"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizzazione dell'accesso alle risorse di griglia di eventi
Griglia di eventi di Azure consente di controllare il livello di accesso assegnato a utenti diversi per eseguire varie **operazioni di gestione** , ad esempio elencare sottoscrizioni di eventi, crearne di nuove e generare chiavi. Griglia di eventi usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).

> [!NOTE]
> EventGrid non supporta il controllo degli accessi in base al ruolo di Azure per la pubblicazione di eventi in argomenti o domini Usare una chiave o un token di firma di accesso condiviso per autenticare i client che pubblicano gli eventi. Per altre informazioni, vedere [autenticare i client di pubblicazione](security-authenticate-publishing-clients.md). 

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

Griglia di eventi offre due ruoli predefiniti per la gestione delle sottoscrizioni di eventi. Sono importanti quando si implementano i [domini eventi](event-domains.md) perché forniscono agli utenti le autorizzazioni necessarie per sottoscrivere gli argomenti nel dominio dell'evento. Questi ruoli sono finalizzati soprattutto alle sottoscrizioni di eventi e non concedono l'accesso per azioni come la creazione di argomenti.

È possibile [assegnare questi ruoli a un utente o a un gruppo](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid sottoscrizione evento Contributor**: gestire le operazioni di sottoscrizione di griglia di eventi

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

**EventGrid sottoscrizione evento Reader**: lettura sottoscrizioni di griglia di eventi

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

Tutti gli eventi o i dati scritti sul disco dal servizio Griglia di eventi vengono crittografati con una chiave gestita da Microsoft, per garantire che siano crittografati quando sono inattivi. Il periodo massimo di conservazione di eventi o dati è inoltre pari a 24 ore, in conformità ai [criteri di ripetizione di Griglia di eventi](delivery-and-retry.md). Griglia di eventi eliminerà automaticamente tutti gli eventi o i dati una volta trascorse 24 ore o il tempo impostato per la durata (TTL) dell'evento, a seconda dell'evento che si verifica per primo.

## <a name="permissions-for-event-subscriptions"></a>Autorizzazioni per sottoscrizioni di eventi
Se si usa un gestore eventi che non è un WebHook, ad esempio, un hub eventi o un'archiviazione code, è necessario l'accesso in scrittura a tale risorsa. Questo controllo delle autorizzazioni impedisce che un utente non autorizzato invii eventi alla risorsa.

È necessaria l'autorizzazione **Microsoft.EventGrid/EventSubscriptions/Write** per la risorsa che è l'origine dell'evento. Questa autorizzazione è necessaria perché si sta scrivendo una nuova sottoscrizione nell'ambito della risorsa. La risorsa necessaria è diversa a seconda del fatto che si sottoscriva un argomento di sistema o un argomento personalizzato. Entrambi i tipi sono descritti in questa sezione.

### <a name="system-topics-azure-service-publishers"></a>Argomenti di sistema (entità di pubblicazione dei servizi di Azure)
Per gli argomenti di sistema, è necessaria l'autorizzazione per scrivere una nuova sottoscrizione di evento nell'ambito della risorsa che pubblica l'evento. Il formato della risorsa è: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Per sottoscrivere, ad esempio, un evento in un account di archiviazione denominato **myacct**, è necessaria l'autorizzazione Microsoft.EventGrid/EventSubscriptions/Write per: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Argomenti personalizzati
Per gli argomenti personalizzati, è necessaria l'autorizzazione per scrivere una nuova sottoscrizione di evento nell'ambito dell'argomento di Griglia di eventi. Il formato della risorsa è: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Per sottoscrivere, ad esempio, un argomento personalizzato denominato **mytopic**, è necessaria l'autorizzazione Microsoft.EventGrid/EventSubscriptions/Write per: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
