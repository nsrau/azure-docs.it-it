---
title: Schema di Griglia di eventi di Azure per l'hub IoT | Microsoft Docs
description: "Pagina di riferimento per il formato e le proprietà dello schema di eventi dell'hub IoT"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 29ad1233a344c3085286c27cb925b2dc9fb41f7e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Schema di eventi di Griglia di eventi di Azure per l'hub IoT

Questo articolo illustra le proprietà e lo schema per gli eventi dell'hub IoT di Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). 

## <a name="available-event-types"></a>Tipi di evento disponibili

L'hub IoT genera i tipi di eventi seguenti:

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Pubblicato quando un dispositivo viene registrato in un hub IoT. |
| Microsoft.Devices.DeviceDeleted | Pubblicato quando un dispositivo viene eliminato da un hub IoT. | 

## <a name="example-event"></a>Evento di esempio

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Proprietà degli eventi

Tutti gli eventi contengono gli stessi dati di livello principale: 

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| id | stringa | Identificatore univoco dell'evento. |
| argomento | stringa | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | stringa | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| data | object | Dati dell'evento dell'hub IoT.  |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Il contenuto dell'oggetto dati è diverso per ogni autore di eventi. Per gli eventi del'hub IoT, l'oggetto dati contiene le proprietà seguenti:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| hubName | stringa | Nome dell'hub IoT in cui il dispositivo è stato creato o eliminato. |
| deviceId | stringa | Identificatore univoco del dispositivo. Questa stringa con distinzione tra maiuscole e minuscole può avere una lunghezza di massimo 128 caratteri e supporta i caratteri alfanumerici ASCII a 7 bit e i caratteri speciali seguenti: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | stringa | Timestamp ISO8601 dell'operazione. |
| opType | stringa | Il tipo di evento specificato per questa operazione dall'hub IoT: `DeviceCreated` o `DeviceDeleted`.
| twin | object | Informazioni sul dispositivo gemello, ovvero la rappresentazione nel cloud dei metadati del dispositivo dell'applicazione. | 
| deviceID | stringa | Identificatore univoco del dispositivo gemello. | 
| etag | stringa | Informazioni descrittive del contenuto del dispositivo gemello. Ogni etag è sicuramente univoco per ogni dispositivo gemello. | 
| status | stringa | Indica se il dispositivo gemello è abilitato o disabilitato. | 
| statusUpdateTime | stringa | Timestamp ISO8601 dell'ultimo aggiornamento di stato del dispositivo gemello. |
| connectionState | stringa | Indica se il dispositivo è connesso o disconnesso. | 
| lastActivityTime | stringa | Timestamp ISO8601 dell'ultima attività. | 
| cloudToDeviceMessageCount | numero intero | Conteggio dei messaggi da cloud a dispositivo inviati al dispositivo. | 
| authenticationType | stringa | Tipo di autenticazione usato per questo dispositivo: `SAS`, `SelfSigned` o `CertificateAuthority`. |
| x509Thumbprint | stringa | L'identificazione personale è un valore univoco per il certificato x509, usato in genere per trovare un certificato specifico in un archivio certificati. L'identificazione personale viene generata in modo dinamico mediante l'algoritmo SHA1 e non esiste fisicamente nel certificato. | 
| primaryThumbprint | stringa | Identificazione personale primaria per il certificato x509. |
| secondaryThumbprint | stringa | Identificazione personale secondaria per il certificato x509. | 
| version | numero intero | Valore intero che viene incrementato di un'unità a ogni aggiornamento del dispositivo gemello. |
| desired | object | Parte delle proprietà che può essere scritta solo dal back-end dell'applicazione e letta dal dispositivo. | 
| reported | object | Parte delle proprietà che può essere scritta solo dal dispositivo e letta dal back-end dell'applicazione. |
| lastUpdated | stringa | Timestamp ISO8601 dell'ultimo aggiornamento delle proprietà del dispositivo gemello. | 

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per informazioni sulle interazioni tra l'hub IoT e Griglia di eventi, vedere [React to IoT Hub events by using Event Grid to trigger actions](../iot-hub/iot-hub-event-grid.md) (Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare azioni).