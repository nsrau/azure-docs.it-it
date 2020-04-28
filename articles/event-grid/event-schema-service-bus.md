---
title: Bus di servizio di Azure come origine di griglia di eventi
description: Descrive le proprietà disponibili per gli eventi del bus di servizio con Griglia di eventi di Azure
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 141a0e96071014dc3705d30f72b1a9257737298a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393249"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Bus di servizio di Azure come origine di griglia di eventi

Questo articolo illustra le proprietà e lo schema per gli eventi del bus di servizio.Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

Il bus di servizio genera i tipi di eventi seguenti:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Generato quando in una coda o una sottoscrizione sono presenti messaggi attivi e nessun ricevitore è in ascolto. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Generato quando in una coda di messaggi non recapitabili sono presenti messaggi attivi e non ci sono listener attivi. |

### <a name="example-event"></a>Evento di esempio

L'esempio seguente illustra lo schema di un evento con messaggi attivi e nessun listener:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Lo schema per un evento con coda di messaggi non recapitabili è simile:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
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
| data | oggetto | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Description |
| -------- | ---- | ----------- |
| namespaceName | stringa | Spazio dei nomi del bus di servizio in cui esiste la risorsa. |
| requestUri | stringa | URI della coda o della sottoscrizione specifica che crea l'evento. |
| entityType | stringa | Tipo di entità del bus di servizio che crea gli eventi (coda o sottoscrizione). |
| queueName | stringa | Coda con messaggi attivi in caso di sottoscrizione di una coda. Valore null se si usano argomenti/sottoscrizioni. |
| topicName | stringa | Argomento a cui appartiene la sottoscrizione del bus di servizio con messaggi attivi. Valore null se si usa una coda. |
| subscriptionName | stringa | Sottoscrizione del bus di servizio con messaggi attivi. Valore null se si usa una coda. |

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure
|Titolo  |Descrizione  |
|---------|---------|
| [Esercitazione: esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |
| [Integrazione tra il bus di servizio di Azure e griglia di eventi](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Panoramica dell'integrazione del bus di servizio con Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per maggiori dettagli sull'uso di Griglia di eventi di Azure con il bus di servizio, vedere [Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Provare a [ricevere gli eventi del bus di servizio con Funzioni o App per la logica](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
