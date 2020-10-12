---
title: Disponibilità e coerenza - Hub eventi di Azure | Microsoft Docs
description: Come fornire la quantità massima di disponibilità e coerenza con l'Hub eventi di Azure usando le partizioni.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 774332b8f2d5c336f1a22d717516ae35a62b341f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000635"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilità e coerenza nell'Hub eventi

## <a name="overview"></a>Panoramica
Hub eventi di Azure usa un [modello di partizionamento](event-hubs-scalability.md#partitions) per migliorare la disponibilità e la parallelizzazione all'interno di un singolo hub eventi. Se ad esempio un hub eventi include quattro partizioni e una di queste partizioni viene spostata da un server a un altro in un'operazione di bilanciamento del carico, è comunque possibile inviare e ricevere dalle altre tre partizioni. Più partizioni consentono anche di avere più lettori simultaneamente che elaborano i dati, migliorando la velocità effettiva di aggregazione. Comprendere le implicazioni del partizionamento e dell'ordinamento in un sistema distribuito è un aspetto critico della progettazione di una soluzione.

Per spiegare il compromesso tra ordinamento e disponibilità, vedere il [teorema CAP](https://en.wikipedia.org/wiki/CAP_theorem), noto anche come teorema di Brewer. Il teorema discute la scelta tra coerenza, disponibilità e tolleranza di partizione. Afferma che per i sistemi partizionati dalla rete è sempre presente un compromesso tra disponibilità e coerenza.

Il teorema di Brewer definisce coerenza e disponibilità come segue:
* Tolleranza di partizione: la capacità di un sistema di elaborazione dei dati di continuare l'elaborazione dei dati anche se si verifica un errore della partizione.
* Disponibilità: un nodo non di errore restituisce una risposta accettabile in un periodo ragionevole di tempo, senza errori o timeout.
* Coerenza: una lettura garantisce la restituzione della scrittura più recente per un determinato client.

## <a name="partition-tolerance"></a>Tolleranza di partizione
L'Hub eventi si basa su un modello di dati partizionato. È possibile configurare il numero di partizioni nell'hub eventi durante l'installazione, ma non è possibile modificare questo valore in un secondo momento. Poiché è obbligatorio usare le partizioni con l'Hub eventi, è necessario prendere una decisione relativa a disponibilità e coerenza dell'applicazione.

## <a name="availability"></a>Disponibilità
Il modo più semplice per iniziare a usare l'Hub eventi è il comportamento predefinito. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 o versione successiva)](#tab/latest)
Se si crea un nuovo oggetto **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** e si usa il metodo **[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** , gli eventi vengono distribuiti automaticamente tra le partizioni nell'hub eventi. Questo comportamento consente la maggiore quantità di tempo di attività.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 o versioni precedenti)](#tab/old)
Se si crea un nuovo oggetto **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** e si usa il metodo **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)**, gli eventi vengono distribuiti automaticamente tra le partizioni nell'hub eventi. Questo comportamento consente la maggiore quantità di tempo di attività.

---

Per i casi di uso che richiedono il massimo del tempo di attività, è preferibile usare questo modello.

## <a name="consistency"></a>Consistenza
In alcuni scenari, l'ordinamento degli eventi può essere importante. È ad esempio, potrebbe essere necessario che il sistema back-end elabori un comando di aggiornamento prima di un comando di eliminazione. In questo caso, è possibile impostare la chiave di partizione su un evento oppure usare un `PartitionSender` oggetto (se si usa la libreria Microsoft. Azure. Messaging precedente) per inviare solo gli eventi a una determinata partizione. In tal modo, quando questi eventi vengono letti dalla partizione, vengono letti nell'ordine. Se si usa la libreria **Azure. Messaging. EventHubs** e per altre informazioni, vedere [migrazione del codice da PartitionSender a EventHubProducerClient per la pubblicazione di eventi in una partizione](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 o versione successiva)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 o versioni precedenti)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

Con questa configurazione, tenere presente che se la partizione specifica alla quale si esegue l'invio non è disponibile, si riceverà una risposta di errore. Per fare un confronto, se non è presente un'affinità a una singola partizione, il servizio dell'Hub eventi invia l'evento alla partizione successiva disponibile.

Una possibile soluzione per garantire l'ordinamento ottimizzando allo stesso tempo i tempi di attività sarebbe l'aggregazione di eventi come parte dell'applicazione di elaborazione di eventi. Il modo più semplice per eseguire questa operazione è contrassegnare l'evento con una proprietà con numero di sequenza personalizzato. Il codice seguente visualizza un esempio:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 o versione successiva)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 o versioni precedenti)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

In questo esempio l'evento viene inviato a una delle partizioni disponibili nell'hub eventi e il numero di sequenza corrispondente viene impostato dall'applicazione. Questa soluzione richiede che l'applicazione di elaborazione mantenga lo stato, ma propone ai mittenti un endpoint che ha maggiori probabilità di essere disponibile.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica del servizio Hub eventi](./event-hubs-about.md)
* [Creare un hub eventi](event-hubs-create.md)
