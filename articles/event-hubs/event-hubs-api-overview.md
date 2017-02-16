---
title: Panoramica delle API Hub eventi di Azure | Documentazione Microsoft
description: Un riepilogo di alcuni client .NET di Hub eventi chiave API.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7f3b6cc0-9600-417f-9e80-2345411bd036
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: c68b5cfadc3e98e15c119b3717c8098887ca67d0
ms.openlocfilehash: e8c97689f84d3bedb312ec4443427181a542e95b


---
# <a name="event-hubs-api-overview"></a>Panoramica dell'API Hub eventi
In questo articolo vengono riepilogati alcuni dei principali API client .NET di Hub eventi. Esistono due categorie: API di runtime e gestione. Le API di runtime si compongono di tutte le operazioni necessarie per inviare e ricevere un messaggio. Operazioni di gestione consentono di gestire lo stato dell'entità di Hub eventi per la creazione, aggiornamento ed eliminazione di entità.

Gli scenari di monitoraggio comprendono sia la gestione che il runtime. Per la documentazione di riferimento dettagliata sulle API .NET, vedere i riferimenti [.NET del bus di servizio](/dotnet/api) e [API EventProcessorHost](/dotnet/api).

## <a name="management-apis"></a>API di gestione
Per eseguire le operazioni di gestione riportate di seguito sono necessarie autorizzazioni di **gestione** per lo spazio dei nomi di Hub eventi:

### <a name="create"></a>Create
```csharp
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### <a name="update"></a>Aggiornamento
```csharp
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### <a name="delete"></a>Elimina
```csharp
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## <a name="run-time-apis"></a>API di runtime
### <a name="create-publisher"></a>Crea editore
```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>Pubblica messaggio
```csharp
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Crea consumer
```csharp
// Create the Event Hubs client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
EventHubConsumerGroup defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Utilizzare messaggio
```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>API host processore di eventi
Queste API garantiscono resilienza ai processi di lavoro che possono diventare non disponibili, distribuendo tuttavia partizioni tra thread di lavoro disponibili.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

L'interfaccia [IEventProcessor](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) è definita come segue:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli scenari di Hub eventi, visitare i collegamenti seguenti:

* [Che cos'è l'hub di eventi di Azure?](event-hubs-what-is-event-hubs.md)
* [Panoramica di Hub eventi](event-hubs-overview.md)
* [Guida alla programmazione di Hub eventi](event-hubs-programming-guide.md)
* [Esempi di codice di Hub eventi](http://code.msdn.microsoft.com/site/search?query=event hub&f\[0\].Value=event hubs&f\[0\].Type=SearchText&ac=5)

I riferimenti API .NET sono qui:

* [Riferimenti API .NET di Hub eventi e del bus di servizio](/dotnet/api)
* [Riferimento all'API dell'host processore di eventi](/dotnet/api)




<!--HONumber=Nov16_HO4-->


