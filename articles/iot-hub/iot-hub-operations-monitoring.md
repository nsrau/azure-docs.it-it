---
title: Monitoraggio delle operazioni dell&quot;hub IoT di Azure | Microsoft Docs
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
ms.date: 03/24/2017
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 68050e31087e9e615dfe715509eeb6f14ecefbab
ms.lasthandoff: 03/31/2017


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

1. Aprire il pannello dell'hub IoT. Da qui, fare clic su **Monitoraggio operazioni**.

    ![Accedere alla configurazione del monitoraggio delle operazioni nel portale][1]

1. Selezionare le categorie di monitoraggio da controllare e fare clic su **Salva**. Gli eventi sono disponibili per la lettura nell'endpoint compatibile con l'hub eventi elencato in **Impostazioni di monitoraggio**. L'endpoint dell'hub IoT è chiamato `messages/operationsmonitoringevents`.

    ![Configurare il monitoraggio delle operazioni sull'hub IoT][2]

> [!NOTE]
> La selezione del monitoraggio **Dettagliato** per la categoria **Connessioni** consente all'hub IoT di generare messaggi di diagnostica aggiuntivi. Per tutte le altre categorie, l'impostazione **Dettagliato** modifica la quantità di informazioni che l'hub IoT include in ogni messaggio di errore.

## <a name="event-categories-and-how-to-use-them"></a>Categorie di eventi e modalità d'uso

Ogni categoria di monitoraggio delle operazioni tiene traccia di un diverso tipo di interazione con l'hub IoT e ogni categoria di monitoraggio ha uno schema che definisce come sono strutturati gli eventi nella categoria stessa.

### <a name="device-identity-operations"></a>Operazioni relative alle identità dei dispositivi

La categoria di operazioni di identità del dispositivo tiene traccia degli errori che si verificano quando si prova a creare, aggiornare o eliminare una voce nel registro delle identità dell'hub IoT. Il rilevamento di questa categoria è utile per gli scenari di provisioning.

```json
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
```

### <a name="device-telemetry"></a>Telemetria dei dispositivi

La categoria di telemetria dei dispositivi tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di telemetria. Questa categoria include gli errori che si verificano durante l'invio di eventi di telemetria, ad esempio la limitazione, e la ricezione di eventi di telemetria, ad esempio un lettore non autorizzato. Questa categoria non può intercettare gli errori causati da codice in esecuzione nel dispositivo stesso.

```json
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
```

### <a name="cloud-to-device-commands"></a>Comandi da cloud a dispositivo

La categoria di comandi da cloud a dispositivo tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di messaggi da cloud a dispositivo. Questa categoria include gli errori che si verificano durante l'invio di messaggi da cloud a dispositivo, ad esempio un mittente non autorizzato, la ricezione di messaggi da cloud a dispositivo, ad esempio il superamento del numero di recapiti, e la ricezione di commenti sui messaggi da cloud a dispositivo, ad esempio commenti scaduti. Questa categoria non intercetta gli errori da un dispositivo che gestisce in modo non corretto un messaggio da cloud a dispositivo, se questo è stato recapitato correttamente.

```json
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
```

### <a name="connections"></a>Connessioni

La categoria Connessioni tiene traccia degli errori che si verificano quando i dispositivi si connettono o disconnettono da un hub IoT. Il rilevamento di questa categoria è utile per identificare i tentativi di connessione non autorizzati e per rilevare quando una connessione viene persa dai dispositivi in aree di scarsa connettività.

```json
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
```

### <a name="file-uploads"></a>Caricamenti di file

La categoria di caricamenti dei file tiene traccia degli errori che si verificano nell'hub IoT e correlati alla funzionalità di caricamento dei file. Questa categoria include:

* Errori che si verificano con l'URI di firma di accesso condiviso, ad esempio quando l'URI scade prima che un dispositivo notifichi all'hub un caricamento completato.
* Caricamenti non riusciti segnalati dal dispositivo.
* Errori che si verificano quando un file non viene trovato nell'archivio durante la creazione del messaggio di notifica dell'hub IoT.

Questa categoria non può intercettare errori che si verificano direttamente mentre il dispositivo sta caricando un file in memoria.

```json
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
```

### <a name="message-routing"></a>Routing dei messaggi

La categoria del routing dei messaggi tiene traccia degli errori che si verificano durante la valutazione del routing dei messaggi e dell'integrità dell'endpoint percepiti dall'hub IoT. Questa categoria include eventi come ad esempio quando una regola viene valutata come "non definita", quando l'hub IoT contrassegna un endpoint come inattivo ed eventuali altri errori ricevuti da un endpoint. Questa categoria non include errori specifici sui messaggi stessi, ad esempio gli errori di limitazione sui dispositivi, che vengono segnalati nella categoria "telemetria del dispositivo".

```json
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
```

## <a name="view-events"></a>Visualizzare eventi

È possibile usare lo strumento *iothub-explorer* per verificare rapidamente che l'hub IoT stia generando eventi di monitoraggio. Per installare lo strumento, vedere le istruzioni disponibili nel repository GitHub [iothub-explorer][lnk-iothub-explorer].

1. Assicurarsi che la categoria di monitoraggio **Connessioni** sia impostata su **Dettagliato** nel portale.

1. Al prompt dei comandi eseguire il comando seguente per consentire la lettura dell'endpoint di monitoraggio:

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. A un altro prompt dei comandi eseguire il comando seguente per simulare un dispositivo che invia messaggi da dispositivo a cloud:

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. Il primo prompt dei comandi visualizza gli eventi di monitoraggio nel momento in cui il dispositivo simulato si connette all'hub IoT.

## <a name="connect-to-the-monitoring-endpoint"></a>Connettersi all'endpoint di monitoraggio

L'endpoint di monitoraggio sull'hub IoT è un endpoint compatibile con Hub eventi. Per leggere i messaggi di monitoraggio da questo endpoint, è possibile usare qualsiasi meccanismo che funzioni con l'Hub eventi. L'esempio seguente crea un lettore di base non adatto per una distribuzione con velocità effettiva elevata. Per altre informazioni su come elaborare i messaggi da Hub eventi, vedere l'esercitazione [Introduzione all'Hub eventi][lnk-eventhubs-tutorial].

Per connettersi all'endpoint di monitoraggio, è necessaria una stringa di connessione e il nome dell'endpoint. La procedura seguente mostra come trovare i valori necessari nel portale:

1. Nel portale, passare al pannello di risorse dell'hub IoT.

1. Scegliere **Monitoraggio operazioni** e prendere nota dei valori in **Nome compatibile con Hub eventi** e in **Endpoint compatibile con Hub eventi**:

    ![Valori di Endpoint compatibile con Hub eventi][img-endpoints]

1. Scegliere **Criteri di accesso condiviso**, quindi scegliere **servizio**. Prendere nota del valore presente in **Chiave primaria**:

    ![Chiave primaria del servizio dei criteri di accesso condiviso][img-service-key]

L'esempio di codice C# seguente viene preso da un'app console C# per **Desktop classico di Windows** di Visual Studio. Il progetto ha il pacchetto **WindowsAzure.ServiceBus** NuGet installato.

* Sostituire il placeholder della stringa di connessione con una stringa di connessione che usa i valori precedentemente annotati per l'**endpoint compatibile con Hub eventi** e il servizio **Chiave primaria** come illustrato nell'esempio seguente:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Sostituire il placeholder del nome dell'endpoint di monitoraggio con il valore del **nome Hub eventi compatibile** annotato in precedenza.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
