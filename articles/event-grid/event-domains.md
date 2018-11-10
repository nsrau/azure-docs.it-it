---
title: Domini eventi in Griglia di eventi di Azure
description: Descrive come usare i domini eventi per gestire gli argomenti in Griglia di eventi di Azure.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669328"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Informazioni sui domini eventi per la gestione degli argomenti di Griglia di eventi

Questo articolo descrive come usare i domini eventi per gestire il flusso di eventi personalizzati per clienti, organizzazioni o applicazioni aziendali. Usare i domini eventi per:

* Gestire architetture di gestione degli eventi su larga scala.
* Gestire l'autenticazione e l'autorizzazione.
* Partizionare gli argomenti senza doverli gestire singolarmente.
* Evitare di dover pubblicare singolarmente in ogni endpoint degli argomenti.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>Panoramica dei domini eventi

Un dominio eventi è uno strumento per la gestione di un numero elevato di argomenti di Griglia di eventi correlati alla stessa applicazione. Si può paragonare a un meta-argomento che può contenere migliaia di singoli argomenti.

I domini eventi costituiscono l'architettura usata da servizi di Azure come Archiviazione e l'hub IoT per pubblicare i relativi eventi e renderli disponibili per l'uso nel sistema dell'utente. Consentono di pubblicare eventi in migliaia di argomenti. I domini offrono anche il controllo dell'autenticazione e dell'autorizzazione su ogni argomento, consentendo di partizionare i tenant.

### <a name="example-use-case"></a>Esempio di caso d'uso

I domini eventi si possono comprendere più facilmente con un esempio. Si supponga di gestire Contoso Construction Machinery, che produce trattori, attrezzature di scavo e altri macchinari pesanti. Come parte della gestione dell'azienda, si esegue il push verso i clienti di informazioni in tempo reale riguardanti la manutenzione delle apparecchiature, l'integrità dei sistemi, gli aggiornamenti dei contratti e così via. Tutte queste informazioni vengono inoltrate a vari endpoint, tra cui l'app, gli endpoint dei clienti e altre infrastrutture configurate dai clienti.

I domini eventi consentono di modellare Contoso Construction Machinery come una singola entità di gestione degli eventi. Ogni cliente è rappresentato come un argomento all'interno del dominio. L'autenticazione e l'autorizzazione vengono gestite tramite Azure Active Directory. Ogni cliente può sottoscrivere il proprio argomento e ricevere i relativi eventi. L'accesso gestito tramite il dominio eventi garantisce che il cliente possa accedere solo al proprio argomento.

Offre inoltre un singolo endpoint, in cui è possibile pubblicare tutti gli eventi dei clienti. Griglia di eventi si occuperà di assicurare che ogni argomento venga a conoscenza solo degli eventi con ambito a livello del proprio tenant.

![Esempio di Contoso Construction](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>gestione degli accessi

 Con un dominio si ottiene il controllo granulare di autenticazione e autorizzazione su ogni argomento, tramite il controllo degli accessi in base al ruolo di Azure. È possibile usare questi ruoli per limitare ogni tenant nell'applicazione ai soli argomenti a cui si vuole concedere l'accesso.

Il controllo degli accessi in base al ruolo nei domini eventi funziona allo stesso modo del [controllo di accesso gestito](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control) nel resto di Griglia di eventi e di Azure. Usare il controllo degli accessi in base al ruolo per creare e applicare definizioni del ruolo personalizzate nei domini eventi.

### <a name="built-in-roles"></a>Ruoli predefiniti

Griglia di eventi include due definizioni del ruolo predefinite per semplificare il controllo degli accessi in base al ruolo:

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>EventGrid EventSubscription Contributor (Preview) (Collaboratore EventGrid EventSubscription - anteprima)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
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

#### <a name="eventgrid-eventsubscription-reader-preview"></a>EventGrid EventSubscription Reader (Preview) (Lettore EventGrid EventSubscription - anteprima)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
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

## <a name="subscribing-to-topics"></a>Sottoscrizione di argomenti

Sottoscrivere eventi in un argomento all'interno di un dominio eventi equivale a [creare una sottoscrizione di eventi in un argomento personalizzato](./custom-event-quickstart.md) oppure a sottoscrivere uno qualsiasi degli autori di eventi predefiniti offerti da Azure.

### <a name="domain-scope-subscriptions"></a>Sottoscrizioni di ambiti di dominio

I domini eventi consentono anche di sottoscrivere ambiti di dominio. Una sottoscrizione di eventi in un dominio eventi riceverà tutti gli eventi inviati al dominio, indipendentemente dall'argomento al quale gli eventi vengono inviati. Le sottoscrizioni di ambiti di dominio possono essere utili a scopi di gestione e controllo.

## <a name="publishing-to-an-event-domain"></a>Pubblicazione in un dominio eventi

Quando si crea un dominio eventi, si riceve un endpoint di pubblicazione come se si fosse creato un argomento in Griglia di eventi. 

Per pubblicare eventi in qualsiasi argomento di un dominio eventi, eseguire il push degli eventi all'endpoint del dominio [esattamente come si farebbe per un argomento personalizzato](./post-to-custom-topic.md). L'unica differenza è che è necessario specificare l'argomento al quale si vuole recapitare l'evento.

Ad esempio, pubblicando la matrice di eventi seguente, l'evento con `"id": "1111"` verrebbe inviato all'argomento `foo`, mentre l'evento con `"id": "2222"` verrebbe inviato all'argomento `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

I domini eventi gestiscono automaticamente la pubblicazione negli argomenti. Anziché pubblicare singolarmente gli eventi in ogni argomento gestito, è possibile pubblicare tutti gli eventi nell'endpoint del dominio e Griglia di eventi assicurerà che ogni evento venga inviato all'argomento corretto.

## <a name="limits-and-quotas"></a>Limiti e quote

### <a name="control-plane"></a>Piano di controllo

Durante l'anteprima, i domini eventi saranno limitati a 1.000 argomenti all'interno di un dominio e 50 sottoscrizioni di eventi per ogni argomento all'interno di un dominio. Anche per le sottoscrizioni di ambiti di dominio sarà previsto un limite di 50.

### <a name="data-plane"></a>Piano dati

Durante l'anteprima, la velocità effettiva degli eventi per un dominio eventi sarà limitata alla stessa velocità di inserimento di 5.000 eventi al secondo prevista per gli argomenti personalizzati.

## <a name="pricing"></a>Prezzi

Durante l'anteprima, per i domini eventi verranno applicati gli stessi [prezzi per le operazioni](https://azure.microsoft.com/pricing/details/event-grid/) di tutte le altre funzionalità di Griglia di eventi.

Nei domini eventi le operazioni funzionano come negli argomenti personalizzati. Ogni ingresso di un evento in un dominio eventi è un'operazione e ogni tentativo di recapito di un evento è un'operazione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione di domini eventi, la creazione di argomenti, la creazione di sottoscrizioni di eventi e la pubblicazione di eventi, vedere [Manage Event Domains](./how-to-event-domains.md) (Gestire domini eventi).