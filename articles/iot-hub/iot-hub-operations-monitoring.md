---
title: Monitoraggio delle operazioni dell&quot;hub IoT di Azure | Documentazione Microsoft
description: Come usare il monitoraggio delle operazioni dell&quot;hub IoT di Azure per monitorare lo stato delle operazioni nell&quot;hub IoT in tempo reale.
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 8f72f2ca66a5d1394e87c7c0f8d8dff9da73732f
ms.openlocfilehash: 612ef94efb9776ae0ce768de1b59fb208824da93


---
# <a name="iot-hub-operations-monitoring"></a>Monitoraggio delle operazioni dell'hub IoT
Il monitoraggio delle operazioni dell'hub IoT consente di monitorare lo stato delle operazioni nel proprio hub IoT in tempo reale. L'hub IoT tiene traccia degli eventi nelle diverse categorie di operazioni. È possibile scegliere di impostare l'invio di eventi da una o più categorie a un endpoint del proprio hub IoT per l'elaborazione. È possibile monitorare i dati per individuare gli errori o configurare un'elaborazione più complessa in base ai modelli di dati.

L'hub IoT monitora sei categorie di eventi:

* Operazioni relative alle identità dei dispositivi
* Telemetria dei dispositivi
* Messaggi da cloud a dispositivo
* Connessioni
* Caricamenti di file
* Routing dei messaggi

## <a name="how-to-enable-operations-monitoring"></a>Come abilitare il monitoraggio delle operazioni
1. Creare un hub IoT. Le istruzioni sulla creazione di un hub IoT sono disponibili nella [Guida introduttiva][lnk-get-started].
2. Aprire il pannello dell'hub IoT. Da qui, fare clic su **Monitoraggio operazioni**.
   
    ![][1]
3. Selezionare le categorie di monitoraggio da controllare e fare clic su **Salva**. Gli eventi sono disponibili per la lettura nell'endpoint compatibile con l'hub eventi elencato in **Impostazioni di monitoraggio**. L'endpoint dell'hub IoT è chiamato `messages/operationsmonitoringevents`.
   
    ![][2]

> [!NOTE]
> La selezione del monitoraggio **Dettagliato** per la categoria **Connessioni** consente all'hub IoT di generare messaggi di diagnostica aggiuntivi. Per tutte le altre categorie, l'impostazione **Dettagliato** modifica la quantità di informazioni che l'hub IoT include in ogni messaggio di errore.

## <a name="event-categories-and-how-to-use-them"></a>Categorie di eventi e modalità d'uso
Ogni categoria di monitoraggio delle operazioni tiene traccia di un diverso tipo di interazione con l'hub IoT e ogni categoria di monitoraggio ha uno schema che definisce come sono strutturati gli eventi nella categoria stessa.

### <a name="device-identity-operations"></a>Operazioni relative alle identità dei dispositivi
La categoria di operazioni di identità del dispositivo tiene traccia degli errori che si verificano quando si prova a creare, aggiornare o eliminare una voce nel registro delle identità dell'hub IoT. Il rilevamento di questa categoria è utile per gli scenari di provisioning.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Telemetria dei dispositivi
La categoria di telemetria dei dispositivi tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di telemetria. Questa categoria include gli errori che si verificano durante l'invio di eventi di telemetria, ad esempio la limitazione, e la ricezione di eventi di telemetria, ad esempio un lettore non autorizzato. Si noti che questa categoria non può intercettare gli errori causati da codice in esecuzione nel dispositivo stesso.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Comandi da cloud a dispositivo
La categoria di comandi da cloud a dispositivo tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di messaggi da cloud a dispositivo. Questa categoria include gli errori che si verificano durante l'invio di messaggi da cloud a dispositivo, ad esempio un mittente non autorizzato, la ricezione di messaggi da cloud a dispositivo, ad esempio il superamento del numero di recapiti, e la ricezione di commenti sui messaggi da cloud a dispositivo, ad esempio commenti scaduti. Questa categoria non intercetta gli errori da un dispositivo che gestisce in modo non corretto un messaggio da cloud a dispositivo, se questo è stato recapitato correttamente.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Connessioni
La categoria Connessioni tiene traccia degli errori che si verificano quando i dispositivi si connettono o disconnettono da un hub IoT. Il rilevamento di questa categoria è utile per identificare i tentativi di connessione non autorizzati e per rilevare quando una connessione viene persa dai dispositivi in aree di scarsa connettività.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Caricamenti di file

La categoria di caricamenti dei file tiene traccia degli errori che si verificano nell'hub IoT e correlati alla funzionalità di caricamento dei file. Questa categoria include:

- Errori che si verificano con l'URI di firma di accesso condiviso, ad esempio quando l'URI scade prima che un dispositivo notifichi all'hub un caricamento completato.
- Caricamenti non riusciti segnalati dal dispositivo.
- Errori che si verificano quando un file non viene trovato nell'archivio durante la creazione del messaggio di notifica dell'hub IoT.

Questa categoria non può intercettare errori che si verificano direttamente mentre il dispositivo sta caricando un file in memoria.


    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

### <a name="message-routing"></a>Routing dei messaggi
La categoria del routing dei messaggi tiene traccia degli errori che si verificano durante la valutazione del routing dei messaggi e dell'integrità dell'endpoint percepiti dall'hub IoT. Questa categoria include eventi come ad esempio quando una regola viene valutata come "non definita", quando l'hub IoT contrassegna un endpoint come inattivo ed eventuali altri errori ricevuti da un endpoint. Si noti che questa categoria non include errori specifici sugli stessi messaggi, ad esempio gli errori di limitazione sui dispositivi, che vengono segnalati nella categoria dei "dati di telemetria del dispositivo".
        
    {
        "messageSizeInBytes": 1234,
        "time": "UTC timestamp",
        "operationName": "ingress",
        "category": "routes",
        "level": "Error",
        "deviceId": "device-ID",
        "messageId": "ID of message",
        "routeName": "myroute",
        "endpointName": "myendpoint",
        "details": "ExternalEndpointDisabled"
    }

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Feb17_HO2-->


