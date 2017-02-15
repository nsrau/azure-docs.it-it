---
title: Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio | Microsoft Docs
description: Informazioni sull&quot;uso del protocollo AMQP (Advanced Message Queuing Protocol) 1.0 con code e argomenti partizionati del bus di servizio.
services: service-bus-messaging
documentationcenter: .net
author: hillaryc
manager: timlt
editor: 
ms.assetid: e2549ad3-41eb-47e5-b25f-20043a7ffa2a
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/14/2016
ms.author: hillaryc;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2f343c1f087b516c35eb75fc9a1e61c5cf6d1e93


---
# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio
Il bus di servizio di Azure ora supporta il protocollo **AMQP** (Advanced Message Queuing Protocol) 1.0 per **code e argomenti partizionati** del bus di servizio.

**AMQP** è un protocollo di accodamento messaggi a standard aperto che consente di sviluppare applicazioni multipiattaforma usando diversi linguaggi di programmazione. Per informazioni generali sul supporto AMQP nel bus di servizio, vedere [Supporto per il protocollo AMQP 1.0 nel bus di servizio](service-bus-amqp-overview.md).

**Code e argomenti partizionati**, noti anche come *entità partizionate*, offrono disponibilità, affidabilità e velocità effettiva superiori rispetto alle code e agli argomenti non partizionati convenzionali. Per altre informazioni sulle entità partizionate, vedere le [entità di messaggistica partizionate](service-bus-partitioning.md).

L'aggiunta di AMQP 1.0 come protocollo per la comunicazione con code e argomenti partizionati consente di creare applicazioni interoperative che riescono a sfruttare la maggiore disponibilità, affidabilità e velocità effettiva garantita dalle entità partizionate del bus di servizio.

Per una guida dettagliata al protocollo wire-level AMQP 1.0, che illustra come il bus di servizio è basato sulla specifica tecnica OASIS AMQP e la implementa, vedere [Guida al protocollo AMQP 1.0 nel bus di servizio e in Hub eventi di Azure](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Uso di AMQP con code partizionate
Le code sono utili per gli scenari che richiedono il disaccoppiamento temporale, il livellamento del carico, il bilanciamento del carico e l'accoppiamento di tipo loose. Con una coda, i server di pubblicazione inviano messaggi alla coda e i consumer li ricevono dalla coda in cui un messaggio può essere ricevuto solo una volta. Un esempio tipico di questo comportamento è rappresentato da un sistema di magazzino in cui i terminali del punto vendita pubblicano i dati in una coda invece che direttamente nel sistema di gestione magazzino. Quest'ultimo usa quindi i dati in qualsiasi momento per gestire il rifornimento delle scorte. Tale soluzione presenta diversi vantaggi, tra cui il fatto che il sistema di gestione magazzino non debba essere sempre online. Per altre informazioni sulle code del bus di servizio, vedere [Creare applicazioni che usano le code del bus di servizio](service-bus-create-queues.md). 

Una coda partizionata aumenta ulteriormente la disponibilità, l'affidabilità e la velocità effettiva delle applicazioni perché vengono partizionate tra più broker dei messaggi e archivi di messaggistica diversi.     

### <a name="create-partitioned-queues"></a>Creazione di code partizionate
È possibile creare una coda partizionata tramite il [portale di Azure classico][portale di Azure classico] e Service Bus SDK. Per creare una coda partizionata, la proprietà [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) deve essere impostata su **true** nell'istanza di [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). Il codice seguente illustra come creare una coda partizionata utilizzando l’SDK del Bus di servizio. 

```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Inviare e ricevere messaggi tramite AMQP
L'invio di messaggi a una coda partizionata e la ricezione di messaggi da una coda di questo tipo con il protocollo AMQP vengono eseguiti impostando la proprietà [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) su [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) come specificato nel codice seguente.  

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

## <a name="use-amqp-with-partitioned-topics"></a>Usare AMQP con argomenti partizionati
Concettualmente, gli argomenti sono simili alle code, ma possono instradare una copia dello stesso messaggio a più *sottoscrizioni*. Le entità di pubblicazione inviano i messaggi all'argomento e i consumer li ricevono dalle sottoscrizioni. Nello scenario di un sistema di magazzino i terminali del punto vendita pubblicano i dati nell'argomento. Il sistema di gestione magazzino riceve quindi i messaggi da una sottoscrizione. Un sistema di monitoraggio può anche ricevere lo stesso messaggio da un'altra sottoscrizione. Per altri dettagli sugli argomenti e le sottoscrizioni del bus di servizio, vedere [Creare applicazioni che usano argomenti e sottoscrizioni del bus di servizio](service-bus-create-topics-subscriptions.md). 

Così come le code, gli argomenti partizionati aumentano ulteriormente la disponibilità, l'affidabilità e la velocità effettiva delle applicazioni perché vengono partizionati con le relative sottoscrizioni tra più broker dei messaggi e archivi di messaggistica diversi. 

### <a name="create-partitioned-topics"></a>Creare argomenti partizionati
È possibile creare un argomento partizionato tramite il [portale di Azure classico][portale di Azure classico] e Service Bus SDK. Per creare un argomento partizionato, la proprietà [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) deve essere impostata su **true** nell'istanza di [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). Il codice seguente illustra come creare un argomento partizionato usando Service Bus SDK.

```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Inviare e ricevere messaggi tramite AMQP
È possibile ricevere e inviare messaggi da e verso una sottoscrizione di un argomento partizionato con il protocollo AMQP impostando la proprietà [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) su [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) come illustrato nel codice seguente.  

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

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle entità di messaggistica partizionate e AMQP, vedere le informazioni aggiuntive seguenti.

* [Entità di messaggistica partizionate](service-bus-partitioning.md)
* [OASIS Advanced Message Queuing Protocol (AMQP) Version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) (ASIS Advanced Message Queuing Protocol (AMQP) versione 1.0)
* [Supporto per il protocollo AMQP 1.0 nel bus di servizio](service-bus-amqp-overview.md)
* [Guida al protocollo AMQP 1.0 nel bus di servizio e in Hub eventi di Azure](service-bus-amqp-protocol-guide.md)
* [Come usare l'API JMS (Java Message Service) con il bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Come usare AMQP 1.0 con l'API .NET del bus di servizio](service-bus-dotnet-advanced-message-queuing.md)

[portale di Azure classico]: http://manage.windowsazure.com



<!--HONumber=Nov16_HO3-->


