---
title: Hub IoT e Griglia di eventi di Azure | Microsoft Docs
description: Usare Griglia di eventi di Azure per attivare processi basati su azioni eseguite nell'hub IoT.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 14bdbb5d629cb5a3fccd6f874e30ded0648e0124
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249469"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni

L'hub IoT di Azure si integra con Griglia di eventi di Azure per poter inviare le notifiche degli eventi agli altri servizi e attivare processi downstream. Configurare le applicazioni aziendali per l'ascolto degli eventi dell'hub IoT in modo che sia possibile rispondere agli eventi critici in modo sicuro, scalabile e affidabile. Compilare, ad esempio, un'applicazione per eseguire più azioni, come l'aggiornamento di un database, la creazione di un ticket e il recapito di una notifica di posta elettronica, ogni volta che un nuovo dispositivo IoT viene registrato nell'hub IoT. 

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

Per una descrizione dettagliata di ogni proprietà, vedere [Schema di eventi di Griglia di eventi di Azure per l'hub IoT](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Filtrare gli eventi

Le sottoscrizioni degli eventi dell'hub IoT possono filtrare gli eventi in base al tipo di evento e al nome del dispositivo. I filtri degli oggetti in Griglia di eventi funzionano in base alle corrispondenze **Inizia con** (prefisso) e **Termina con** (suffisso). Il filtro utilizza un `AND` operatore in modo che gli eventi con un oggetto che corrisponde sia al prefisso che al suffisso viene recapitato al sottoscrittore. 

L'oggetto di eventi IoT usa il formato:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitazioni per gli eventi correlati a dispositivi connessi e disconnessi

Per ricevere eventi correlati a dispositivi connessi e disconnessi, è necessario aprire il collegamento D2C o C2D per il dispositivo. Se il dispositivo usa il protocollo MQTT, l'hub IoT mantiene aperto il collegamento C2D. Per il protocollo AMQP, è possibile aprire il collegamento C2D chiamando l'[API ReceiveAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). 

Il collegamento D2C è aperto se si inviano dati di telemetria. Se la connessione del dispositivo è intermittente, ovvero se il dispositivo si connette e si disconnette frequentemente, non verrà inviato lo stato di ogni singola connessione, ma verrà pubblicati lo stato di connessione rilevato ogni minuto. In caso di interruzione del servizio dell'hub IoT, lo stato di connessione del dispositivo viene pubblicato al termine dell'interruzione. Se il dispositivo si disconnette durante il periodo di interruzione, l'evento di dispositivo disconnesso viene pubblicato entro 10 minuti.

## <a name="tips-for-consuming-events"></a>Suggerimenti per l'utilizzo di eventi

Le applicazioni che gestiscono gli eventi dell'hub IoT devono seguire queste procedure consigliate:

* Poiché più sottoscrizioni potrebbero essere configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi provengano da una determinata origine. Controllare sempre l'argomento del messaggio per assicurarsi che provengano dall'hub IoT previsto. 

* Non presupporre che tutti gli eventi ricevuti siano del tipo previsto. Controllare sempre eventType prima di elaborare il messaggio.

* I messaggi possono arrivare senza ordine o dopo un ritardo. Usare il campo etag per determinare se le informazioni sugli oggetti sono aggiornate.

## <a name="next-steps"></a>Passaggi successivi

* [Try the IoT Hub events tutorial (Provare l'esercitazione sugli eventi dell'hub IoT)](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Informazioni sull'ordinamento di eventi di connessione e disconnessione dispositivi](iot-hub-how-to-order-connection-state-events.md)
* [Altre informazioni su Griglia di eventi](../event-grid/overview.md)
* [Compare the differences between routing IoT Hub events and messages (Confrontare le differenze tra il routing degli eventi dell'hub IoT e quello dei messaggi)](iot-hub-event-grid-routing-comparison.md)
