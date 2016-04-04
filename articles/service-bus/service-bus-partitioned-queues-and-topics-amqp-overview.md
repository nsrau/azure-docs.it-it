<properties 
	pageTitle="Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio | Microsoft Azure" 
	description="Informazioni sull'uso del protocollo AMQP (Advanced Message Queuing Protocol) 1.0 con code e argomenti partizionati del bus di servizio." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2016" 
	ms.author="hillaryc;sethm"/>

# Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio 

Il bus di servizio di Azure ora supporta il protocollo **AMQP** (Advanced Message Queuing Protocol) 1.0 per **code e argomenti partizionati** del bus di servizio.

**AMQP** è un protocollo di accodamento messaggi a standard aperto che consente di sviluppare applicazioni multipiattaforma usando diversi linguaggi di programmazione. Per informazioni generali sul supporto AMQP nel bus di servizio, vedere [supporto per AMQP 1.0 nel Bus di servizio](service-bus-amqp-overview.md).

**Code e argomenti partizionati**, noti anche come *entità partizionate*, offrono una disponibilità, un'affidabilità e una velocità effettiva superiori rispetto alle code e agli argomenti non partizionati convenzionali. Per altre informazioni sulle entità partizionate, vedere [Partizionamento delle entità di messaggistica](service-bus-partitioning.md).

L'aggiunta di AMQP 1.0 come protocollo per la comunicazione con code e argomenti partizionati consente di creare applicazioni interoperative in grado di sfruttare la maggiore disponibilità, affidabilità e velocità effettiva garantita dalle entità partizionate del bus di servizio.

## Uso di AMQP con code partizionate

Le code sono utili per gli scenari che richiedono il disaccoppiamento temporale, il livellamento del carico, il bilanciamento del carico e l'accoppiamento di tipo loose. Con una coda, i server di pubblicazione inviano messaggi alla coda e i consumer li ricevono dalla coda in cui un messaggio può essere ricevuto solo una volta. Un esempio tipico di questo comportamento è rappresentato da un sistema di magazzino in cui i terminali del punto vendita pubblicano i dati in una coda invece che direttamente nel sistema di gestione magazzino. Quest'ultimo usa quindi i dati in qualsiasi momento per gestire il rifornimento delle scorte. Tale soluzione presenta diversi vantaggi, tra cui il fatto che il sistema di gestione magazzino non debba essere sempre online. Per altre informazioni sulle code del bus di servizio, vedere [Creazione di applicazioni che usano code del Bus di servizio](service-bus-create-queues.md).

Una coda partizionata aumenta ulteriormente la disponibilità, l'affidabilità e la velocità effettiva delle applicazioni perché vengono partizionate tra più broker dei messaggi e archivi di messaggistica diversi.

### Creazione di code partizionate

È possibile creare una coda partizionata tramite il [portale di Azure classico][] e l’SDK del bus di servizio. Per creare una coda partizionata, la proprietà [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) deve essere impostata su **true** nell'istanza di [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). Il codice seguente illustra come creare una coda partizionata utilizzando l’SDK del Bus di servizio.
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### Inviare e ricevere messaggi tramite AMQP

L'invio di messaggi a una coda partizionata e la ricezione di messaggi da una coda di questo tipo con il protocollo AMQP vengono eseguiti impostando la proprietà [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) su [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) come mostrato nel codice seguente.

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## Uso di AMQP con argomenti partizionati

Concettualmente, gli argomenti sono simili alle code, ma possono instradare una copia dello stesso messaggio a più sottoscrittori. I server di pubblicazione pertanto inviano i messaggi all'argomento e i consumer li ricevono dalle *sottoscrizioni*. Nello scenario di un sistema di magazzino, i terminali del punto vendita pubblicano i dati nell'argomento. Il sistema di gestione magazzino riceve quindi i messaggi da una sottoscrizione. Un sistema di monitoraggio inoltre può ricevere lo stesso messaggio da un'altra sottoscrizione. Per altre informazioni sugli argomenti e le sottoscrizioni del bus di servizio, vedere [Creazione di applicazioni che usano argomenti e sottoscrizioni del bus di servizio](service-bus-create-topics-subscriptions.md)

Gli argomenti partizionati aumentano ulteriormente la disponibilità, l'affidabilità e la velocità effettiva delle applicazioni perché vengono partizionati con le relative sottoscrizioni tra più broker dei messaggi e archivi di messaggistica diversi.

### Creare argomenti partizionati

È possibile creare un argomento partizionato tramite il [portale di Azure classico][] e l’SDK del bus di servizio. Per creare un argomento partizionato, la proprietà [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) deve essere impostata su **true** nell'istanza [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). Il codice seguente illustra come creare un argomento partizionato utilizzando l’SDK del Bus di servizio.
	
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### Inviare e ricevere messaggi tramite AMQP

L'invio di messaggi a una argomento partizionato e la ricezione di messaggi da una sottoscrizione di un argomento partizionato con il protocollo AMQP vengono eseguiti impostando la proprietà [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) su [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) come mostrato nel codice seguente.

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
	
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
	
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## Passaggi successivi

Vedere le seguenti informazioni aggiuntive per altre informazioni sulle entità di messaggistica partizionate.

*    [Entità di messaggistica partizionate](service-bus-partitioning.md)
*    [OASIS Advanced Message Queuing Protocol (AMQP) versione 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Supporto per il protocollo AMQP 1.0 nel bus di servizio](service-bus-amqp-overview.md)
*    [Come usare l'API JMS (Java Message Service) con il bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Come usare AMQP 1.0 con l'API .NET del bus di servizio](service-bus-dotnet-advanced-message-queuing.md)

[portale di Azure classico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0323_2016-->