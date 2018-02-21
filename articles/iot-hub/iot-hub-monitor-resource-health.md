---
title: "Monitorare l'integrità delle risorse dell'hub IoT di Azure | Microsoft Docs"
description: "Usare Monitoraggio di Azure e Integrità risorse di Azure per monitorare l'hub IoT e diagnosticare rapidamente i problemi"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 3051af03d0c1433db98bcc674a072188e7ce80e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorare l'integrità dell'hub IoT di Azure ed eseguire la diagnostica rapida dei problemi

Le aziende che implementano l'hub IoT di Azure si aspettano prestazioni affidabili dalle proprie risorse. Per aiutare l'utente a mantenere sotto controllo le proprie operazioni, l'hub IoT è completamente integrato con [Monitoraggio di Azure] [ lnk-AM] e [Integrità risorse di Azure] [ lnk-ARH]. Questi due servizi funzionano insieme per fornire i dati necessari per mantenere le soluzioni IoT operative in uno stato integro. 

Monitoraggio di Azure è una singola origine di monitoraggio e registrazione per tutti i servizi di Azure. È possibile inviare i log generati da Monitoraggio di Azure a Log Analytics di OMS, Hub eventi o Archiviazione di Azure per l'elaborazione personalizzata. Le impostazioni di diagnostica e le metriche di Monitoraggio di Azure offrono una visibilità in tempo reale sulle prestazioni delle risorse. Continuare a leggere questo articolo per informazioni su come [Usare Monitoraggio di Azure](#use-azure-monitor) con l'hub IoT. 

