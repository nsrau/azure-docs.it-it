---
title: Hub IoT e Griglia di eventi di Azure | Microsoft Docs
description: Usare Griglia di eventi di Azure per attivare processi basati su azioni eseguite nell'hub IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a67d90a0888c39938f07c294f8e161ce98fd945a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732508"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni

L'hub IoT di Azure si integra con Griglia di eventi di Azure per poter inviare le notifiche degli eventi agli altri servizi e attivare processi downstream. Configurare le applicazioni aziendali per l'ascolto degli eventi dell'hub IoT in modo che sia possibile rispondere agli eventi critici in modo sicuro, scalabile e affidabile.Ad esempio, compilare un'applicazione che aggiorna un database, crea un ticket di lavoro e invia una notifica tramite posta elettronica ogni volta che un nuovo dispositivo IoT viene registrato nell'hub IoT.For example, build an application that updates a database, creates a work ticket, and delivers an email notification every time a new IoT device is registered to your IoT hub.

[Griglia di eventi di Azure](../event-grid/overview.md) è un servizio di routing di eventi completamente gestito che usa un modello di pubblicazione-sottoscrizione. Griglia di eventi include il supporto predefinito per i servizi di Azure, ad esempio [Funzioni di Azure](../azure-functions/functions-overview.md) e [App per la logica di Azure](../logic-apps/logic-apps-what-are-logic-apps.md), e può recapitare gli avvisi relativi agli eventi ai servizi non di Azure usando i webhook. Per un elenco completo dei gestori di eventi supportati da Griglia di eventi, vedere [Introduzione a Griglia di eventi di Azure](../event-grid/overview.md).

