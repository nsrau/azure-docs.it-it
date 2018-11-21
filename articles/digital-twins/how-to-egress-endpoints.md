---
title: Traffico in uscita ed endpoint in Gemelli digitali di Azure | Microsoft Docs
description: Linee guida su come creare endpoint con Gemelli digitali di Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c94d29f16c011a9ff9951d064d7496d3a87f70ef
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636306"
---
# <a name="egress-and-endpoints"></a>Traffico in uscita ed endpoint

Gemelli digitale di Azure supporta il concetto di **endpoint**. Ogni endpoint rappresenta un broker messaggi o eventi nella sottoscrizione di Azure dell'utente. Messaggi ed eventi possono essere inviati ad argomenti di Hub eventi di Azure, Griglia di eventi di Azure e Bus di servizio di Azure.

Gli eventi vengono inviati agli endpoint in base alle preferenze di routing predefinite. L'utente può specificare quale endpoint deve ricevere uno qualsiasi degli eventi seguenti: 

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Per informazioni di base sul routing di eventi e tipi di evento, fare riferimento a [Routing di eventi e messaggi](concepts-events-routing.md).

## <a name="event-types-description"></a>Descrizione dei tipi di evento

I formati degli eventi per ognuno dei tipi di eventi sono descritti nelle sezioni seguenti.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** si applica alle modifiche del grafico. La proprietà **subject** specifica il tipo di oggetto interessato. I tipi di oggetti seguenti potrebbero generare questo evento: 

- Dispositivo
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Report
- RoleDefinition
- Sensore
- SensorBlobMetadata
- SensorExtendedProperty
- Spazio
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- Sistema
- Utente
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Esempio

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valore | Sostituire con |
| --- | --- |
| YOUR_TOPIC_NAME | Nome dell'argomento personalizzato |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** è un evento inviato da una funzione definita dall'utente. 
  
> [!IMPORTANT]  
> Questo evento deve essere inviato in modo esplicito dalla funzione definita dall'utente stessa.

#### <a name="example"></a>Esempio

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valore | Sostituire con |
| --- | --- |
| YOUR_TOPIC_NAME | Nome dell'argomento personalizzato |

### <a name="sensorchange"></a>SensorChange

**SensorChange** è un aggiornamento applicato allo stato di un sensore in base alle modifiche dei dati di telemetria.

#### <a name="example"></a>Esempio

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valore | Sostituire con |
| --- | --- |
| YOUR_TOPIC_NAME | Nome dell'argomento personalizzato |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** è un aggiornamento applicato allo stato di uno spazio in base alle modifiche dei dati di telemetria.

#### <a name="example"></a>Esempio

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valore | Sostituire con |
| --- | --- |
| YOUR_TOPIC_NAME | Nome dell'argomento personalizzato |

### <a name="devicemessage"></a>DeviceMessage

**DeviceMessage** consente di specificare una connessione **EventHub** in cui anche gli eventi di telemetria non elaborati possono essere instradati da Gemelli digitali di Azure.

> [!NOTE]
> - **DeviceMessage** è combinabile solo con **EventHub**. Non è possibile combinare **DeviceMessage** con uno qualsiasi degli altri tipi di eventi.
> - È possibile specificare un solo endpoint della combinazione di tipo **EventHub** o **DeviceMessage**.

## <a name="configure-endpoints"></a>Configurare gli endpoint

La gestione degli endpoint viene eseguita tramite l'API per gli endpoint. Gli esempi seguenti illustrano come configurare i diversi endpoint supportati. Prestare particolare attenzione alla matrice dei tipi di evento, perché definisce il routing per l'endpoint:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Instrada al bus di servizio i tipi di evento **SensorChange**, **SpaceChange** e **TopologyOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Valore | Sostituire con |
    | --- | --- |
    | YOUR_NAMESPACE | Spazio dei nomi dell'endpoint |
    | YOUR_PRIMARY_KEY | Stringa di connessione primaria usata per l'autenticazione |
    | YOUR_SECONDARY_KEY | Stringa di connessione secondaria usata per l'autenticazione |
    | YOUR_TOPIC_NAME | Nome dell'argomento personalizzato |

- Instrada a Griglia di eventi i tipi di evento **SensorChange**, **SpaceChange** e **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Valore | Sostituire con |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Stringa di connessione primaria usata per l'autenticazione|
    | YOUR_SECONDARY_KEY | Stringa di connessione secondaria usata per l'autenticazione |
    | YOUR_TOPIC_NAME | Nome dell'argomento personalizzato |

- Instrada a Hub eventi i tipi di evento **SensorChange**, **SpaceChange** e **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Valore | Sostituire con |
    | --- | --- |
    | YOUR_NAMESPACE | Spazio dei nomi dell'endpoint |
    | YOUR_PRIMARY_KEY | Stringa di connessione primaria usata per l'autenticazione |
    | YOUR_SECONDARY_KEY | Stringa di connessione secondaria usata per l'autenticazione |
    | YOUR_EVENT_HUB_NAME | Nome dell'hub eventi |

- Instrada a Hub eventi il tipo di evento **DeviceMessage**. È obbligatorio includere `EntityPath` in **connectionString**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Valore | Sostituire con |
    | --- | --- |
    | YOUR_NAMESPACE | Spazio dei nomi dell'endpoint |
    | YOUR_PRIMARY_KEY | Stringa di connessione primaria usata per l'autenticazione |
    | YOUR_SECONDARY_KEY | Stringa di connessione secondaria usata per l'autenticazione |
    | YOUR_EVENT_HUB_NAME | Nome dell'hub eventi |

> [!NOTE]  
> Dopo la creazione di un nuovo endpoint, potrebbero trascorrere da 5 a 10 minuti prima che si inizi a ricevere eventi nell'endpoint.

## <a name="primary-and-secondary-connection-keys"></a>Chiavi di connessione primaria e secondaria

Quando una chiave di connessione primaria non è più autorizzata, il sistema tenta automaticamente la chiave di connessione secondaria. Questa fornisce un meccanismo di backup e permette di autenticare e aggiornare correttamente la chiave primaria tramite l'API per gli endpoint.

Se entrambe le chiavi di connessione primaria e secondaria non sono autorizzate, il sistema attiva un tempo di attesa di backoff esponenziale fino a 30 minuti. Gli eventi vengono eliminati in occasione di ogni tempo di attesa di backoff attivato.

Ogni volta che il sistema è in stato di attesa di backoff, l'aggiornamento delle chiavi di connessione tramite l'API degli endpoint può richiedere fino a 30 minuti prima di diventare effettivo.

## <a name="unreachable-endpoints"></a>Endpoint non raggiungibili

Quando un endpoint non è più raggiungibile, il sistema deve attivare un tempo di attesa di backoff esponenziale fino a 30 minuti. Gli eventi vengono eliminati in occasione di ogni tempo di attesa di backoff attivato.

## <a name="next-steps"></a>Passaggi successivi

- Scoprire [come usare Swagger per Gemelli digitali di Azure](how-to-use-swagger.md).

- Altre informazioni sul [routing di eventi e messaggi](concepts-events-routing.md) in Gemelli digitali di Azure.
