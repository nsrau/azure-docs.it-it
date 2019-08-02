---
title: Guida alla programmazione - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come scrivere codice per Hub eventi di Azure tramite Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d9a1dff9c44403ad14e58b3fc3cda880cf65a29c
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679100"
---
# <a name="programming-guide-for-azure-event-hubs"></a>Guida alla programmazione per Hub eventi di Azure
Questo articolo prende in esame alcuni scenari comuni nella scrittura di codice tramite Hub eventi di Azure. Si presuppone una conoscenza preliminare di Hub eventi. Per una panoramica sui concetti relativi a Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Autori di eventi

L'invio di eventi a un hub eventi viene eseguito tramite una connessione AMQP 1.0 o HTTP POST. La scelta del protocollo da usare e di quando usarlo dipende dallo scenario specifico. Le connessioni AMQP 1.0 sono misurate come connessioni negoziate nel bus di servizio e sono più appropriate in scenari in cui sono frequenti volumi di messaggi più elevati e con requisiti di latenza inferiori, perché offrono un canale di messaggistica persistente.

Quando si usano le API gestite da .NET, i costrutti primari per la pubblicazione dei dati in Hub eventi sono le classi [EventHubClient][] e [EventData][]. [EventHubClient][] offre il canale di comunicazione AMQP tramite il quale gli eventi vengono inviati all'hub eventi. La classe [EventData][] rappresenta un evento e viene usata per pubblicare i messaggi in un hub eventi. Questa classe include il corpo, alcuni metadati (proprietà) e le informazioni di intestazione (SystemProperties) sull'evento. Altre proprietà vengono aggiunte all'oggetto [EventData][] quando passa attraverso un hub eventi.

## <a name="get-started"></a>Attività iniziali
Le classi .NET che supportano Hub eventi vengono fornite con il pacchetto NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). È possibile eseguire l'installazione tramite Esplora soluzioni di Visual Studio o la [Console di gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio. A tale scopo, eseguire il comando seguente nella finestra della [Console di gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) :

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Creare un hub eventi

