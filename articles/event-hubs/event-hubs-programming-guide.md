<properties 
    pageTitle="Guida alla programmazione per Hub eventi di Azure | Microsoft Azure"
    description="Descrive la programmazione con Hub eventi di Azure mediante Azure .NET SDK."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sethm" />  

# Guida alla programmazione di Hub eventi

Questo argomento descrive la programmazione con Hub eventi di Azure mediante Azure .NET SDK. Si presuppone una conoscenza preliminare di Hub eventi. Per una panoramica sui concetti relativi a Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-overview.md).

## Publisher di eventi

L'invio di eventi a un hub eventi viene eseguito tramite una connessione AMQP 1.0 o HTTP POST. La scelta del protocollo da usare dipende dallo scenario specifico. Le connessioni AMQP 1.0 sono misurate come connessioni negoziate nel bus di servizio e sono più appropriate in scenari in cui sono frequenti volumi di messaggi più elevati e con requisiti di latenza inferiori, perché offrono un canale di messaggistica persistente.

Si crea e si gestisce Hub eventi con la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Quando si usano le API gestite da .NET, i costrutti primari per la pubblicazione dei dati in Hub eventi sono le classi [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) e [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) offre il canale di comunicazione AMQP tramite il quale gli eventi vengono inviati all'hub eventi. La classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) rappresenta un evento e viene usata per pubblicare i messaggi in un Hub eventi. Questa classe include il corpo, alcuni metadati e informazioni di intestazione sull'evento. Altre proprietà vengono aggiunte all'oggetto [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) quando passa attraverso un hub eventi.

## Introduzione

Le classi .NET che supportano Hub eventi fanno parte dell'assembly Microsoft.ServiceBus.dll. Il modo più semplice per fare riferimento all'API del bus di servizio e configurare l'applicazione con tutte le dipendenze del bus di servizio consiste nello scaricare il [pacchetto NuGet del bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus). In alternativa, è possibile usare [Console di gestione pacchetti](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio. A tale scopo, eseguire il comando seguente nella finestra della [Console di gestione pacchetti](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```
Install-Package WindowsAzure.ServiceBus
```

## Creare un hub eventi

È possibile utilizzare la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) per creare gli hub di eventi. ad esempio:

```
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

Nella maggior parte dei casi, è consigliabile usare i metodi [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) per evitare di generare eccezioni se il servizio viene riavviato. ad esempio:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Tutte le operazioni di creazione di Hub eventi, tra cui [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx), richiedono di **gestire** le autorizzazioni per lo spazio dei nomi in questione. Se si desidera limitare le autorizzazioni delle applicazioni publisher o consumer, è possibile evitare queste chiamate alle operazioni di creazione nel codice di produzione quando si usano credenziali con autorizzazioni limitate.

La classe [EventHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubdescription.aspx) contiene informazioni dettagliate su un hub eventi, tra cui le regole di autorizzazione, l'intervallo di conservazione dei messaggi, l'ID di partizione, lo stato e il percorso. È possibile usare questa classe per aggiornare i metadati in un hub eventi.

## Creare un client di Hub eventi

La classe primaria per l'interazione con Hub eventi è [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx). Questa classe fornisce funzionalità di mittente e destinatario. È possibile creare un'istanza di questa classe usando il metodo [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx), come illustrato nell'esempio seguente.

```
var client = EventHubClient.Create(description.Path);
```

Questo metodo usa le informazioni di connessione del bus di servizio nella sezione `appSettings` del file App.config. Per un esempio dell'XML di `appSettings` usato per archiviare le informazioni di connessione del bus di servizio, vedere la documentazione per il metodo [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx).

È anche possibile creare il client da una stringa di connessione. Questa opzione funziona bene quando si usano i ruoli di lavoro di Azure, poiché è possibile archiviare la stringa nelle proprietà di configurazione per il lavoro. ad esempio:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

La stringa di connessione avrà un formato uguale a quello presente nel file App.config per i metodi precedenti:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Infine, è inoltre possibile creare un oggetto [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) da un'istanza di [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx), come illustrato nell'esempio seguente.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

È importante notare che oggetti [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) aggiuntivi creati da un'istanza di factory di messaggistica riutilizzeranno la stessa connessione TCP sottostante. Di conseguenza, questi oggetti prevedono un limite sul lato client per la velocità effettiva. Il metodo [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) riutilizza una singola factory di messaggistica. Se è necessario molto elevata velocità effettiva di un singolo mittente, è possibile creare più factory di messaggistica e un oggetto [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) factory di messaggistica.

