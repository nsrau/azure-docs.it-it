---
title: Schema di eventi del bus di servizio di Griglia di eventi di Azure
description: "Descrive le proprietà disponibili per gli eventi del bus di servizio con Griglia di eventi di Azure"
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 02/21/2018
ms.author: babanisa
ms.openlocfilehash: 72780bff3807534efb456a9a7998f7d4de3c6f12
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Schema di eventi di Griglia di eventi di Azure per il bus di servizio

Questo articolo illustra le proprietà e lo schema per gli eventi del bus di servizio. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="available-event-types"></a>Tipi di evento disponibili

Il bus di servizio genera i tipi di eventi seguenti:

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Generato quando in una coda o una sottoscrizione sono presenti messaggi attivi e nessun ricevitore è in ascolto. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Generato quando in una coda di messaggi non recapitabili sono presenti messaggi attivi e non ci sono listener attivi. |

## <a name="example-event"></a>Evento di esempio

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

## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| argomento | stringa | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | stringa | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | stringa | Identificatore univoco dell'evento. |
| data | object | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| nameSpaceName | stringa | Spazio dei nomi del bus di servizio in cui esiste la risorsa. |
| requestUri | stringa | URI della coda o della sottoscrizione specifica che crea l'evento. |
| entityType | stringa | Tipo di entità del bus di servizio che crea gli eventi (coda o sottoscrizione). |
| queueName | stringa | Coda con messaggi attivi in caso di sottoscrizione di una coda. Valore null se si usano argomenti/sottoscrizioni. |
| topicName | stringa | Argomento a cui appartiene la sottoscrizione del bus di servizio con messaggi attivi. Valore null se si usa una coda. |
| subscriptionName | stringa | Sottoscrizione del bus di servizio con messaggi attivi. Valore null se si usa una coda. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per maggiori dettagli sull'uso di Griglia di eventi di Azure con il bus di servizio, vedere [Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Provare a [ricevere gli eventi del bus di servizio con Funzioni o App per la logica](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
