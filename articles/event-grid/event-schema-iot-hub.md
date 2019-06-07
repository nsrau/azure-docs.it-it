---
title: Schema di Griglia di eventi di Azure per l'hub IoT | Microsoft Docs
description: Pagina di riferimento per il formato e le proprietà dello schema di eventi dell'hub IoT
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: e770beb0470b54d8e13493bca4790323b2e96ce1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393197"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Schema di eventi di Griglia di eventi di Azure per l'hub IoT

Questo articolo illustra le proprietà e lo schema per gli eventi dell'hub IoT di Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). 

Per un elenco di esercitazioni e script di esempio, vedere l'[origine eventi Hub IoT](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Tipi di evento disponibili

L'hub IoT genera i tipi di eventi seguenti:

| Tipo evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Pubblicato quando un dispositivo viene registrato in un hub IoT. |
| Microsoft.Devices.DeviceDeleted | Pubblicato quando un dispositivo viene eliminato da un hub IoT. | 
| Microsoft.Devices.DeviceConnected | Pubblicato quando un dispositivo è connesso a un hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Pubblicato quando un dispositivo è disconnesso da un hub IoT. | 
| Microsoft.Devices.DeviceTelemetry | Pubblicato quando viene inviato un messaggio di telemetria a un hub IoT. |

Tutti gli eventi di dispositivo, ad eccezione degli eventi di telemetria dispositivo sono disponibili a livello generale in tutte le aree supportate da griglia di eventi. Evento di telemetria dispositivo è in anteprima pubblica ed è disponibile in tutte le aree ad eccezione degli Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale [Azure per enti pubblici](/azure-government/documentation-government-welcome.md), [Azure Cina 21Vianet](/azure/china/china-welcome.md), e [Azure Germania](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Evento di esempio

Lo schema per gli eventi DeviceConnected e DeviceDisconnected ha la stessa struttura. Questo evento di esempio illustra lo schema di un evento generato quando un dispositivo viene connesso a un hub IoT:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

L'evento DeviceTelemetry viene generato quando un evento di telemetria verrà inviato a un IoT Hub. Seguito è riportato un esempio di schema per questo evento.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Lo schema per gli eventi DeviceCreated e DeviceDeleted ha la stessa struttura. Questo evento di esempio illustra lo schema di un evento generato quando un dispositivo viene registrato in un hub IoT:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Proprietà degli eventi

Tutti gli eventi contengono gli stessi dati di livello principale: 

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| id | string | Identificatore univoco dell'evento. |
| topic | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| data | object | Dati dell'evento dell'hub IoT.  |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Per tutti gli eventi dell'hub IoT, l'oggetto dati contiene le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| hubName | string | Nome dell'hub IoT in cui il dispositivo è stato creato o eliminato. |
| deviceId | string | Identificatore univoco del dispositivo. Questa stringa con distinzione tra maiuscole e minuscole può avere una lunghezza di massimo 128 caratteri e supporta i caratteri alfanumerici ASCII a 7 bit e i caratteri speciali seguenti: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Il contenuto dell'oggetto dati è diverso per ogni autore di eventi. 

Per gli eventi **Device Connected** e **Device Disconnected** dell'hub IoT, l'oggetto dati contiene le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| moduleId | string | Identificatore univoco del modulo. Questo è un campo di output solo per i dispositivi di modulo. Questa stringa con distinzione tra maiuscole e minuscole può avere una lunghezza di massimo 128 caratteri e supporta i caratteri alfanumerici ASCII a 7 bit e i caratteri speciali seguenti: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | object | Informazioni sugli eventi dello stato di connessione del dispositivo
| sequenceNumber | string | Numero che consente di indicare l'ordine degli eventi correlati a dispositivi connessi o disconnessi. All'evento più recente è associato un numero di sequenza maggiore di quello dell'evento precedente. Questo numero potrebbe cambiare in incrementi maggiori di 1, ma è strettamente in aumento. Vedere [come usare il numero di sequenza](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Per la **telemetria del dispositivo** evento dell'IoT Hub, l'oggetto dati contiene il messaggio da dispositivo a cloud nel [formato del messaggio dell'hub IoT](../iot-hub/iot-hub-devguide-messages-construct.md) e presenta le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| Corpo | string | Il contenuto del messaggio dal dispositivo. |
| properties | string | Le proprietà dell'applicazione sono stringhe definite dall'utente che è possibile aggiungere al messaggio. Questi campi sono facoltativi. |
| Proprietà di sistema | string | [Le proprietà di sistema](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) identificare contenuto e l'origine dei messaggi. Messaggio di telemetria dispositivo deve essere in formato JSON valido con contentType impostato su JSON e contentEncoding impostato su UTF-8 in proprietà del sistema del messaggio. Se non è impostato, l'IoT Hub scriverà i messaggi in formato di codifica base 64.  |

Per gli eventi **Device Created** e **Device Deleted** dell'hub IoT, l'oggetto dati contiene le proprietà seguenti:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| twin | object | Informazioni sul dispositivo gemello, ovvero la rappresentazione nel cloud dei metadati del dispositivo dell'applicazione. | 
| deviceID | string | Identificatore univoco del dispositivo gemello. | 
| etag | string | Validator per garantire la coerenza degli aggiornamenti per un dispositivo gemello. Ogni etag è sicuramente univoco per ogni dispositivo gemello. |  
| deviceEtag| string | Validator per garantire la coerenza degli aggiornamenti a un registro dei dispositivi. Ogni deviceEtag è univoco per ogni registro dei dispositivi. |
| status | string | Indica se il dispositivo gemello è abilitato o disabilitato. | 
| statusUpdateTime | string | Timestamp ISO8601 dell'ultimo aggiornamento di stato del dispositivo gemello. |
| connectionState | string | Indica se il dispositivo è connesso o disconnesso. | 
| lastActivityTime | string | Timestamp ISO8601 dell'ultima attività. | 
| cloudToDeviceMessageCount | numero intero | Conteggio dei messaggi da cloud a dispositivo inviati al dispositivo. | 
| authenticationType | string | Tipo di autenticazione usato per questo dispositivo: `SAS`, `SelfSigned` o `CertificateAuthority`. |
| x509Thumbprint | string | L'identificazione personale è un valore univoco per il certificato x509, usato in genere per trovare un certificato specifico in un archivio certificati. L'identificazione personale viene generata in modo dinamico mediante l'algoritmo SHA1 e non esiste fisicamente nel certificato. | 
| primaryThumbprint | string | Identificazione personale primaria per il certificato x509. |
| secondaryThumbprint | string | Identificazione personale secondaria per il certificato x509. | 
| version | numero intero | Valore intero che viene incrementato di un'unità a ogni aggiornamento del dispositivo gemello. |
| desired | object | Parte delle proprietà che può essere scritta solo dal back-end dell'applicazione e letta dal dispositivo. | 
| reported | object | Parte delle proprietà che può essere scritta solo dal dispositivo e letta dal back-end dell'applicazione. |
| lastUpdated | string | Timestamp ISO8601 dell'ultimo aggiornamento delle proprietà del dispositivo gemello. | 

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per informazioni sulle interazioni tra l'hub IoT e Griglia di eventi, vedere [React to IoT Hub events by using Event Grid to trigger actions](../iot-hub/iot-hub-event-grid.md) (Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare azioni).