Per creare hub eventi è possibile usare il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure. Per informazioni dettagliate, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il portale di Azure](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Creare un client di Hub eventi

La classe primaria per interagire con Hub eventi è [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. È possibile creare un'istanza di questa classe usando il metodo [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring), come illustrato nell'esempio seguente:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Inviare eventi a un hub eventi

Gli eventi vengono inviati a un hub eventi tramite la creazione di un'istanza di [EventHubClient][] e l'invio di quest'ultima in modo asincrono con il metodo [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync). Questo metodo accetta un singolo parametro dell'istanza di [EventData][] e lo invia in modo asincrono a un hub eventi.

## <a name="event-serialization"></a>Serializzazione degli eventi

La classe [EventData][] ha [due costruttori di overload](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) che accettano una serie di parametri, byte o una matrice di byte, che rappresentano il payload dei dati degli eventi. Quando si usa JSON con [EventData][]è possibile usare **Encoding.UTF8.GetBytes()** per recuperare la matrice di byte per una stringa con codifica JSON. Ad esempio:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Chiave di partizione

> [!NOTE]
> Se non si ha familiarità con le partizioni, vedere [questo articolo](event-hubs-features.md#partitions). 

Quando si inviano i dati degli eventi, è possibile specificare un valore di cui viene eseguito l'hashing per generare un'assegnazione di partizione. Per specificare la partizione si usa la proprietà [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid). Tuttavia, la decisione di usare le partizioni implica una scelta tra disponibilità e coerenza. 

### <a name="availability-considerations"></a>Considerazioni sulla disponibilità

L'uso di una chiave di partizione è facoltativo. È consigliabile valutare attentamente se farne uso o meno. Se non si specifica una chiave di partizione quando si pubblica un evento, viene usata un'assegnazione round robin. La chiave di partizione è un'ottima scelta se l'ordine degli eventi è importante. Quando si usa una chiave di partizione, queste partizioni richiedono la disponibilità in un singolo nodo. Nel tempo possono verificarsi interruzioni, ad esempio al riavvio dei nodi di calcolo e all'applicazione di patch. Di conseguenza, se si imposta un ID di partizione e per qualche motivo la partizione diventa non disponibile, il tentativo di accedere ai dati della partizione avrà esito negativo. Se è prioritaria la disponibilità elevata, non specificare alcuna chiave di partizione. In questo caso gli eventi vengono inviati alle partizioni con il modello round robin descritto in precedenza. In questo scenario viene esercitata una scelta esplicita tra disponibilità (nessun ID di partizione) e coerenza (aggiunta di eventi a un ID di partizione).

Va anche considerata la gestione dei ritardi nell'elaborazione degli eventi. In alcuni casi è preferibile eliminare i dati e riprovare anziché continuare a provare e mantenere attiva l'elaborazione, poiché ciò può sommare altro ritardo all'elaborazione downstream. Con i titoli azionari, ad esempio, è consigliabile attendere il completamento dell'aggiornamento dei dati, ma nel caso di una chat in tempo reale o in uno scenario VOIP è preferibile ottenere i dati rapidamente, anche se non sono completi.

Considerati gli aspetti relativi alla disponibilità, in questi scenari è possibile scegliere una tra le strategie di gestione degli errori seguenti:

- Arresto (arresto della lettura da Hub eventi fino alla risoluzione del problema)
- Eliminazione (i messaggi non sono importanti, eliminarli)
- Ripetizione (nuovo tentativo di invio dei messaggi quando possibile)

Per altre informazioni e una discussione sui compromessi tra disponibilità e coerenza, vedere [Availability and consistency in Event Hubs (Disponibilità e coerenza in Hub eventi)](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Operazioni di invio di eventi in batch

L'invio di eventi in batch può aumentare la velocità effettiva. È possibile usare l'API [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) per creare un batch a cui è possibile aggiungere oggetti dati in un secondo momento per una chiamata [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync).

Si noti che un singolo batch non deve superare il limite di 1 MB di un evento. Inoltre, ogni messaggio nel batch usa la stessa identità del publisher. È responsabilità del mittente verificare che il batch non superi la dimensione massima dell'evento. In questo caso, viene generato un errore di **invio** del client. È possibile usare il metodo helper [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) per assicurarsi che il batch non superi 1 MB. Si ottiene un [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) vuoto dall'API [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch), quindi si usa [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) per aggiungere eventi per costruire il batch. 

## <a name="send-asynchronously-and-send-at-scale"></a>Inviare in modo asincrono e inviare a livello di scalabilità

È possibile inviare eventi a un hub eventi in modo asincrono. L'invio asincrono può consentire di aumentare la velocità con cui un client può inviare gli eventi. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) restituisce un oggetto [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). È possibile usare la classe [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) nel client per controllare le opzioni di ripetizione dei tentativi del client.

## <a name="event-consumers"></a>Consumer di eventi
La classe [EventProcessorHost][] elabora i dati da Hub eventi. È consigliabile usare questa implementazione durante la creazione di reader di eventi sulla piattaforma .NET. [EventProcessorHost][] fornisce un ambiente di runtime thread-safe, multiprocesso e sicuro per le implementazioni del processore di eventi che fornisce inoltre la gestione di lease di Checkpoint e la partizione.

Per usare la classe [EventProcessorHost][], è possibile implementare [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Questa interfaccia contiene quattro metodi:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Per avviare l'elaborazione di eventi, creare un'istanza [EventProcessorHost][]fornendo i parametri appropriati per l'hub eventi. Ad esempio:

> [!NOTE]
> EventProcessorHost e le classi correlate sono disponibili nel pacchetto **Microsoft. Azure. EventHubs. Processor** . Aggiungere il pacchetto al progetto di Visual Studio seguendo le istruzioni riportate in [questo articolo](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) oppure eseguendo il comando seguente nella finestra della [console di gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) :`Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Chiamare quindi [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) per registrare l'implementazione di [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) con il runtime:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

A questo punto, l'host tenta di acquisire un lease per ogni partizione nell'hub eventi tramite un algoritmo "greedy". Tali lease durano per un determinato intervallo di tempo e quindi devono essere rinnovati. Appena nuovi nodi, in questo caso istanze di lavoro, passano online, inviano prenotazioni di lease e nel tempo il carico passa tra i nodi man mano che ognuno tenta di acquisire più lease.

![Host processore di eventi](./media/event-hubs-programming-guide/IC759863.png)

Con il passare del tempo, viene stabilito un equilibrio. Questa funzionalità dinamica consente il ridimensionamento automatico basato su CPU da applicare ai consumer per la scalabilità verticale e orizzontale. Poiché Hub eventi non ha una conoscenza diretta del numero di messaggi, l'uso medio della CPU è spesso il meccanismo migliore per misurare la scala back-end o di consumer. Se i publisher iniziano a pubblicare più eventi di quelli che i consumer possono elaborare, l'aumento della CPU nei consumer può essere usato per applicare la scalabilità automatica sul numero di istanze di lavoro.

La classe [EventProcessorHost][] implementa inoltre un meccanismo di impostazione di checkpoint basato sull'archiviazione di Azure. Questo meccanismo archivia l'offset per ogni partizione, in modo che ogni consumer possa determinare qual è stato l'ultimo checkpoint per il consumer precedente. Man mano che le partizioni passano tra i nodi tramite lease, questo è il meccanismo di sincronizzazione che semplifica lo spostamento del carico.

## <a name="publisher-revocation"></a>Revoca di publisher

Oltre alle funzionalità di runtime avanzate di [EventProcessorHost][], Hub eventi consente anche di revocare i publisher per impedire a publisher specifici di inviare eventi a un hub eventi. Queste funzionalità sono utili in situazioni in cui il token di un autore è stato compromesso o un aggiornamento software sta causando un comportamento non appropriato. In queste situazioni, l'identità dell'autore, che fa parte del relativo token di firma di accesso condiviso, può essere bloccata impedendo la pubblicazione di eventi.

Per altre informazioni sulla revoca di publisher e su come eseguire l'invio a Hub eventi come publisher, vedere l'esempio relativo alla [pubblicazione sicura su larga scala in Hub eventi](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli scenari di Hub eventi, visitare i collegamenti seguenti:

* [Panoramica dell'API di Hub eventi](event-hubs-api-overview.md)
* [Che cos'è Hub eventi?](event-hubs-what-is-event-hubs.md)
* [Disponibilità e coerenza nell'Hub eventi](event-hubs-availability-and-consistency.md)
* [Riferimento all'API dell'host processore di eventi](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
