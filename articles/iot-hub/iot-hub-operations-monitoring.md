<properties
 pageTitle="Monitoraggio delle operazioni dell'hub IoT"
 description="Panoramica del monitoraggio delle operazioni dell'hub IoT che consente agli utenti di monitorare lo stato delle operazioni nel proprio hub IoT in tempo reale"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="nberdy"/>

# Introduzione al monitoraggio delle operazioni

Il monitoraggio delle operazioni dell'hub IoT consente agli utenti di monitorare lo stato delle operazioni nel proprio hub IoT in tempo reale. L'hub IoT tiene traccia degli eventi nelle diverse categorie di operazioni e gli utenti possono scegliere di impostare l'invio di eventi da una o più categorie a un endpoint del proprio hub IoT per l'elaborazione. Gli utenti possono monitorare i dati per individuare gli errori o configurare un'elaborazione più complessa in base ai modelli di dati.

L'hub IoT Hub monitora quattro categorie di eventi:

- Operazioni di identità del dispositivo
- Telemetria dei dispositivi
- Comandi da cloud a dispositivo
- Connessioni

## Come abilitare il monitoraggio delle operazioni

1. Creare un hub IoT. È possibile trovare istruzioni su come creare un hub IoT in [Introduzione all'hub IoT di Azure][lnk-get-started].

2. Aprire il pannello dell'hub IoT. Nel pannello fare clic su **Tutte le impostazioni** e quindi su **Monitoraggio operazioni**.

    ![][1]

3. Selezionare le categorie di monitoraggio da controllare e quindi fare clic su **Salva**. Gli eventi sono disponibili per la lettura nell'endpoint compatibile con l'hub eventi elencato in **Impostazioni di monitoraggio**. L'endpoint IoT Hub è chiamato `messages/operationsmonitoringevents`.

    ![][2]

## Categorie di eventi e modalità d'uso

Ogni categoria di monitoraggio delle operazioni tiene traccia di un diverso tipo di interazione con l'hub IoT e ogni categoria di monitoraggio ha uno schema che definisce come sono strutturati gli eventi nella categoria stessa.

### Operazioni relative alle identità dei dispositivi

La categoria di operazioni di identità del dispositivo tiene traccia degli errori che si verificano quando l'utente prova a creare, aggiornare o eliminare una voce nel registro delle identità dell'hub IoT. Il rilevamento di questa categoria è utile per gli scenari di provisioning.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": “userAgent”,
         "sharedAccessPolicy": "accessPolicy"
    }

### Telemetria dei dispositivi

La categoria di telemetria dei dispositivi tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di telemetria. Sono inclusi gli errori che si verificano durante l'invio di eventi di telemetria, ad esempio la limitazione, e la ricezione di eventi di telemetria, ad esempio un lettore non autorizzato. Si noti che questa categoria non può intercettare gli errori causati da codice in esecuzione nel dispositivo stesso.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
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

### Comandi da cloud a dispositivo

La categoria di comandi da cloud a dispositivo tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di comandi dei dispositivi. Sono inclusi gli errori che si verificano durante l'invio di comandi, ad esempio un mittente non autorizzato, la ricezione di comandi, ad esempio il superamento del numero di recapiti, e la ricezione di commenti sui comandi, ad esempio commenti scaduti. Questa categoria non intercetta gli errori da un dispositivo che gestisce in modo non corretto un comando, se questo è stato recapitato correttamente.

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

### Connessioni

La categoria Connessioni tiene traccia degli errori che si verificano quando i dispositivi si connettono o disconnettono da un hub IoT. Il rilevamento di questa categoria è utile per identificare i tentativi di connessione non autorizzati e per rilevare quando una connessione viene persa dai dispositivi in aree di scarsa connettività.

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

### Caricamenti di file

La categoria di caricamenti dei file tiene traccia degli errori che si verificano nell'hub IoT e correlati alla funzionalità di caricamento dei file. Questo include errori che si verificano con l'URI della firma di accesso condiviso (ad esempio, quando scade prima che un dispositivo invii una notifica all'hub riguardo a un caricamento completato), con caricamenti non riusciti segnalati dal dispositivo e quando non è possibile trovare un file in memoria durante la creazione dei messaggi di notifica dell’hub IoT. Notare che questa categoria non può intercettare errori che si verificano direttamente mentre il dispositivo sta caricando un file in memoria.

    {
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

## Passaggi successivi

Al termine di questa panoramica del monitoraggio delle operazioni, è possibile usare i collegamenti seguenti per altre informazioni:

- [Metrica di diagnostica dell'hub IoT][lnk-diagnostic-metrics]
- [Ridimensionamento dell'hub IoT][lnk-scaling]
- [Disponibilità elevata e ripristino di emergenza dell'hub IoT][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0608_2016-->