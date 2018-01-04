---
title: Panoramica delle API .NET Standard di Azure Hub eventi | Microsoft Docs
description: Panoramica dell'API .NET Standard
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a173f8e4-556c-42b8-b856-838189f7e636
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 855f6e7f401621d7f923d68215ca880c05d38629
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="event-hubs-net-standard-api-overview"></a>Panoramica dell'API .NET Standard di Hub eventi

In questo articolo vengono riepilogate alcune delle principali API client .NET Standard di Hub eventi. Esistono attualmente due librerie client .NET Standard:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): fornisce tutte le operazioni di runtime di base.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): aggiunge nuove funzionalità che consente di tenere traccia di eventi elaborati e il modo più semplice da leggere da un hub eventi.

## <a name="event-hubs-client"></a>Client di Hub eventi

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) è l'oggetto principale da usare per inviare eventi, creare ricevitori e ottenere informazioni di runtime. Questo client è collegato a un determinato hub eventi e crea una nuova connessione all'endpoint di Hub eventi.

### <a name="create-an-event-hubs-client"></a>Creare un client di Hub eventi

Un oggetto [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) viene creato da una stringa di connessione. Nell'esempio seguente viene illustrato il modo più semplice per creare un'istanza di un nuovo client:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("{Event Hubs connection string}");
```

Per modificare la stringa di connessione a livello di codice, è possibile usare la classe [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) e passare la stringa di connessione come parametro a [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("{Event Hubs connection string}")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Inviare eventi

Per inviare eventi a un hub eventi, usare la classe [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata). Il corpo deve essere un array `byte` o un segmento di array `byte`.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Ricevere eventi

Il modo consigliato per ricevere eventi dall'hub eventi è tramite il [Host processore di eventi](#event-processor-host-apis), che fornisce funzionalità di automaticamente tenere traccia le informazioni di offset e la partizione di hub eventi. Tuttavia, in alcune situazioni per ricevere eventi è preferibile usare la flessibilità della libreria di Hub eventi di base.

#### <a name="create-a-receiver"></a>Creare un ricevitore

Ricevitori sono collegati a partizioni specifiche, pertanto per ricevere tutti gli eventi in un hub eventi, è necessario creare più istanze. È buona norma per ottenere le informazioni di partizione a livello di codice, anziché come hardcoded gli ID di partizione. A tale scopo, è possibile usare il metodo [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync).

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Poiché gli eventi non vengono mai rimosse da un hub di eventi (e solo scadono), è necessario specificare il punto di partenza appropriato. L'esempio seguente mostra le combinazioni possibili:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Usare un evento

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>API host processore di eventi

Queste API garantiscono resilienza ai processi di lavoro che possono diventare non disponibili, distribuendo tuttavia partizioni tra i ruoli di lavoro disponibili.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Di seguito è riportato un esempio di implementazione di [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli scenari di Hub eventi, visitare i collegamenti seguenti:

* [Che cos'è l'hub di eventi di Azure?](event-hubs-what-is-event-hubs.md)
* [Available Event Hubs apis](event-hubs-api-overview.md) (API di Hub eventi disponibili)

I riferimenti API .NET sono qui:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)