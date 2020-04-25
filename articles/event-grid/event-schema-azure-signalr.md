---
title: Azure SignalR come origine griglia di eventi
description: Descrive le proprietà fornite per gli eventi di Azure SignalR con griglia di eventi di Azure
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: babanisa
ms.openlocfilehash: e4ebae9597d750cea6f292655e9f03dd65ccc3f5
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133727"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Schema di eventi di griglia di eventi di Azure per il servizio SignalR

Questo articolo fornisce le proprietà e lo schema per gli eventi del servizio SignalR.Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). Viene anche visualizzato un elenco di guide introduttive ed esercitazioni per usare Azure SignalR come origine evento.

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

Il servizio SignalR emette i tipi di evento seguenti:

| Tipo di evento | Description |
| ---------- | ----------- |
| Microsoft. SignalRService. ClientConnectionConnected | Generato quando una connessione client si connette. |
| Microsoft. SignalRService. ClientConnectionDisconnected | Generato quando una connessione client viene disconnessa. |

### <a name="example-event"></a>Evento di esempio

Nell'esempio seguente viene illustrato lo schema di un evento connesso della connessione client: 

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

Lo schema per un evento di connessione client disconnessa è simile: 

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

| Proprietà | Type | Description |
| -------- | ---- | ----------- |
| argomento | stringa | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | stringa | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | stringa | Identificatore univoco dell'evento. |
| data | oggetto | Dati degli eventi del servizio SignalR. |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Description |
| -------- | ---- | ----------- |
| timestamp | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| hubName | stringa | Hub a cui appartiene la connessione client. |
| connectionId | stringa | Identificatore univoco per la connessione client. |
| userId | stringa | Identificatore utente definito nell'attestazione. |
| errorMessage | stringa | Errore che causa la disconnessione della connessione. |

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure
|Titolo | Descrizione |
|---------|---------|
| [Reagire agli eventi del servizio Azure SignalR usando griglia di eventi](../azure-signalr/signalr-concept-event-grid-integration.md) | Panoramica dell'integrazione del servizio SignalR di Azure con griglia di eventi. |
| [Come inviare eventi del servizio Azure SignalR a griglia di eventi](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra come inviare eventi del servizio Azure SignalR a un'applicazione tramite griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
