---
title: Hub IoT e Griglia di eventi di Azure | Microsoft Docs
description: Usare Griglia di eventi di Azure per attivare processi basati su azioni eseguite nell'hub IoT.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: a2bb961989d5bb1cc879b197e45d25b566c56e83
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906769"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni

L'hub IoT di Azure si integra con Griglia di eventi di Azure per poter inviare le notifiche degli eventi agli altri servizi e attivare processi downstream. Configurare le applicazioni aziendali per l'ascolto degli eventi dell'hub IoT in modo che sia possibile rispondere agli eventi critici in modo sicuro, scalabile e affidabile. Ad esempio, creare un'applicazione per l'aggiornamento di un database, creare un ticket di lavoro e recapitare una notifica di posta elettronica ogni volta che viene registrato un nuovo dispositivo Internet nell'hub Internet.

[Griglia di eventi di Azure](../event-grid/overview.md) è un servizio di routing di eventi completamente gestito che usa un modello di pubblicazione-sottoscrizione. Griglia di eventi include il supporto predefinito per i servizi di Azure, ad esempio [Funzioni di Azure](../azure-functions/functions-overview.md) e [App per la logica di Azure](../logic-apps/logic-apps-what-are-logic-apps.md), e può recapitare gli avvisi relativi agli eventi ai servizi non di Azure usando i webhook. Per un elenco completo dei gestori di eventi supportati da Griglia di eventi, vedere [Introduzione a Griglia di eventi di Azure](../event-grid/overview.md).

