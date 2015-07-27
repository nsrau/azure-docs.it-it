<properties 
   pageTitle="Guida alla programmazione di Hub eventi"
   description="Viene descritta la programmazione con Hub eventi di Azure utilizzando Azure .NET SDK."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/10/2015"
   ms.author="sethm" />

# Guida alla programmazione di Hub eventi

In questo argomento viene descritta la programmazione con Hub eventi di Azure utilizzando Azure .NET SDK. Si presuppone una conoscenza preliminare di Hub eventi. Per concettuale una panoramica di Hub eventi, vedere le [Informazioni generali su Hub eventi](event-hubs-overview.md).

## Eventi di pubblicazione: gli autori di eventi

L'invio di eventi a Hub eventi viene eseguita utilizzando HTTP POST o tramite una connessione di AMQP 1.0. La scelta di cui si desidera utilizzare quando dipende dallo scenario specifico indirizzato. Le connessioni AMQP 1.0 sono misurate come connessioni negoziate nel Bus di servizio e sono più appropriate in scenari con requisiti di latenza inferiori offrendo un canale di messaggistica persistente e volumi più elevati di messaggio.

Gli hub di eventi vengono creati e gestiti mediante la classe [NamespaceManager](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). Quando si utilizza API gestito da .NET, i costrutti primari per la pubblicazione di dati per Hub eventi sono classi [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) e [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx). La classe [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) fornisce il canale di comunicazione AMQP su cui gli eventi vengono inviati all'Hub eventi. La classe [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) rappresenta un evento e viene utilizzato per pubblicare i messaggi a un Hub di eventi. Questa classe include il corpo, alcuni metadati e informazioni di intestazione sull'evento. Altre proprietà vengono aggiunte per l’oggetto [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) quando passa attraverso un Hub di eventi.

## Introduzione

Le classi .NET che supportano gli hub di eventi fanno parte dell'assembly Microsoft.ServiceBus.dll. Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. Per ulteriori informazioni, vedere [utilizzare il pacchetto NuGet del Bus di servizio](https://msdn.microsoft.com/library/dn741354.aspx). In alternativa, è possibile utilizzare [Console di gestione pacchetti](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio. Per fare questo, inserire il comando seguente nella finestra della [Console di gestione pacchetti](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```powershell
Install-Package WindowsAzure.ServiceBus
```

## Creare un hub eventi

È possibile utilizzare la classe [NamespaceManager](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx) per creare gli hub di eventi. Ad esempio:

```c
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

Nella maggior parte dei casi, è consigliabile utilizzare i metodi [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) per evitare di generare eccezioni se il servizio viene riavviato. Ad esempio:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Tutte le operazioni di creazione di Hub di eventi, tra cui [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) richiedono **Gestisci** le autorizzazioni per lo spazio dei nomi in questione. Se si desidera limitare le autorizzazioni delle applicazioni server di pubblicazione o consumer, è possibile evitare questi creare chiamate di operazioni nel codice di produzione quando si utilizzano le credenziali con autorizzazioni limitate.

La classe [EventHubDescription](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubdescription.aspx) contiene informazioni dettagliate su un Hub, tra cui le regole di autorizzazione, il periodo di memorizzazione del messaggio, ID di partizione, stato e percorso. È possibile utilizzare questa classe per aggiornare i metadati su un Hub di eventi.

## Creare un input dell'hub eventi

La classe primaria per l'interazione con Hub eventi è [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx). Questa classe fornisce funzionalità di mittente e destinatario. È possibile creare un'istanza di questa classe utilizzando il metodo [Crea](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx), come illustrato nell'esempio seguente.

```
var client = EventHubClient.Create(description.Path);
```

Questo metodo utilizza le informazioni di connessione del Bus di servizio nel file app. config nella sezione `appSettings`. Per un esempio del `appSettings` XML utilizzato per archiviare le informazioni di connessione del Bus di servizio, vedere la documentazione per il metodo [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx).

Un'altra opzione consiste nel creare il client da una stringa di connessione. Questa opzione funziona anche quando si utilizza ruoli di lavoro di Azure, poiché è possibile archiviare la stringa nelle proprietà di configurazione per il processo di lavoro. Ad esempio:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

La stringa di connessione sarà nello stesso formato visualizzato nel file app. config per i metodi precedenti:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Infine, è inoltre possibile creare un oggetto [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) da un[MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)dell'istanza, come illustrato nell'esempio seguente.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

