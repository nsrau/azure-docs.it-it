---
title: Traffico in uscita ed endpoint in Gemelli digitali di Azure | Microsoft Docs
description: Linee guida su come creare endpoint con Gemelli digitali di Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: c917fab84448684cf29af162ec0781d764605f71
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323852"
---
# <a name="egress-and-endpoints"></a>Traffico in uscita ed endpoint

Gemelli digitali di Azure supporta il concetto di _endpoint_, per cui ogni endpoint rappresenta un broker di messaggi/eventi nella sottoscrizione di Azure dell'utente. Eventi e messaggi possono essere inviati a **Hub eventi**, **Griglia di eventi** e ad **argomenti del bus di servizio**.

Gli eventi verranno inviati agli endpoint in base alle preferenze di routing predefinite: l'utente può specificare quale endpoint deve ricevere uno qualsiasi di questi eventi:`TopologyOperation`, `UdfCustom`, `SensorChange`, `SpaceChange` e `DeviceMessage`.

Per informazioni di base sul routing di eventi e tipi di evento, fare riferimento a [Routing di eventi e messaggi](concepts-events-routing.md).

## <a name="event-types-description"></a>Descrizione dei tipi di evento

Ecco i formati di evento per ognuno dei tipi di evento:

- `TopologyOperation`

  Si applica alle modifiche del grafico. La proprietà `subject` specifica il tipo di oggetto interessato. I tipi di oggetti che possono attivare questo evento sono: `Device, DeviceBlobMetadata`, `DeviceExtendedProperty`, `ExtendedPropertyKey`, `ExtendedType`, `KeyStore`, `Report`, `RoleDefinition`, `Sensor`, `SensorBlobMetadata`, `SensorExtendedProperty`, `Space`, `SpaceBlobMetadata`, `SpaceExtendedProperty`, `SpaceResource`, `SpaceRoleAssignment`, `System`, `User`, `UserBlobMetadata` e `UserExtendedProperty`.

  Esempio:

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nome attributo personalizzato | Sostituire con |
    | --- | --- |
    | `yourTopicName` | Nome dell'argomento personalizzato |

- `UdfCustom`

  Evento inviato da una funzione definita dall'utente. Si noti che questo evento deve essere inviato in modo esplicito dalla funzione definita dall'utente stessa.

  Esempio:

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nome attributo personalizzato | Sostituire con |
    | --- | --- |
    | `yourTopicName` | Nome dell'argomento personalizzato |

- `SensorChange`

  Aggiornamento applicato allo stato di un sensore in base alle modifiche dei dati di telemetria.

  Esempio:

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nome attributo personalizzato | Sostituire con |
    | --- | --- |
    | `yourTopicName` | Nome dell'argomento personalizzato |

- `SpaceChange`

  Aggiornamento applicato allo stato di uno spazio in base alle modifiche dei dati di telemetria.

  Esempio:

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nome attributo personalizzato | Sostituire con |
    | --- | --- |
    | `yourTopicName` | Nome dell'argomento personalizzato |

- `DeviceMessage`

  Permette di specificare una connessione `EventHub` in cui gli eventi di telemetria non elaborati possono anche essere instradati da Gemelli digitali di Azure.

> [!NOTE]
> - `DeviceMessage` può essere combinato solo con `EventHub`, mentre non sarà possibile combinare `DeviceMessage` con uno qualsiasi degli altri tipi di evento.
> - È possibile specificare un solo endpoint della combinazione di tipo `EventHub`/`DeviceMessage`.

## <a name="configuring-endpoints"></a>Configurazione degli endpoint

La gestione degli endpoint viene eseguita tramite l'API per gli endpoint. Ecco alcuni esempi di come configurare i diversi endpoint supportati. Prestare particolare attenzione alla matrice dei tipi di evento, perché definisce il routing per l'endpoint:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Instrada al **bus di servizio** i tipi di evento `SensorChange`, `SpaceChange` e `TopologyOperation`

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | Nome attributo personalizzato | Sostituire con |
    | --- | --- |
    | `yourNamespace` | Spazio dei nomi dell'endpoint |
    | `yourPrimaryKey` | Stringa di connessione primaria usata per l'autenticazione |
    | `yourSecondaryKey` | Stringa di connessione secondaria usata per l'autenticazione |
    | `yourTopicName` | Nome dell'argomento personalizzato |

- Instrada a **Griglia di eventi** i tipi di evento `SensorChange`, `SpaceChange` e `TopologyOperation`

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | Nome attributo personalizzato | Sostituire con |
    | --- | --- |
    | `yourPrimaryKey` | Stringa di connessione primaria usata per l'autenticazione|
    | `yourSecondaryKey` | Stringa di connessione secondaria usata per l'autenticazione |
    | `yourTopicName` | Nome dell'argomento personalizzato |

- Instrada a **Hub eventi** i tipi di evento `SensorChange`, `SpaceChange` e `TopologyOperation`

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | Nome attributo personalizzato | Sostituire con |
    | --- | --- |
    | `yourNamespace` | Spazio dei nomi dell'endpoint |
    | `yourPrimaryKey` | Stringa di connessione primaria usata per l'autenticazione |
    | `yourSecondaryKey` | Stringa di connessione secondaria usata per l'autenticazione |
    | `yourEventHubName` | Nome dell'hub eventi |

- Instrada a **Hub eventi** i tipi di evento `DeviceMessage`. Notare l'inclusione di _EntityPath_ in `connectionString`, che è un'impostazione obbligatoria.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | Nome attributo personalizzato | Sostituire con |
    | --- | --- |
    | `yourNamespace` | Spazio dei nomi dell'endpoint |
    | `yourPrimaryKey` | Stringa di connessione primaria usata per l'autenticazione |
    | `yourSecondaryKey` | Stringa di connessione secondaria usata per l'autenticazione |
    | `yourEventHubName` | Nome dell'hub eventi |

> [!NOTE]
> Al momento della creazione di un nuovo endpoint, potrebbero essere necessari fino a 5-10 minuti prima di iniziare a ricevere eventi nell'endpoint.

## <a name="primary-and-secondary-connection-keys"></a>Chiavi di connessione primaria e secondaria

Quando una chiave di connessione primaria non è più autorizzata, il sistema tenta automaticamente la chiave di connessione secondaria. Questa fornisce un meccanismo di backup e permette di autenticare e aggiornare correttamente la chiave primaria tramite l'API per gli endpoint.

Se entrambe le chiavi di connessione primaria e secondaria non sono autorizzate, il sistema attiverà un tempo di attesa di backoff esponenziale fino a 30 minuti. Gli eventi verranno eliminati in occasione di ogni tempo di attesa di backoff attivato.

Ogni volta che il sistema è in stato di attesa di back-off, l'aggiornamento delle chiavi di connessione tramite l'API degli endpoint può richiedere fino a 30 minuti prima di diventare effettivo.

## <a name="unreachable-endpoints"></a>Endpoint non raggiungibili

Quando un endpoint non è più raggiungibile, il sistema dovrà attivare un tempo di attesa di backoff esponenziale fino a 30 minuti. Gli eventi verranno eliminati in occasione di ogni tempo di attesa di backoff attivato.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare Swagger per Gemelli digitali di Azure, vedere [Azure Digital Twins Swagger](how-to-use-swagger.md) (Swagger per Gemelli digitali di Azure).

Per altre informazioni sul routing di eventi e messaggi in Gemelli digitali di Azure, vedere [Routing di eventi e messaggi](concepts-events-routing.md).