## Inviare eventi a un Hub eventi

Gli eventi vengono inviati a un Hub eventi tramite la creazione di un'istanza [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e l'invio di quest'ultima con il metodo [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx). Questo metodo accetta un singolo parametro dell'istanza di [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e lo invia in modo sincrono a un hub eventi.

## Serializzazione degli eventi

La classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) dispone di [quattro costruttori di overload](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.eventdata.aspx) che accettano una serie di parametri, ad esempio un oggetto, un serializzatore, una matrice di byte o un flusso. È inoltre possibile creare un'istanza della classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e impostare il flusso del corpo in un secondo momento. Quando si usa JSON con [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) è possibile usare **Encoding.UTF8.GetBytes()** per recuperare la matrice di byte per una stringa con codifica JSON.

## Chiave di partizione

La classe [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) dispone di una proprietà [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) che consente al mittente di specificare un valore che viene eseguito con hashing per produrre un'assegnazione di partizione. L'uso di una chiave di partizione assicura che tutti gli eventi con la stessa chiave vengano inviati alla stessa partizione nell'hub eventi. Le chiavi di partizione comuni includono ID sessione utente e ID mittente univoci. La proprietà [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) è facoltativa e può essere fornito quando si utilizza il metodo [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx)o[Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx). Se non si specifica un valore per [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx), gli eventi inviati vengono distribuiti alle partizioni tramite un modello round robin.

## Operazioni di invio di eventi in batch

L'invio di eventi in batch può aumentare la velocità effettiva. Il metodo [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) accetta un parametro **IEnumerable** di tipo[EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) e invia l'intero batch come operazione atomica all'Hub di eventi.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Si noti che un singolo batch non deve superare il limite di 256 KB di un evento. Inoltre, ogni messaggio nel batch usa la stessa identità del publisher. È responsabilità del mittente verificare che il batch non superi la dimensione massima dell'evento. In questo caso, viene generato un errore di **invio** del client.

## Inviare in modo asincrono e inviare a livello di scalabilità

È anche possibile inviare eventi a un hub eventi in modo asincrono. L'invio asincrono può consentire di aumentare la velocità con cui un client può inviare gli eventi. Entrambi i metodi [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) e [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) sono disponibili nelle versioni asincrone che restituiscono un oggetto [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Sebbene questa tecnica possa consentire di aumentare la velocità effettiva, può anche provocare l'invio continuo di eventi da parte del client anche in presenza di limitazioni imposte dal servizio Hub eventi e ciò può comportare errori del client o perdita di messaggi, se l'implementazione non avviene correttamente. Inoltre, è possibile usare la proprietà [RetryPolicy](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) nel client per controllare le opzioni di ripetizione dei tentativi nel client.

## Creare un mittente di partizione

Sebbene sia più comune inviare eventi a un hub eventi con una chiave di partizione, in alcuni casi è possibile inviare gli eventi direttamente a una partizione specifica. ad esempio:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) restituisce un oggetto [EventHubSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubsender.aspx) che è possibile usare per pubblicare eventi in una partizione specifica dell'hub eventi.

## Consumer di eventi

Hub eventi dispone di due modelli principali per l'utilizzo di eventi: ricevitori diretti e astrazioni di livello superiore, ad esempio [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx). I ricevitori diretti sono responsabili del coordinamento del proprio accesso alle partizioni in un gruppo di consumer.

### Consumer diretto

Il modo più diretto per leggere da una partizione all'interno di un gruppo di consumer consiste nell'uso della classe [EventHubReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.aspx). Per creare un'istanza di questa classe, è necessario usare un'istanza della classe [EventHubConsumerGroup](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.aspx). Nell'esempio seguente, è necessario specificare l'ID di partizione quando si crea il ricevitore per il gruppo di consumer.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

Il metodo [CreateReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) dispone di diversi overload che facilitano il controllo sul lettore creato. Questi metodi includono la specifica di un offset sotto forma di stringa o timestamp e la possibilità di specificare se includere questo offset nel flusso restituito o iniziare dopo di esso. Dopo aver creato il ricevitore, è possibile iniziare a ricevere gli eventi nell'oggetto restituito. Il metodo [Receive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) dispone di quattro overload che controllano i parametri dell'operazione receive, ad esempio le dimensioni di batch e il tempo di attesa. È possibile usare le versioni asincrone di questi metodi per aumentare la velocità effettiva di un consumer. ad esempio:

