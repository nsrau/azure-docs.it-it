---
title: Azure SingnalR come origine griglia di eventiAzure SingnalR as Event Grid source
description: Descrive le proprietà fornite per gli eventi SignalR di Azure con Griglia di eventi di AzureDescribes the properties that are provided for Azure SignalR events with Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 730d1a7a053ab636c45313dd0c35a537434eb782
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393402"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Azure Event Grid event schema for SignalR Service

In questo articolo vengono fornite le proprietà e lo schema per gli eventi del servizio SignalR.Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). Fornisce inoltre un elenco di guide introduttive ed esercitazioni per usare Azure SignalR come origine eventi.

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

SignalR Service genera i seguenti tipi di evento:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Generato quando si è connessa una connessione client. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Generato quando una connessione client è stata disconnessa. |

### <a name="example-event"></a>Evento di esempio

Nell'esempio seguente viene illustrato lo schema di un evento connesso di connessione client:The following example shows the schema of a client connection connected event: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Lo schema per un evento disconnesso di connessione client è simile:The schema for a client connection disconnected event is similar: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati dell'evento SignalR Service. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
|  timestamp | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| hubName | string | Hub a cui appartiene la connessione client. |
| connectionId | string | Identificatore univoco della connessione client. |
| userId | string | Identificatore utente definito nell'attestazione. |
| errorMessage | string | Errore che causa la connessione disconnessa. |

## <a name="tutorials-and-how-tos"></a>Esercitazioni ed es.
|Titolo | Descrizione |
|---------|---------|
| [Reagire agli eventi del servizio SignalR di Azure usando Griglia di eventiReact to Azure SignalR Service events by using Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Panoramica dell'integrazione del servizio SignalR di Azure con La griglia di eventi. |
| [Come inviare eventi del servizio SignalR di Azure alla griglia di eventiHow to send Azure SignalR Service events to Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Viene illustrato come inviare eventi del servizio SignalR di Azure a un'applicazione tramite Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