![Architettura di Griglia di eventi di Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilità internazionale

L'integrazione di Griglia di eventi è disponibile per gli hub IoT situati nelle aree in cui Griglia di eventi è supportata. Per l'elenco aggiornato delle aree, vedere [Introduzione a Griglia di eventi di Azure](../event-grid/overview.md).

## <a name="event-types"></a>Tipi di eventi

L'hub IoT pubblica i tipi di eventi seguenti:

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Pubblicato quando un dispositivo viene registrato in un hub IoT. |
| Microsoft.Devices.DeviceDeleted | Pubblicato quando un dispositivo viene eliminato da un hub IoT. |
| Microsoft.Devices.DeviceConnected | Pubblicato quando un dispositivo è connesso a un hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Pubblicato quando un dispositivo è disconnesso da un hub IoT. |
| Microsoft. Devices. DeviceTelemetry | Pubblicato quando un messaggio di telemetria del dispositivo viene inviato a un hub Internet |

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

### <a name="device-telemetry-schema"></a>Schema di telemetria del dispositivo

Il messaggio di telemetria del dispositivo deve essere in un formato JSON valido con contentType impostato su **Application/JSON** e ContentEncoding impostato su **UTF-8** nelle [proprietà del sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)del messaggio. Entrambe le proprietà non fanno distinzione tra maiuscole e minuscole. Se la codifica del contenuto non è impostata, l'hub Internet scriverà i messaggi nel formato con codifica base 64.

È possibile arricchire gli eventi di telemetria del dispositivo prima che vengano pubblicati in griglia di eventi selezionando l'endpoint come griglia di eventi. Per ulteriori informazioni, vedere [Cenni preliminari sugli arricchimenti dei messaggi](iot-hub-message-enrichments-overview.md).

L'esempio seguente illustra lo schema di un evento di telemetria del dispositivo:

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

Per una descrizione dettagliata di ogni proprietà, vedere [schema di eventi di griglia di eventi di Azure per l'hub](../event-grid/event-schema-iot-hub.md)Internet.

## <a name="filter-events"></a>Filtrare gli eventi

Le sottoscrizioni degli eventi dell'hub Internet possono filtrare gli eventi in base al tipo di evento, al contenuto dei dati e al soggetto, ovvero il nome del dispositivo.

Griglia di eventi consente di [filtrare](../event-grid/event-filtering.md) i tipi di eventi, gli oggetti e il contenuto dei dati. Durante la creazione della sottoscrizione di griglia di eventi, è possibile scegliere di sottoscrivere gli eventi di un sacco selezionato. I filtri degli oggetti in Griglia di eventi funzionano in base alle corrispondenze **Inizia con** (prefisso) e **Termina con** (suffisso). Il filtro utilizza un `AND` operatore in modo che gli eventi con un oggetto che corrisponde sia al prefisso che al suffisso viene recapitato al sottoscrittore.

L'oggetto di eventi IoT usa il formato:

```json
devices/{deviceId}
```

Griglia di eventi consente inoltre di filtrare gli attributi di ogni evento, incluso il contenuto dei dati. In questo modo è possibile scegliere quali eventi vengono recapitati in base al contenuto del messaggio di telemetria. Per visualizzare esempi, vedere [filtro avanzato](../event-grid/event-filtering.md#advanced-filtering) . Per filtrare il corpo del messaggio di telemetria, è necessario impostare contentType su **Application/JSON** e ContentEncoding su **UTF-8** nelle [proprietà del sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)del messaggio. Entrambe le proprietà non fanno distinzione tra maiuscole e minuscole.

Per gli eventi non di telemetria come DeviceConnected, DeviceDisconnected, DeviceCreated e DeviceDeleted, è possibile usare il filtro di griglia di eventi durante la creazione della sottoscrizione. Per gli eventi di telemetria, oltre al filtro in griglia di eventi, gli utenti possono anche filtrare in dispositivi gemelli, proprietà e corpo del messaggio tramite la query di routing dei messaggi. Viene creata una [Route](iot-hub-devguide-messages-d2c.md) predefinita nell'hub Internet, basata sulla sottoscrizione di griglia di eventi per la telemetria del dispositivo. Questa singola route può gestire tutte le sottoscrizioni di griglia di eventi. Per filtrare i messaggi prima dell'invio dei dati di telemetria, è possibile aggiornare la [query di routing](iot-hub-devguide-routing-query-syntax.md). Si noti che la query di routing può essere applicata al corpo del messaggio solo se il corpo è JSON. È inoltre necessario impostare contentType su **Application/JSON** e ContentEncoding su **UTF-8** nelle [proprietà del sistema](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)del messaggio.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitazioni per gli eventi correlati a dispositivi connessi e disconnessi

Per ricevere eventi correlati a dispositivi connessi e disconnessi, è necessario aprire il collegamento D2C o C2D per il dispositivo. Se il dispositivo usa il protocollo MQTT, l'hub IoT mantiene aperto il collegamento C2D. Per AMQP, è possibile aprire il collegamento C2D chiamando l' [API di ricezione asincrona](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

Il collegamento D2C è aperto se si inviano dati di telemetria. Se la connessione del dispositivo è tremolante, il che significa che il dispositivo si connette e si disconnette di frequente, non verrà inviato ogni singolo stato di connessione, ma verrà pubblicato lo stato di connessione di cui viene effettuato uno snapshot ogni minuto. In caso di interruzione del servizio dell'hub IoT, lo stato di connessione del dispositivo viene pubblicato al termine dell'interruzione. Se il dispositivo si disconnette durante il periodo di interruzione, l'evento di dispositivo disconnesso viene pubblicato entro 10 minuti.

## <a name="tips-for-consuming-events"></a>Suggerimenti per l'utilizzo di eventi

Le applicazioni che gestiscono gli eventi dell'hub IoT devono seguire queste procedure consigliate:

* È possibile configurare più sottoscrizioni per indirizzare gli eventi allo stesso gestore eventi, quindi non presupporre che gli eventi provengano da un'origine particolare. Controllare sempre l'argomento del messaggio per assicurarsi che provengano dall'hub IoT previsto.

* Non presupporre che tutti gli eventi ricevuti siano del tipo previsto. Controllare sempre eventType prima di elaborare il messaggio.

* I messaggi possono arrivare senza ordine o dopo un ritardo. Usare il campo ETag per capire se le informazioni sugli oggetti sono aggiornate per gli eventi creati dal dispositivo o eliminati dal dispositivo.

## <a name="next-steps"></a>Passaggi successivi

* [Try the IoT Hub events tutorial (Provare l'esercitazione sugli eventi dell'hub IoT)](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Informazioni sull'ordinamento di eventi di connessione e disconnessione dispositivi](iot-hub-how-to-order-connection-state-events.md).

* [Altre informazioni su Griglia di eventi](../event-grid/overview.md)

* [Compare the differences between routing IoT Hub events and messages (Confrontare le differenze tra il routing degli eventi dell'hub IoT e quello dei messaggi)](iot-hub-event-grid-routing-comparison.md)

* [Informazioni su come usare gli eventi di telemetria degli oggetti Internet per implementare l'analisi spaziale delle cose usando mappe di Azure](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
