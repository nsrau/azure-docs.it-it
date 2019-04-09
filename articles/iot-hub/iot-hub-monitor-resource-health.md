---
title: Monitorare l'integrità delle risorse dell'hub IoT di Azure | Microsoft Docs
description: Usare Monitoraggio di Azure e Integrità risorse di Azure per monitorare l'hub IoT e diagnosticare rapidamente i problemi
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: kgremban
ms.openlocfilehash: 6dea1add1e329cfc894068732898a856a69c9b4c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274043"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorare l'integrità dell'hub IoT di Azure ed eseguire la diagnostica rapida dei problemi

Le aziende che implementano l'hub IoT di Azure si aspettano prestazioni affidabili dalle proprie risorse. Che consentono di mantenere sotto controllo le operazioni, l'IoT Hub è completamente integrato con [monitoraggio di Azure](../azure-monitor/index.yml) e [integrità risorse di Azure](../service-health/resource-health-overview.md). Questi due servizi funzionano per fornire i dati che necessari per mantenere le soluzioni IoT operative e in esecuzione in uno stato integro.

Monitoraggio di Azure è una singola origine di monitoraggio e registrazione per tutti i servizi di Azure. È possibile inviare i log di diagnostica che genera l'errore monitoraggio di Azure a log di monitoraggio di Azure, hub eventi o archiviazione di Azure per l'elaborazione personalizzata. Le impostazioni di diagnostica e le metriche di Monitoraggio di Azure offrono visibilità sulle prestazioni delle risorse. Continuare a leggere questo articolo per informazioni su come [Usare Monitoraggio di Azure](#use-azure-monitor) con l'hub IoT. 

> [!IMPORTANT]
> Gli eventi generati dal servizio hub IoT tramite i log di diagnostica di Monitoraggio di Azure non sono necessariamente affidabili o ordinati. Alcuni eventi possono andare persi o recapitati non in ordine. I log di diagnostica, poi, è previsto che siano in tempo reale. La registrazione degli eventi nella destinazione scelta può richiedere diversi minuti.

Integrità risorse di Azure aiuta a diagnosticare gli eventuali problemi di Azure che possono influire negativamente sulle risorse e a ottenere il supporto necessario. Un dashboard fornisce lo stato di integrità correnti e precedenti per ognuno degli hub IoT. Passare alla sezione nella parte inferiore di questo articolo per informazioni su come [integrità risorse di Azure Usa](#use-azure-resource-health) con l'hub IoT. 

Hub IoT fornisce inoltre metriche specifiche che è possibile usare per comprendere lo stato delle risorse di IoT. Per altre informazioni, vedere [le metriche dell'IoT Hub comprendere](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Usare Monitoraggio di Azure

Monitoraggio di Azure fornisce informazioni di diagnostica per le risorse di Azure, il che significa che è possibile monitorare le operazioni che si verificano all'interno dell'hub IoT.

Le impostazioni di diagnostica di Monitoraggio di Azure sostituiscono il monitoraggio delle operazioni dell'hub IoT. Se si usa il monitoraggio delle operazioni, è necessario eseguire la migrazione dei flussi di lavoro. Per altre informazioni, vedere [Migrate dalle operazioni di impostazioni di monitoraggio per la diagnostica](iot-hub-migrate-to-diagnostics-settings.md).

Per altre informazioni sulle metriche specifiche e gli eventi che verifica la presenza di monitoraggio di Azure, vedere [metriche con monitoraggio di Azure supportate](../azure-monitor/platform/metrics-supported.md) e [supportati servizi, schemi e categorie per i log di diagnostica di Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Informazioni sui log

Monitoraggio di Azure tiene traccia delle diverse operazioni che si verificano nell'hub IoT. Ogni categoria dispone di uno schema che definisce la modalità in cui vengono segnalati gli eventi in tale categoria.

#### <a name="connections"></a>connessioni

La categoria Connessioni tiene traccia degli eventi di connessione e disconnessione del dispositivo da un hub IoT, nonché degli errori. Questa categoria è utile per identificare i tentativi di connessione non autorizzati e per inviare avvisi in caso di interruzione della connessione ai dispositivi.

> [!NOTE]
> Affidabile lo stato della connessione dei dispositivi controllare [heartbeat del dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Comandi da cloud a dispositivo

La categoria di comandi da cloud a dispositivo tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di messaggi da cloud a dispositivo. Questa categoria include errori che risultano da:

* Invio di messaggi da cloud a dispositivo (ad esempio, errori di mittente non autorizzato)
* Ricezione di messaggi da cloud a dispositivo (ad esempio, errori di superamento del numero di recapiti)
* Ricezione di feedback sui messaggi da cloud a dispositivo (ad esempio, errori di feedback scaduto)

Questa categoria non intercetta gli errori quando il messaggio da cloud a dispositivo viene recapitato correttamente ma viene gestito in modo non corretto dal dispositivo.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Operazioni relative alle identità dei dispositivi

La categoria di operazioni di identità del dispositivo tiene traccia degli errori che si verificano quando si prova a creare, aggiornare o eliminare una voce nel registro delle identità dell'hub IoT. Il rilevamento di questa categoria è utile per gli scenari di provisioning.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="routes"></a>Route

La categoria del routing dei messaggi tiene traccia degli errori che si verificano durante la valutazione del routing dei messaggi e dell'integrità dell'endpoint percepiti dall'hub IoT. Questa categoria include eventi di questo tipo:

* Una regola viene valutata come "non definita"
* Hub IoT contrassegna un endpoint come inattivo
* Qualsiasi errore ricevuto da un endpoint 

Questa categoria non include errori specifici sui messaggi stessi, come gli errori di limitazione sui dispositivi, che vengono inclusi nella categoria "Telemetria dei dispositivi".

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Telemetria dei dispositivi

La categoria di telemetria dei dispositivi tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di telemetria. Questa categoria include gli errori che si verificano durante l'invio di eventi di telemetria, ad esempio la limitazione, e la ricezione di eventi di telemetria, ad esempio un lettore non autorizzato. Questa categoria non può intercettare gli errori causati da codice in esecuzione nel dispositivo stesso.

```json
{
    "records":
    [
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
    ]
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
    "records":
    [
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
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operazioni da cloud a dispositivi gemelli

La categoria relativa alle operazioni da cloud a dispositivo gemello tiene traccia degli eventi avviati dal servizio su dispositivi gemelli. Queste operazioni possono includere il recupero del dispositivo gemello, l'aggiornamento o la sostituzione dei tag e l'aggiornamento o la sostituzione delle proprietà desiderate.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operazioni da dispositivo gemello a cloud

La categoria relativa alle operazioni da dispositivo gemello a cloud tiene traccia degli eventi avviati dal servizio su dispositivi gemelli. Queste operazioni possono includere il recupero del dispositivo gemello, l'aggiornamento delle proprietà segnalate e la sottoscrizione alle proprietà desiderate.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="twin-queries"></a>Query dei dispositivi gemelli

La categoria relativa alle query dei dispositivi gemelli segnala le richieste di query per i dispositivi gemelli che vengono avviati nel cloud.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="jobs-operations"></a>Operazioni dei processi

La categoria relativa alle operazioni dei processi segnala le richieste di processo per aggiornare i dispositivi gemelli o richiamare i metodi diretti su più dispositivi. Queste richieste vengono avviate nel cloud.

```json
{
    "records":
    [
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
    ]
}
```

#### <a name="direct-methods"></a>Metodi diretti

La categoria relativa ai metodi diretti tiene traccia delle interazioni di richiesta-risposta inviate ai singoli dispositivi. Queste richieste vengono avviate nel cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Traccia distribuita (anteprima)

La categoria relativa alla traccia distribuita tiene traccia degli ID di correlazione per i messaggi che contengono l'intestazione del contesto di traccia. Per abilitare completamente i log, il codice lato client deve essere aggiornato seguendo [analizza e diagnosticare IoT applicazioni end-to-end con analisi distribuita dell'IoT Hub (anteprima)](iot-hub-distributed-tracing.md).

Si noti che `correlationId` conforme al [contesto di analisi di W3C](https://github.com/w3c/trace-context) proposta, in cui contiene una `trace-id` nonché un `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Log dell'hub IoT D2C (da dispositivo a cloud)

L'hub IoT registra questo log all'arrivo di un messaggio contenente proprietà di traccia valide nell'hub IoT.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

In questo caso, il valore di `durationMs` non viene calcolato in quanto l'orologio dell'hub IoT potrebbe non essere sincronizzato con l'orologio del dispositivo e quindi il calcolo della durata potrebbe essere fuorviante. È consigliabile scrivere la logica usando i timestamp nella sezione `properties` per acquisire i picchi di latenza da dispositivo a cloud.

| Proprietà | Type | DESCRIZIONE |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Dimensione del messaggio da dispositivo a cloud in byte |
| **deviceId** | Stringa di caratteri alfanumerici ASCII a 7 bit | Identità del dispositivo |
| **callerLocalTimeUtc** | Timestamp UTC | Ora di creazione del messaggio in base a quanto indicato dall'orologio locale del dispositivo |
| **calleeLocalTimeUtc** | Timestamp UTC | Ora di arrivo del messaggio nel gateway dell'hub IoT in base a quanto indicato dall'orologio sul lato servizio dell'hub IoT |

##### <a name="iot-hub-ingress-logs"></a>Log di ingresso dell'hub IoT

L'hub IoT registra questo log quando un messaggio contenente proprietà di traccia valide scrive nell'hub eventi predefinito o interno.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Nel `properties` sezione, questo log contiene informazioni aggiuntive sui messaggi in ingresso.

| Proprietà | Type | DESCRIZIONE |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | string | True o false, indica se il routing dei messaggi è abilitato o meno nell'hub IoT |
| **parentSpanId** | string | Oggetto [span-id](https://w3c.github.io/trace-context/#parent-id) del messaggio padre, che in questo caso sarebbe la traccia del messaggio D2C |

##### <a name="iot-hub-egress-logs"></a>Log di uscita dell'hub IoT

L'hub IoT Hub registra questo log quando il [routing](iot-hub-devguide-messages-d2c.md) è abilitato e il messaggio viene scritto in un [endpoint](iot-hub-devguide-endpoints.md). Se il routing non è abilitato, l'hub IoT non registra questo log.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Nel `properties` sezione, questo log contiene informazioni aggiuntive sui messaggi in ingresso.

| Proprietà | Type | DESCRIZIONE |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | string | Nome dell'endpoint di routing |
| **endpointType** | string | Tipo dell'endpoint di routing |
| **parentSpanId** | string | Oggetto [span-id](https://w3c.github.io/trace-context/#parent-id) del messaggio padre, che in questo caso sarebbe la traccia del messaggio di ingresso nell'hub IoT |

### <a name="read-logs-from-azure-event-hubs"></a>Leggere i log da Hub eventi di Azure

Dopo aver configurato la registrazione degli eventi tramite le impostazioni di diagnostica, è possibile creare applicazioni che leggono i log in modo che sia possibile eseguire azioni sulla base delle informazioni in essi contenuti. Questo codice di esempio recupera i log da un hub eventi:

```csharp
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
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
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

L'hub IoT di Azure indica l'integrità a livello regionale. Se un'interruzione dell'alimentazione locale ha un impatto sull'hub IoT, lo stato di integrità appare come **Sconosciuto**. Per altre informazioni, vedere [tipi di risorse e controlli di integrità in integrità risorse di Azure](../service-health/resource-health-checks-resource-types.md).

Per controllare l'integrità degli hub IoT, seguire questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare a **Integrità del servizio** > **Integrità risorse**.

3. Le caselle di riepilogo a discesa, selezionare la sottoscrizione, quindi selezionare **IoT Hub** come il tipo di risorsa.

Per altre informazioni su come interpretare i dati di integrità, vedere [Panoramica su integrità risorse di Azure](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* [Comprendere le metriche dell'hub IoT](iot-hub-metrics.md)
* [Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale](iot-hub-monitoring-notifications-with-azure-logic-apps.md)