```
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

Per una partizione specifica, i messaggi vengono ricevuti nell'ordine in cui sono stati inviati all'hub eventi. L'offset è un token di stringa usato per identificare un messaggio in una partizione.

Si noti che una singola partizione all'interno di un gruppo di consumer non può avere più di cinque lettori concorrenti connessi in un determinato momento. Quando i reader si connettono o si disconnettono, le relative sessioni potrebbero rimanere attive per alcuni minuti prima che il servizio riconosca che si sono disconnessi. In questo intervallo, la riconnessione a una partizione potrebbe non riuscire. Per un esempio completo di scrittura di un ricevitore diretto per Hub eventi, vedere l'esempio relativo ai [ricevitori diretti per gli hub eventi del bus di servizio](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### Host processore di eventi

La classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) elabora i dati da Hub eventi. È consigliabile usare questa implementazione durante la creazione di reader di eventi sulla piattaforma .NET. [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) fornisce un ambiente di runtime thread-safe, multiprocesso e sicuro per le implementazioni del processore di eventi che fornisce inoltre la gestione di lease di Checkpoint e la partizione.

Per usare la classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), è possibile implementare [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx). Questa interfaccia contiene tre metodi:

- [OpenAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Per avviare l'elaborazione di eventi, creare un'istanza [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) fornendo i parametri appropriati per l'Hub eventi. Chiamare quindi [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) per registrare l'implementazione di [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) con il runtime. A questo punto, l'host tenta di acquisire un lease per ogni partizione nell'hub eventi mediante un algoritmo "greedy". Tali lease dureranno per un determinato intervallo di tempo e quindi devono essere rinnovati. Appena nuovi nodi, in questo caso istanze di lavoro, passano online, inviano prenotazioni di lease e nel tempo il carico passa tra i nodi man mano che ognuno tenta di acquisire più lease.

![Host processore di eventi](./media/event-hubs-programming-guide/IC759863.png)

Con il passare del tempo, viene stabilito un equilibrio. Questa funzionalità dinamica consente il ridimensionamento automatico basato su CPU da applicare ai consumer per la scalabilità verticale e orizzontale. Poiché gli hub eventi non hanno una conoscenza diretta del numero di messaggi, l'utilizzo medio della CPU è spesso il meccanismo migliore per misurare la scala back-end o di consumer. Se i publisher iniziano a pubblicare più eventi di quelli che i consumer possono elaborare, l'aumento della CPU nei consumer può essere usato per applicare la scalabilità automatica sul numero di istanze di lavoro.

La classe [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) implementa inoltre un meccanismo di impostazione di checkpoint basato sull'archiviazione di Azure. Questo meccanismo archivia l'offset per ogni partizione, in modo che ogni consumer possa determinare qual è stato l'ultimo checkpoint per il consumer precedente. Man mano che le partizioni passano tra i nodi tramite lease, questo è il meccanismo di sincronizzazione che semplifica lo spostamento del carico.

## Revoca di publisher

Oltre alle funzionalità di runtime avanzate di [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), Hub eventi consente anche di revocare i publisher per impedire a publisher specifici di inviare eventi a un Hub eventi. Queste funzionalità sono particolarmente utili in situazioni in cui il token di un autore è stato compromesso o un aggiornamento software sta causando un comportamento non appropriato. In queste situazioni, l'identità dell'autore, che fa parte del relativo token di firma di accesso condiviso, può essere bloccata impedendo la pubblicazione di eventi.

Per altre informazioni sulla revoca di publisher e su come eseguire l'invio a Hub eventi come publisher, vedere l'esempio relativo alla [pubblicazione sicura su larga scala in Hub eventi del bus di servizio](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## Passaggi successivi

Per altre informazioni sugli scenari di Hub eventi, visitare i collegamenti seguenti:

- [Panoramica dell'API di Hub eventi](event-hubs-api-overview.md)
- [Panoramica di Hub eventi](event-hubs-overview.md)
- [Esempi di codice di Hub eventi] (http://code.msdn.microsoft.com/site/search?query=eventhub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)
- [Riferimento all'API dell'host processore di eventi](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx)

<!---HONumber=AcomDC_0817_2016-->