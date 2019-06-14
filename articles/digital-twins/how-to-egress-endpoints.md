---
title: Traffico in uscita ed endpoint in Gemelli digitali di Azure | Microsoft Docs
description: Linee guida sulla creazione di endpoint con Gemelli digitali di Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: alinast
ms.openlocfilehash: 478fe1859dd9067e8097df0384657793602c1378
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071451"
---
# <a name="egress-and-endpoints"></a>Traffico in uscita ed endpoint

Gli *endpoint* di Gemelli digitali di Azure rappresentano un broker messaggi o eventi nella sottoscrizione di Azure dell'utente. Messaggi ed eventi possono essere inviati ad argomenti di Hub eventi di Azure, Griglia di eventi di Azure e Bus di servizio di Azure.

Gli eventi vengono indirizzati agli endpoint in base alle preferenze di routing predefinite. Gli utenti specificano i *tipi di eventi* che ogni endpoint può ricevere.

Per altre informazioni su eventi, routing e tipi di eventi, vedere [Routing di eventi e messaggi](./concepts-events-routing.md).

## <a name="events"></a>Eventi

Gli eventi vengono inviati da oggetti IoT, come dispositivi e sensori, per essere elaborati dai broker messaggi ed eventi di Azure. Gli eventi sono definiti dallo [schema di eventi di Griglia di eventi di Azure](../event-grid/event-schema.md) seguente.

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

| Attributo | Type | Descrizione |
| --- | --- | --- |
| id | string | Identificatore univoco dell'evento. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| data | object | Dati dell'evento specifici del provider di risorse. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |
| topic | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |

Per altre informazioni sullo schema di eventi di Griglia di eventi:

- Vedere [Schema di eventi di Griglia di eventi di Azure](../event-grid/event-schema.md).
- Vedere la documentazione di riferimento sull'[interfaccia EventGridEvent](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Tipi di eventi

I tipi di eventi classificano la natura dell'evento e sono impostati nel campo **eventType**. Di seguito sono elencati i tipi di evento disponibili:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

I formati di evento per ogni tipo di evento sono descritti in maggiore dettaglio nelle sottosezioni seguenti.

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

| Value | Sostituire con |
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

| Value | Sostituire con |
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

| Value | Sostituire con |
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

| Value | Sostituire con |
| --- | --- |
| YOUR_TOPIC_NAME | Nome dell'argomento personalizzato |

### <a name="devicemessage"></a>DeviceMessage

**DeviceMessage** consente di specificare una connessione **EventHub** in cui anche gli eventi di telemetria non elaborati possono essere instradati da Gemelli digitali di Azure.

> [!NOTE]
> - **DeviceMessage** è combinabile solo con **EventHub**. Non è possibile combinare **DeviceMessage** con uno qualsiasi degli altri tipi di eventi.
> - È possibile specificare un solo endpoint della combinazione di tipo **EventHub** o **DeviceMessage**.

## <a name="configure-endpoints"></a>Configurare gli endpoint

La gestione degli endpoint viene eseguita tramite l'API per gli endpoint.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Gli esempi seguenti illustrano come configurare gli endpoint supportati.

>[!IMPORTANT]
> Prestare particolare attenzione all'attributo **eventTypes**. Definisce infatti i tipi di eventi gestiti dall'endpoint e ne determina quindi il routing.

Una richiesta HTTP POST autenticata su

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
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

    | Value | Sostituire con |
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

    | Value | Sostituire con |
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

    | Value | Sostituire con |
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

    | Value | Sostituire con |
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