È importante notare che gli oggetti supplementari [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) creati da un'istanza di messaggistica factory riutilizzano la stessa connessione TCP sottostante. Di conseguenza, questi oggetti hanno un limite sul lato client sulla velocità effettiva. Il metodo [Crea](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx) riutilizza un singolo factory di messaggistica. Se è necessario molto elevata velocità effettiva di un singolo mittente, è possibile creare più factory di messaggistica e un oggetto [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) factory di messaggistica.

## Inviare gli eventi a un Hub di eventi

Gli eventi vengono inviati a un Hub di eventi tramite la creazione di una istanza [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) e inviati tramite il metodo [Invio](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.send.aspx). Questo metodo accetta un singolo parametro di istanza [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) e invia in modo sincrono a un Hub di eventi.

## Serializzazione di evento

La classe [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) dispone di [quattro costruttori di overload](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) che accettano una serie di parametri, ad esempio un oggetto e serializzatore, una matrice di byte o un flusso. È inoltre possibile creare un'istanza della classe [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) e impostare il flusso del corpo in un secondo momento. Quando si utilizza JSON con [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) è possibile utilizzare **Encoding.UTF8.GetBytes()** per recuperare la matrice di byte per una stringa con codifica JSON.

## Chiave di partizione

La classe [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) dispone di una proprietà [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) che consente al mittente di specificare un valore che viene eseguito con hashing per produrre un'assegnazione di partizione. Utilizzare una chiave di partizione assicura che tutti gli eventi con la stessa chiave vengano inviati alla stessa partizione nell'evento Hub. Chiavi di partizione comuni includono gli ID di sessione utente e ID mittente unico. La proprietà [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) è facoltativa e può essere fornito quando si utilizza il metodo [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx)o[Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx). Se non si specifica un valore per [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) gli eventi inviati vengono distribuiti alle partizioni tramite un modello round robin.

## Operazioni di invio evento batch

L'invio di eventi in batch può aumentare la velocità effettiva. Il metodo [SendBatch](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) accetta un parametro **IEnumerable** di tipo[EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) e invia l'intero batch come operazione atomica all'Hub di eventi.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

È importante notare che un singolo batch non deve superare il limite di 256 KB di un evento. Inoltre, ogni messaggio nel batch utilizza la stessa identità del server di pubblicazione. È responsabilità del mittente verificare che il batch non superi la dimensione massima dell'evento. In questo caso, un errore client **Invio** viene generato .

## Inviare in modo asincrono e inviare a livello di scalabilità

È inoltre possibile inviare eventi a un Hub di eventi in modo asincrono. Inviando in modo asincrono, è possibile aumentare la frequenza con cui un client è in grado di inviare eventi. Entrambi i metodi [inviare](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.send.aspx) e [SendBatch](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) sono disponibili nelle versioni asincrone che restituiscono un oggetto [attività](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Mentre questa tecnica può aumentare la velocità effettiva, è possibile che il client continui a inviare eventi anche quando è limitato dal servizio Hub eventi e può comportare errori per il client o messaggi persi se non implementato correttamente. Inoltre, è possibile utilizzare la proprietà [RetryPolicy](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) sul client per le opzioni di tentativi di controllo client.

## Creare un mittente di partizione

Sebbene sia più comune per inviare eventi a un Hub di eventi con una chiave di partizione, in alcuni casi è possibile inviare eventi direttamente a una determinata partizione. Ad esempio:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) restituisce un oggetto [EventHubSender](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubsender.aspx) che è possibile utilizzare per la pubblicazione di eventi in una partizione di Hub di eventi specifica.

## Utilizzare gli eventi: consumer di eventi

Hub eventi dispone di due modelli principali per l'utilizzo di eventi: indirizzare ricevitori e astrazioni di livello superiore, ad esempio [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx). Ricevitori diretti sono responsabili per il proprio coordinamento di accesso alle partizioni all'interno di un gruppo di consumer.

### Consumer diretto

Il modo più diretto per leggere da una partizione all'interno di un gruppo di consumer consiste nell'utilizzare la classe [EventHubReceiver](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubreceiver.aspx). Per creare un'istanza di questa classe, è necessario utilizzare un'istanza della classe [EventHubConsumerGroup](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubconsumergroup.aspx). Nell'esempio di codice riportato di seguito, è necessario specificare l'ID di partizione quando si crea il ricevitore per il gruppo di consumer.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

Il metodo [CreateReceiver](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) dispone di diversi overload che facilitano il controllo sul lettore creato. Questi metodi includono la specifica di un offset come una stringa o il timestamp e la possibilità di specificare se includere questo specifico offset nel flusso restituito o avviarlo dopo di esso. Dopo aver creato il ricevitore, è possibile avviare la ricezione di eventi sull'oggetto restituito. Il metodo [ricezione](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) dispone di quattro overload che controllano i parametri dell'operazione receive, ad esempio le dimensioni di batch e tempo di attesa. È possibile utilizzare le versioni asincrone di questi metodi per aumentare la velocità effettiva di un consumer. Ad esempio:

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