![Architettura di Griglia di eventi di Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilità a livello di area

L'integrazione di Griglia di eventi è disponibile per gli hub IoT situati nelle aree in cui Griglia di eventi è supportata. Per l'elenco aggiornato delle aree, vedere [Introduzione a Griglia di eventi di Azure](../event-grid/overview.md).

## <a name="event-types"></a>Tipi di eventi

L'hub IoT pubblica i tipi di eventi seguenti:

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Pubblicato quando un dispositivo viene registrato in un hub IoT. |
| Microsoft.Devices.DeviceDeleted | Pubblicato quando un dispositivo viene eliminato da un hub IoT. |
| Microsoft.Devices.DeviceConnected | Pubblicato quando un dispositivo è connesso a un hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Pubblicato quando un dispositivo è disconnesso da un hub IoT. |
| Microsoft.Devices.DeviceTelemetry | Pubblicato quando un messaggio di telemetria del dispositivo viene inviato a un hub IoTPublished when a device telemetry message is sent to an IoT hub |

Usare il portale di Azure o l'interfaccia della riga di comando di Azure per configurare gli eventi da pubblicare da ogni hub IoT. Per un esempio, provare l'esercitazione [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando App per la logica).

## <a name="event-schema"></a>Schema di eventi

Gli eventi dell'hub IoT contengono tutte le informazioni necessarie per rispondere alle modifiche del ciclo di vita del dispositivo. Per identificare un evento dell'hub IoT, controllare se la proprietà eventType inizia con **Microsoft.Devices**. Per altre informazioni su come usare le proprietà degli eventi di Griglia di eventi, vedere lo [schema di eventi di Griglia di eventi](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schema di dispositivo connesso

L'esempio seguente illustra lo schema di un evento di dispositivo connesso:

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
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Schema di telemetria del dispositivoDevice Telemetry schema

Il messaggio di telemetria del dispositivo deve essere in un formato JSON valido con contentType impostato su **application/json** e contentEncoding impostato su **UTF-8** nelle proprietà del sistema di [messaggi.](iot-hub-devguide-routing-query-syntax.md#system-properties) Entrambe queste proprietà non fanno distinzione tra maiuscole e minuscole. Se la codifica del contenuto non è impostata, l'hub IoT scriverà i messaggi in formato codificato in base 64.

È possibile arricchire gli eventi di telemetria del dispositivo prima che vengano pubblicati in Griglia di eventi selezionando l'endpoint come Griglia di eventi. Per ulteriori informazioni, consultate [Cenni preliminari sugli arricchimenti](iot-hub-message-enrichments-overview.md)di messaggi.

L'esempio seguente mostra lo schema di un evento di telemetria del dispositivo:The following example shows the schema of a device telemetry event:

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

### <a name="device-created-schema"></a>Schema creato da un dispositivo

L'esempio seguente illustra lo schema di un evento creato da un dispositivo:

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
      "deviceEtag":"null",
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

Per una descrizione dettagliata di ogni proprietà, vedere Schema di eventi Griglia di eventi di [Azure per Hub IoT](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrare gli eventi

Le sottoscrizioni di eventi dell'hub IoT possono filtrare gli eventi in base al tipo di evento, al contenuto dei dati e all'oggetto, ovvero il nome del dispositivo.

Griglia di eventi consente [di filtrare](../event-grid/event-filtering.md) i tipi di evento, gli oggetti e il contenuto dei dati. Durante la creazione della sottoscrizione griglia di eventi, è possibile scegliere di sottoscrivere eventi IoT selezionati. I filtri degli oggetti in Griglia di eventi funzionano in base alle corrispondenze **Inizia con** (prefisso) e **Termina con** (suffisso). Il filtro utilizza un `AND` operatore in modo che gli eventi con un oggetto che corrisponde sia al prefisso che al suffisso viene recapitato al sottoscrittore.

L'oggetto di eventi IoT usa il formato:

```json
devices/{deviceId}
```

Griglia di eventi consente inoltre di filtrare gli attributi di ogni evento, incluso il contenuto dei dati. In questo modo è possibile scegliere quali eventi vengono recapitati in base al contenuto del messaggio di telemetria. Vedere [il filtro avanzato](../event-grid/event-filtering.md#advanced-filtering) per visualizzare esempi. Per filtrare il corpo del messaggio di telemetria, è necessario impostare contentType su **application/json** e contentEncoding su **UTF-8** nelle proprietà del sistema di [messaggi.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties) Entrambe queste proprietà non fanno distinzione tra maiuscole e minuscole.

Per gli eventi non di telemetria come DeviceConnected, DeviceDisconnected, DeviceCreated e DeviceDeleted, il filtro Griglia di eventi può essere usato durante la creazione della sottoscrizione. Per gli eventi di telemetria, oltre al filtro in Griglia di eventi, gli utenti possono anche filtrare in base ai dispositivi gemelli, alle proprietà e al corpo dei messaggi tramite la query di routing dei messaggi. 

Quando si sottoscrivono eventi di telemetria tramite Griglia di eventi, l'hub IoT crea una route dei messaggi predefinita per inviare i messaggi del dispositivo di tipo origine dati a Griglia di eventi. Per altre informazioni sul routing dei messaggi, vedere Routing dei [messaggi dell'hub IoT](iot-hub-devguide-messages-d2c.md). Questa route sarà visibile nel portale nell'hub IoT > routing dei messaggi. Viene creata una sola route a Griglia di eventi indipendentemente dal numero di sottoscrizioni EG create per gli eventi di telemetria. Pertanto, se sono necessarie più sottoscrizioni con filtri diversi, è possibile utilizzare l'operatore OR in queste query sulla stessa route. La creazione e l'eliminazione della route vengono controllate tramite la sottoscrizione di eventi di telemetria tramite Griglia di eventi. Non è possibile creare o eliminare una route a Griglia di eventi utilizzando il routing dei messaggi dell'hub IoT.You cannot create or delete a route to Event Grid using IoT Hub Message Routing.

Per filtrare i messaggi prima dell'invio dei dati di telemetria, è possibile aggiornare la query di [routing.](iot-hub-devguide-routing-query-syntax.md) Si noti che la query di routing può essere applicata al corpo del messaggio solo se il corpo è JSON. È inoltre necessario impostare contentType su **application/json** e contentEncoding su **UTF-8** nelle proprietà del sistema di [messaggi.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitazioni per gli eventi correlati a dispositivi connessi e disconnessi

Per ricevere gli eventi dello stato di connessione del dispositivo, un dispositivo deve eseguire un'operazione 'D2C Send Telemetry' OPPURE un'operazione 'C2D Receive Message' con Iot Hub.To receive device connection state events, a device must do either a 'D2C Send Telemetry' OPPURE a 'C2D Receive Message' operation with Iot Hub. Tuttavia, si noti che se un dispositivo utilizza il protocollo AMQP per connettersi con Hub Iot, è consigliabile eseguire un'operazione 'Messaggio di ricezione C2D' altrimenti le notifiche sullo stato della connessione potrebbero subire ritardi di pochi minuti. Se il dispositivo usa il protocollo MQTT, l'hub IoT mantiene aperto il collegamento C2D. Per AMQP, è possibile aprire il collegamento C2D chiamando [l'API Receive Async](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), per l'SDK di C, o il client del dispositivo dell'hub IoT per [AMQP](iot-hub-amqp-support.md#device-client).

Il collegamento D2C è aperto se si inviano dati di telemetria. 

Se la connessione del dispositivo è tremolante, il che significa che il dispositivo si connette e si disconnette frequentemente, non invieremo ogni singolo stato di connessione, ma pubblicheremo lo stato di connessione corrente preso in uno snapshot periodico, fino a quando lo sfarfallio continua. La ricezione dello stesso evento di stato della connessione con numeri di sequenza diversi o eventi di stato di connessione diversi significa che è stata apportata una modifica allo stato di connessione del dispositivo.

## <a name="tips-for-consuming-events"></a>Suggerimenti per l'utilizzo di eventi

Le applicazioni che gestiscono gli eventi dell'hub IoT devono seguire queste procedure consigliate:

* È possibile configurare più sottoscrizioni per instradare gli eventi allo stesso gestore eventi, pertanto non presupporre che gli eventi provengano da una determinata origine. Controllare sempre l'argomento del messaggio per assicurarsi che provengano dall'hub IoT previsto.

* Non presupporre che tutti gli eventi ricevuti siano del tipo previsto. Controllare sempre eventType prima di elaborare il messaggio.

* I messaggi possono arrivare senza ordine o dopo un ritardo. Utilizza il campo etag per capire se le tue informazioni sugli oggetti sono aggiornate per gli eventi creati dal dispositivo o eliminati dal dispositivo.

## <a name="next-steps"></a>Passaggi successivi

* [Try the IoT Hub events tutorial (Provare l'esercitazione sugli eventi dell'hub IoT)](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Informazioni sull'ordinamento di eventi di connessione e disconnessione dispositivi](iot-hub-how-to-order-connection-state-events.md)

* [Ulteriori informazioni su Griglia di eventi](../event-grid/overview.md)

* [Compare the differences between routing IoT Hub events and messages (Confrontare le differenze tra il routing degli eventi dell'hub IoT e quello dei messaggi)](iot-hub-event-grid-routing-comparison.md)

* [Informazioni su come usare gli eventi di telemetria IoT per implementare l'analisi spaziale IoT usando Azure MapsLearn how to use IoT telemetry events to implement IoT spatial analytics using Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
