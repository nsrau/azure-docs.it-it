---
title: Interpretare i dati degli eventi
titleSuffix: Azure Digital Twins
description: Vedere come interpretare diversi tipi di evento e i relativi messaggi di notifica.
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f6794559c2296b02ef61d0e280d29456904ae607
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609300"
---
# <a name="understand-event-data"></a>Informazioni sui dati degli eventi

Eventi diversi nei dispositivi gemelli digitali di Azure producono **notifiche**che consentono al back-end della soluzione di essere informati quando si verificano azioni diverse. Questi vengono quindi [indirizzati](concepts-route-events.md) a posizioni diverse all'interno e all'esterno dei dispositivi gemelli digitali di Azure che possono usare queste informazioni per intervenire.

Esistono diversi tipi di notifiche che possono essere generate e i messaggi di notifica possono avere un aspetto diverso a seconda del tipo di evento che li ha generati. In questo articolo vengono fornite informazioni dettagliate sui diversi tipi di messaggi e sull'aspetto che potrebbero essere simili.

Questo grafico mostra i diversi tipi di notifica:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

In generale, le notifiche sono costituite da due parti: l'intestazione e il corpo. 

### <a name="event-notification-headers"></a>Intestazioni delle notifiche degli eventi

Le intestazioni dei messaggi di notifica sono rappresentate da coppie chiave-valore. A seconda del protocollo utilizzato (MQTT, AMQP o HTTP), le intestazioni dei messaggi verranno serializzate in modo diverso. In questa sezione vengono illustrate le informazioni generali sull'intestazione per i messaggi di notifica, indipendentemente dal protocollo specifico e dalla serializzazione scelta.

Alcune notifiche sono conformi allo standard CloudEvents. La conformità di CloudEvents è la seguente.
* Le notifiche emesse dai dispositivi continuano a seguire le specifiche esistenti per le notifiche
* Le notifiche elaborate e emesse dall'hub Internet continuano a seguire le specifiche esistenti per la notifica, tranne nel caso in cui l'hub Internet sceglie di supportare CloudEvents, ad esempio tramite griglia di eventi.
* Notifiche emesse dai dispositivi [gemelli digitali](concepts-twins-graph.md) con un [modello](concepts-models.md) conforme a CloudEvents
* Le notifiche elaborate e emesse dai dispositivi gemelli digitali di Azure sono conformi a CloudEvents

I servizi devono aggiungere un numero di sequenza per tutte le notifiche per indicare l'ordine o mantenere il proprio ordinamento in altro modo. 

Le notifiche emesse da dispositivi gemelli digitali di Azure a griglia di eventi verranno automaticamente formattate nello schema CloudEvents o EventGridEvent, a seconda del tipo di schema definito nell'argomento griglia di eventi. 

Gli attributi di estensione sulle intestazioni verranno aggiunti come proprietà nello schema di griglia di eventi all'interno del payload. 

### <a name="event-notification-bodies"></a>Corpi delle notifiche degli eventi

I corpi dei messaggi di notifica sono descritti in JSON. A seconda della serializzazione desiderata per il corpo del messaggio, ad esempio with JSON, CBOR, protobuf e così via, il corpo del messaggio può essere serializzato in modo diverso.

Il set di campi contenuti nel corpo varia a seconda del tipo di notifica. Di seguito sono riportati due corpi dei messaggi di esempio, per avere un'idea di ciò che generalmente sembrano e possono includere.

Messaggio di telemetria:

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

Messaggio di notifica del ciclo di vita:

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

## <a name="message-format-detail-for-different-event-types"></a>Dettagli del formato del messaggio per diversi tipi di evento

Questa sezione illustra in modo più dettagliato i diversi tipi di notifiche emesse dall'hub e dai dispositivi gemelli digitali di Azure o da altri servizi di Azure. Vengono fornite informazioni sugli elementi che attivano ogni tipo di notifica e sul set di campi inclusi in ogni tipo di corpo della notifica.

### <a name="digital-twin-life-cycle-notifications"></a>Notifiche del ciclo di vita di dispositivi gemelli digitali