Per una partizione specifica, i messaggi vengono ricevuti nell'ordine in cui sono stati inviati all'Hub di eventi. L'offset è un token di stringa utilizzato per identificare un messaggio in una partizione.

È importante notare che una singola partizione all'interno di un gruppo di consumer non può avere più di 5 utenti concorrenti connessi in qualsiasi momento. Quando i lettori di connettono o disconnettono, le sessioni potrebbero restare attive per alcuni minuti prima che il servizio riconosca che sono disconnessi. Durante questo periodo, la riconnessione a una partizione può non riuscire. Per un esempio completo di scrittura di un ricevitore diretto per Hub eventi, vedere l’esempio [Ricevitori diretti per hub di eventi di bus di servizio](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### Host processore di eventi

La classe [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) elabora i dati da Hub eventi. Durante la creazione di lettori eventi sulla piattaforma .NET, è consigliabile utilizzare questa implementazione. [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) fornisce un ambiente di runtime thread-safe, multiprocesso e sicuro per le implementazioni del processore di eventi che fornisce inoltre la gestione di lease di Checkpoint e la partizione.

Utilizzando la classe [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx), è possibile implementare[IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx). Questa interfaccia contiene tre metodi:

- [OpenAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Per avviare l'elaborazione di eventi, creare un'istanza [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) fornendo i parametri appropriati per l'Hub di eventi. Richiamare quindi [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) per registrare l’implementazione [IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx) con il runtime. A questo punto, l'host tenta di acquisire un lease per ogni partizione nell'evento Hub mediante un algoritmo "greedy". Tali lease dureranno per un determinato intervallo di tempo e quindi devono essere rinnovati. Quando nuovi nodi, istanze di lavoro in questo caso, arrivano in linea, posizionano le prenotazioni di lease e col tempo gli spostamenti del carico tra i nodi per ogni tentativo di acquisire ulteriori lease.

![Host processore di eventi](./media/event-hubs-programming-guide/IC759863.png)

Nel corso del tempo, viene stabilito un equilibrio. Questa funzionalità dinamica consente il ridimensionamento automatico basato su CPU da applicare ai consumer per la scalabilità verticale e scalabilità verticale. Poiché Hub eventi non è un concetto diretto del numero di messaggi, utilizzo medio della CPU è spesso il meccanismo più adatto per misurare la scala back end o consumer. Se il server di pubblicazione inizia a pubblicare più eventi che i consumer sono in grado di elaborare, l'aumento della CPU sui consumatori consente di impedire un ridimensionamento automatico in un numero di istanze di lavoro.

La classe [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) implementa inoltre un meccanismo di checkpoint basati sull'archiviazione Azure. Questo meccanismo archivia l'offset in base alla partizione, in modo che ogni consumer può determinare qual è l'ultimo checkpoint dal consumer precedente. Quando le partizioni passano tra i nodi tramite lease, questo è il meccanismo di sincronizzazione che facilita lo spostamento di carico.

## Revoca di server di pubblicazione

Oltre alle funzionalità avanzate del runtime [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) gli hub di eventi consentono di revocare di certificati server di pubblicazione per bloccare editori specifici dall'invio di eventi a un Hub di eventi. Queste funzionalità sono particolarmente utili se il token del server di pubblicazione sia stata compromessa o un aggiornamento software è la causa si comportino in modo inappropriato. In queste situazioni, l'identità dell'autore, che fa parte del loro token SAS, può essere bloccato da eventi di pubblicazione.

Per ulteriori informazioni sulla revoca di pubblicazione e su come inviare a Hub eventi in qualità di server di pubblicazione, vedere l’esempio [Pubblicazione su grande scalabilità protetta su Hub eventi di bus di servizio](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## Passaggi successivi

Per ulteriori informazioni sugli scenari di Hub eventi, visitare i collegamenti seguenti:

- [Panoramica dell’API Hub eventi](event-hubs-api-overview.md)
- [Panoramica di Hub eventi](event-hubs-overview.md)
- [Esempi di codice di Hub eventi] (http://code.msdn.microsoft.com/site/search?query=eventhub & f [0]. Valore = hub eventi & f [0]. Digitare = SearchText & CA = 5)
- [Host del processore riferimento all'API di eventi](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)

<!---HONumber=July15_HO3-->