Integrità risorse di Azure aiuta a diagnosticare gli eventuali problemi di Azure che possono influire negativamente sulle risorse e a ottenere il supporto necessario. Un dashboard personalizzato fornisce lo stato di integrità corrente e precedente per gli hub IoT. Continuare a leggere questo articolo per informazioni su come [Usare Integrità risorse di Azure](#use-azure-resource-health) con l'hub IoT. 

Oltre all'integrazione con questi due servizi, l'hub IoT fornisce le proprie metriche utilizzabili per comprendere lo stato delle risorse di IoT. Per altre informazioni, vedere [Comprendere le metriche dell'hub IoT][lnk-metrics].

## <a name="use-azure-monitor"></a>Usare Monitoraggio di Azure

Monitoraggio di Azure fornisce informazioni di diagnostica a livello di risorsa, il che significa che è possibile monitorare le operazioni che si verificano all'interno dell'hub IoT. 

Le impostazioni di diagnostica di Monitoraggio di Azure sostituiscono il monitoraggio delle operazioni dell'hub IoT. Se si usa il monitoraggio delle operazioni, è necessario eseguire la migrazione dei flussi di lavoro. Per altre informazioni, vedere [Migrate from operations monitoring to diagnostics settings (Eseguire la migrazione dalla funzionalità Monitoraggio operazioni a Impostazioni di diagnostica)][lnk-migrate].

Per altre informazioni sulle metriche specifiche e gli eventi che Monitoraggio di Azure controlla, vedere [Metriche supportate con il monitoraggio di Azure] [ lnk-AM-metrics] e [Servizi, schemi e categorie supportati per i log di Diagnostica di Azure][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Informazioni sui log

Monitoraggio di Azure tiene traccia delle diverse operazioni che si verificano nell'hub IoT. Ogni categoria dispone di uno schema che definisce la modalità in cui vengono segnalati gli eventi in tale categoria. 

#### <a name="connections"></a>connessioni

La categoria Connessioni tiene traccia degli errori che si verificano quando i dispositivi si connettono o disconnettono da un hub IoT. Il rilevamento di questa categoria è utile per identificare i tentativi di connessione non autorizzati e per rilevare quando una connessione viene persa dai dispositivi in aree di scarsa connettività.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Comandi da cloud a dispositivo

La categoria di comandi da cloud a dispositivo tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di messaggi da cloud a dispositivo. Questa categoria include gli errori che si verificano durante l'invio di messaggi da cloud a dispositivo, ad esempio un mittente non autorizzato, la ricezione di messaggi da cloud a dispositivo, ad esempio il superamento del numero di recapiti, e la ricezione di commenti sui messaggi da cloud a dispositivo, ad esempio commenti scaduti. Questa categoria non intercetta gli errori da un dispositivo che gestisce in modo non corretto un messaggio da cloud a dispositivo, se questo è stato recapitato correttamente.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>Operazioni relative alle identità dei dispositivi

La categoria di operazioni di identità del dispositivo tiene traccia degli errori che si verificano quando si prova a creare, aggiornare o eliminare una voce nel registro delle identità dell'hub IoT. Il rilevamento di questa categoria è utile per gli scenari di provisioning.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>Route

La categoria del routing dei messaggi tiene traccia degli errori che si verificano durante la valutazione del routing dei messaggi e dell'integrità dell'endpoint percepiti dall'hub IoT. Questa categoria include eventi come ad esempio quando una regola viene valutata come "non definita", quando l'hub IoT contrassegna un endpoint come inattivo ed eventuali altri errori ricevuti da un endpoint. Questa categoria non include errori specifici sui messaggi stessi, ad esempio gli errori di limitazione sui dispositivi, che vengono segnalati nella categoria "telemetria del dispositivo".

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Telemetria dei dispositivi

La categoria di telemetria dei dispositivi tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di telemetria. Questa categoria include gli errori che si verificano durante l'invio di eventi di telemetria, ad esempio la limitazione, e la ricezione di eventi di telemetria, ad esempio un lettore non autorizzato. Questa categoria non può intercettare gli errori causati da codice in esecuzione nel dispositivo stesso.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>Operazioni di caricamento file

La categoria di caricamenti dei file tiene traccia degli errori che si verificano nell'hub IoT e correlati alla funzionalità di caricamento dei file. Questa categoria include:

* Errori che si verificano con l'URI di firma di accesso condiviso, ad esempio quando l'URI scade prima che un dispositivo notifichi all'hub un caricamento completato.
* Caricamenti non riusciti segnalati dal dispositivo.
* Errori che si verificano quando un file non viene trovato nell'archivio durante la creazione del messaggio di notifica dell'hub IoT.

Questa categoria non può intercettare errori che si verificano direttamente mentre il dispositivo sta caricando un file in memoria.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operazioni da cloud a dispositivi gemelli

La categoria relativa alle operazioni da cloud a dispositivo gemello tiene traccia degli eventi avviati dal servizio su dispositivi gemelli. Queste operazioni possono includere il recupero del dispositivo gemello, l'aggiornamento delle proprietà segnalate e la sottoscrizione alle proprietà desiderate

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operazioni da dispositivo gemello a cloud

La categoria relativa alle operazioni da dispositivo gemello a cloud tiene traccia degli eventi avviati dal servizio su dispositivi gemelli. Queste operazioni possono includere il recupero del dispositivo gemello, l'aggiornamento o la sostituzione dei tag e l'aggiornamento o la sostituzione delle proprietà desiderate. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>Query dei dispositivi gemelli

La categoria relativa alle query dei dispositivi gemelli segnala le richieste di query per i dispositivi gemelli che vengono avviati nel cloud. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>Operazioni dei processi

La categoria relativa alle operazioni dei processi segnala le richieste di processo per aggiornare i dispositivi gemelli o richiamare i metodi diretti su più dispositivi. Queste richieste vengono avviate nel cloud. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>Metodi diretti

La categoria relativa ai metodi diretti tiene traccia delle interazioni di richiesta-risposta inviate ai singoli dispositivi. Queste richieste vengono avviate nel cloud. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Leggere i log da Hub eventi di Azure

Dopo aver configurato la registrazione degli eventi tramite le impostazioni di diagnostica, è possibile creare applicazioni che leggono i log in modo che sia possibile eseguire azioni sulla base delle informazioni in essi contenuti. Questo codice di esempio recupera i log da un hub eventi:

```
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
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
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Usare Integrità risorse di Azure

Usare Integrità risorse di Azure per controllare se l'hub IoT sia operativo. È inoltre possibile capire se un'interruzione dell'alimentazione locale stia influenzando l'integrità dell'hub IoT. Per comprendere i dettagli specifici sullo stato di integrità dell'hub IoT di Azure, è consigliabile [Usare Monitoraggio di Azure](#use-azure-monitor). 

L'hub IoT di Azure indica l'integrità a livello regionale. Se un'interruzione dell'alimentazione locale sta influenzando l'hub IoT, lo stato di integrità appare come **Sconosciuto**. Per altre informazioni sui controlli di integrità specifici che Integrità risorse di Azure esegue, vedere [Tipi di risorse e controlli di integrità in Integrità risorse di Azure][lnk-ARH-checks].

Per controllare l'integrità degli hub IoT, seguire questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Integrità del servizio** > **Integrità risorse**.
1. Dall'elenco a discesa, selezionare la propria sottoscrizione e **Hub IoT**.

Per altre informazioni su come interpretare i dati di integrità, vedere [Panoramica su Integrità risorse di Azure][lnk-ARH]

## <a name="next-steps"></a>Passaggi successivi

- [Comprendere le metriche dell'hub IoT][lnk-metrics]
- [Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md