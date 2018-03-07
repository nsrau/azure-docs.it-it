---
title: Hub IoT e Griglia di eventi di Azure | Microsoft Docs
description: Usare Griglia di eventi di Azure per attivare processi basati su azioni eseguite nell'hub IoT.
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 7c75a65714898f27ab0008ad5a30a5714d7174f4
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni - Anteprima

L'hub IoT di Azure si integra con Griglia di eventi di Azure per poter inviare le notifiche degli eventi agli altri servizi e attivare processi downstream. Configurare le applicazioni aziendali per l'ascolto degli eventi dell'hub IoT in modo che sia possibile rispondere agli eventi critici in modo sicuro, scalabile e affidabile. Compilare, ad esempio, un'applicazione per eseguire più azioni, come l'aggiornamento di un database, la creazione di un ticket e il recapito di una notifica di posta elettronica, ogni volta che un nuovo dispositivo IoT viene registrato nell'hub IoT. 

[Griglia di eventi di Azure][lnk-eg-overview] è un servizio di routing di eventi completamente gestito che usa un modello di pubblicazione-sottoscrizione. Griglia di eventi include il supporto predefinito per i servizi di Azure, ad esempio [Funzioni di Azure](../azure-functions/functions-overview.md) e [App per la logica di Azure](../logic-apps/logic-apps-what-are-logic-apps.md), e può recapitare gli avvisi relativi agli eventi ai servizi non di Azure usando i webhook. Per un elenco completo dei gestori di eventi supportati da Griglia di eventi, vedere [Introduzione a Griglia di eventi di Azure][lnk-eg-overview]. 

![Architettura di Griglia di eventi di Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilità internazionale

L'integrazione di Griglia di eventi è disponibile per gli hub IoT situati nelle aree in cui Griglia di eventi è supportata. Per l'elenco aggiornato delle aree, vedere [Introduzione a Griglia di eventi di Azure][lnk-eg-overview]. 

## <a name="event-types"></a>Tipi di eventi

L'hub IoT pubblica i tipi di eventi seguenti: 

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Pubblicato quando un dispositivo viene registrato in un hub IoT. |
| Microsoft.Devices.DeviceDeleted | Pubblicato quando un dispositivo viene eliminato da un hub IoT. | 

Usare il portale di Azure o l'interfaccia della riga di comando di Azure per configurare gli eventi da pubblicare da ogni hub IoT. Per un esempio, provare l'esercitazione [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando App per la logica). 

## <a name="event-schema"></a>Schema di eventi

Gli eventi dell'hub IoT contengono tutte le informazioni necessarie per rispondere alle modifiche del ciclo di vita del dispositivo. Per identificare un evento dell'hub IoT, controllare se la proprietà eventType inizia con **Microsoft.Devices**. Per altre informazioni su come usare le proprietà degli eventi di Griglia di eventi, vedere lo [schema di eventi di Griglia di eventi](../event-grid/event-schema.md).

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

Per una descrizione dettagliata di ogni proprietà, vedere [Schema di eventi di Griglia di eventi di Azure per l'hub IoT](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Filtrare gli eventi

Le sottoscrizioni degli eventi dell'hub IoT possono filtrare gli eventi in base al tipo di evento e al nome del dispositivo. Il funzionamento dei filtri oggetto in Griglia di eventi avviene in base alle corrispondenze con **prefisso** e **suffisso**. Il filtro utilizza un `AND` operatore in modo che gli eventi con un oggetto che corrisponde sia al prefisso che al suffisso viene recapitato al sottoscrittore. 

L'oggetto di eventi IoT usa il formato:

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>Suggerimenti per l'utilizzo di eventi

Le applicazioni che gestiscono gli eventi dell'hub IoT devono seguire queste procedure consigliate:

* Poiché più sottoscrizioni potrebbero essere configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi provengano da una determinata origine. Controllare sempre l'argomento del messaggio per assicurarsi che provengano dall'hub IoT previsto. 
* Non presupporre che tutti gli eventi ricevuti siano del tipo previsto. Controllare sempre eventType prima di elaborare il messaggio.
* I messaggi possono arrivare senza ordine o dopo un ritardo. Usare il campo etag per determinare se le informazioni sugli oggetti sono aggiornate.



## <a name="next-steps"></a>Passaggi successivi

* [Try the IoT Hub events tutorial (Provare l'esercitazione sugli eventi dell'hub IoT)](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Altre informazioni su Griglia di eventi][lnk-eg-overview]
* [Compare the differences between routing IoT Hub events and messages (Confrontare le differenze tra il routing degli eventi dell'hub IoT e quello dei messaggi)][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md