Tutti i dispositivi [gemelli digitali](concepts-twins-graph.md) emettono notifiche, indipendentemente dal fatto che rappresentino i [dispositivi dell'hub Internet o meno nei dispositivi gemelli digitali di Azure](how-to-ingest-iot-hub-data.md) . Questo è dovuto alle **notifiche del ciclo di vita**, che fanno parte del dispositivo gemello digitale.

Le notifiche del ciclo di vita vengono attivate nei casi seguenti:
* Viene creato un dispositivo gemello digitale
* Un dispositivo gemello digitale è stato eliminato

#### <a name="properties"></a>Proprietà

Di seguito sono riportati i campi nel corpo di una notifica del ciclo di vita.

| Nome | valore |
| --- | --- |
| `id` | Identificatore della notifica, ad esempio un UUID o un contatore gestito dal servizio. `source` + `id`è univoco per ogni evento distinto. |
| `source` | Nome dell'hub Internet delle cose o dell'istanza di Azure Digital gemelli, ad esempio *MyHub.Azure-Devices.NET* o *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID del dispositivo gemello digitale |
| `time` | Timestamp per il momento in cui l'operazione si è verificata nel dispositivo gemello |
| `traceparent` | Contesto di traccia W3C per l'evento |

#### <a name="body-details"></a>Dettagli corpo

Il corpo è il gemello digitale interessato, rappresentato in formato JSON. Lo schema per questo è la risorsa dei dispositivi *gemelli digitali 7,1*.

Per gli eventi di creazione, il payload riflette lo stato del gemello dopo la creazione della risorsa, quindi deve includere tutti gli elementi generati dal sistema esattamente come una `GET` chiamata.

Di seguito è riportato un esempio di corpo per un dispositivo [plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) , con componenti e senza proprietà di primo livello. Le proprietà che non hanno senso per i dispositivi, ad esempio le proprietà segnalate, devono essere omesse.

```json
{
  "$dtId": "device-digitaltwin-01",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Ecco un altro esempio di un dispositivo gemello digitale. Questo si basa su un [modello](concepts-models.md)e non supporta i componenti:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>Notifiche di modifica della relazione tra dispositivi gemelli digitali

Le **notifiche di modifica delle relazioni** vengono attivate quando viene creata, aggiornata o eliminata una relazione di un dispositivo gemello digitale. 

#### <a name="properties"></a>Proprietà

Ecco i campi nel corpo di una notifica di modifica del bordo.

| Nome    | valore |
| --- | --- |
| `id` | Identificatore della notifica, ad esempio un UUID o un contatore gestito dal servizio. `source` + `id`univoco per ogni evento distinto |
| `source` | Nome dell'istanza di Azure Digital Twins, ad esempio *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | ID della relazione, ad esempio`<twinID>/relationships/<relationshipID>` |
| `time` | Timestamp relativo al momento in cui si è verificata l'operazione sulla relazione |
| `traceparent` | Contesto di traccia W3C per l'evento |

#### <a name="body-details"></a>Dettagli corpo

Il corpo è il payload di una relazione, anche in formato JSON. USA lo stesso formato di una `GET` richiesta per una relazione tramite l' [API DigitalTwins](how-to-use-apis-sdks.md). 

"Aggiornamento di una relazione" indica che le proprietà della relazione sono state modificate. 

Di seguito è riportato un esempio di una notifica di aggiornamento della relazione per aggiornare una proprietà:

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Per `Relationship.Delete` , il corpo è lo stesso della `GET` richiesta e ottiene lo stato più recente prima dell'eliminazione.

Di seguito è riportato un esempio di una notifica di creazione o eliminazione di una relazione:

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>Notifiche di modifiche del dispositivo gemello digitale

Quando si aggiorna un dispositivo gemello digitale, vengono attivate le notifiche di modifica dei dispositivi **gemelli digitali** , ad esempio:
* Quando vengono modificati i valori o i metadati della proprietà.
* Quando vengono modificati i metadati del componente o del dispositivo digitale. Un esempio di questo scenario è la modifica del modello di un dispositivo gemello digitale.

#### <a name="properties"></a>Proprietà

Ecco i campi nel corpo di una notifica di modifica del dispositivo gemello digitale.

| Nome    | valore |
| --- | --- |
| `id` | Identificatore della notifica, ad esempio un UUID o un contatore gestito dal servizio. `source` + `id`univoco per ogni evento distinto |
| `source` | Nome dell'hub Internet delle cose o dell'istanza di Azure Digital gemelli, ad esempio *MyHub.Azure-Devices.NET* o *mydigitaltwins.westus2.azuredigitaltwins.NET*
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID del dispositivo gemello digitale |
| `time` | Timestamp per il momento in cui l'operazione si è verificata nel dispositivo gemello digitale |
| `traceparent` | Contesto di traccia W3C per l'evento |

#### <a name="body-details"></a>Dettagli corpo

Il corpo della `Twin.Update` notifica è un documento di patch JSON contenente l'aggiornamento al dispositivo gemello digitale.

Si immagini ad esempio che un dispositivo gemello digitale è stato aggiornato con la seguente patch.

```json
[
    {
        "op": "replace",
        "value": 40,
        "path": "/Temperature"
    },
    {
        "op": "add",
        "value": 30,
        "path": "/comp1/prop1"
    }
]
```

La notifica corrispondente, se eseguita in modo sincrono dal servizio, ad esempio i gemelli digitali di Azure che aggiornano un dispositivo gemello digitale, avrà un corpo simile al seguente:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="next-steps"></a>Passaggi successivi

Vedere come creare endpoint e route per recapitare gli eventi:
* [Procedura: gestire endpoint e Route](how-to-manage-routes.md)

In alternativa, Scopri di più sulle API dei dispositivi digitali gemelli di Azure e sulle opzioni dell'SDK:
* [Procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure](how-to-use-apis-sdks.md)