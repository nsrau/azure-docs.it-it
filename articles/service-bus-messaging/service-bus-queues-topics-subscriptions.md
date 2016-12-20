---
title: Code, argomenti e sottoscrizioni del bus di servizio | Microsoft Docs
description: "Panoramica delle entità di messaggistica del bus di servizio."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: edee84938bddf28ac4dbf4152ccf9d7ab6afe6c3


---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Code, argomenti e sottoscrizioni del bus di servizio
Il bus di servizio di Microsoft Azure supporta un set di tecnologie middleware orientate ai messaggi e basate sul cloud, incluso l'accodamento dei messaggi affidabile e la messaggistica di pubblicazione e sottoscrizione permanente. Queste funzionalità di messaggistica negoziata possono essere considerate come funzionalità di messaggistica asincrone o disaccoppiate che supportano scenari di pubblicazione e sottoscrizione, disaccoppiamento temporale e bilanciamento del carico tramite l'infrastruttura di messaggistica del bus di servizio.  La comunicazione disaccoppiata presenta molti vantaggi, ad esempio client e server possono connettersi quando necessario ed eseguire le relative operazioni in modo asincrono.

Le entità di messaggistica che costituiscono le funzionalità di messaggistica negoziata di base nel bus di servizio sono code, argomenti/sottoscrizioni e regole/azioni.

## <a name="queues"></a>Code
Le code consentono un recapito dei messaggi di tipo First In, First Out (FIFO) a uno o più consumer concorrenti. In altri termini, i messaggi in genere vengono ricevuti ed elaborati dai ricevitori secondo l'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer. Il vantaggio principale derivante dall'uso delle code è quello di ottenere un "disaccoppiamento temporale" dei componenti applicativi, ovvero non è necessario che i producer e i consumer inviino e ricevano i messaggi contemporaneamente perché i messaggi restano archiviati nella coda. Il producer inoltre non deve attendere la risposta del consumer per continuare a elaborare e inviare messaggi.

Un vantaggio correlato è quello del "livellamento del carico", che permette ai producer e ai consumer di inviare e ricevere i messaggi con frequenze diverse. In molte applicazioni, il carico del sistema varia nel tempo, tuttavia, il tempo di elaborazione necessario per ogni unità è in genere costante. L'interposizione di una coda tra producer e consumer di messaggi implica che è necessario solo eseguire il provisioning dell'applicazione consumer per gestire un carico medio invece di un carico massimo. In base alla variazione del carico in ingresso, si verificherà un incremento o una riduzione della profondità della coda, con un risparmio diretto in termini economici rispetto alle risorse infrastrutturali richieste per gestire il carico dell'applicazione. Con l'aumento del carico, è possibile aggiungere altri processi di lavoro per la lettura della coda. Ciascun messaggio viene elaborato da un solo processo di lavoro. Inoltre, il bilanciamento del carico di tipo pull permette un uso ottimale dei computer di lavoro anche quando questi presentano una potenza di elaborazione diversa.Ogni computer effettuerà infatti il pull dei messaggi in base alla propria velocità massima. Questo modello viene spesso definito modello del "consumer concorrente".

L'uso di code da interporre tra producer e consumer di messaggi fornisce un accoppiamento intrinseco di tipo regime di controllo libero tra i componenti. Poiché producer e consumer sono indipendenti gli uni dagli altri, è possibile aggiornare un consumer senza causare alcun effetto sul producer.

La creazione di una coda è un processo che prevede più passaggi.  Le operazioni di gestione per le entità di messaggistica del bus di servizio (code e argomenti) vengono eseguite tramite la classe [Microsoft.ServiceBus.NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) che viene costruita specificando l'indirizzo di base dello spazio dei nomi e le credenziali utente del bus di servizio. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) specifica i metodi per creare, enumerare ed eliminare le entità di messaggistica. Dopo aver creato un oggetto [Microsoft.ServiceBus.TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) da un nome e una chiave di firma di accesso condiviso e un oggetto di gestione dello spazio dei nomi servizio, è possibile usare il metodo [Microsoft.ServiceBus.NamespaceManager.CreateQueue](https://msdn.microsoft.com/library/azure/hh293157.aspx) per creare la coda. Ad esempio:

```
// Create management credentials
TokenProvider credentials = TokenProvider. CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

È quindi possibile creare un oggetto coda e una factory di messaggistica usando l'URI del bus di servizio come argomento. Ad esempio:

```
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

È quindi possibile inviare messaggi alla coda. Se ad esempio si ha un elenco di messaggi negoziati denominato `MessageList`, sarà possibile usare un codice simile al seguente:

```
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

È possibile ricevere messaggi dalla coda, come indicato nell'esempio seguente:

```
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Quando si usa la modalità [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), la ricezione è un'operazione a un'unica fase. Quando il bus di servizio riceve la richiesta, contrassegna il messaggio come usato e lo restituisce all'applicazione. La modalità [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) rappresenta il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come consumato, quando l'applicazione viene riavviata e inizia a consumare nuovamente i messaggi, il messaggio consumato prima dell'arresto anomalo risulterà perso.

Con la modalità [PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) il processo di ricezione diventa un'operazione in due fasi, che rende possibile il supporto di applicazioni che non riescono a tollerare messaggi mancanti. Quando il bus di servizio riceve la richiesta, individua il messaggio successivo da consumare, lo blocca per impedirne la ricezione da parte di altri consumer e lo restituisce quindi all'applicazione. Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. Quando il bus di servizio vede la chiamata [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), contrassegna il messaggio come usato.

Se per qualche motivo l’applicazione non è in grado di elaborare il messaggio, può chiamare il metodo [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx), invece di [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), per il messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio che sarà disponibile per essere nuovamente ricevuto dallo stesso consumer o da un altro consumer concorrente. Al blocco è associato anche un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sblocca il messaggio rendendolo nuovamente disponibile per la ricezione (eseguendo essenzialmente un'operazione [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) per impostazione predefinita).

Si noti che in caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'emissione della richiesta [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio verrà elaborato almeno una volta. ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, sarà necessaria una logica aggiuntiva nell'applicazione per il rilevamento dei duplicati in base alla proprietà **MessageId** del messaggio, che rimane costante per tutti i tentativi di recapito. Questo tipo di elaborazione viene definito di tipo *Exactly Once*.

Per altre informazioni e per un esempio pratico su come creare e inviare messaggi alle code e dalle code, vedere [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET](service-bus-brokered-tutorial-dotnet.md).

## <a name="topics-and-subscriptions"></a>Argomenti e sottoscrizioni
Diversamente dalle code, in cui ogni messaggio viene elaborato da un unico consumer, gli *argomenti* e le *sottoscrizioni* offrono una forma di comunicazione di tipo uno-a-molti, in un modello di *pubblicazione/sottoscrizione*. Particolarmente utile per la comunicazione con un numero molto elevato di destinatari, ogni messaggio pubblicato è reso disponibile per ogni sottoscrizione registrata con l'argomento. I messaggi vengono inviati a un argomento e recapitati a una o più sottoscrizioni associate, a seconda delle regole di filtro che possono essere impostate per ogni sottoscrizione. Per limitare i messaggi da ricevere, le sottoscrizioni possono usare filtri aggiuntivi. I messaggi vengono inviati a un argomento nello stesso modo in cui vengono inviati a una coda, con la differenza che i messaggi non vengono ricevuti direttamente dall'argomento. Vengono ricevuti dalle sottoscrizioni. La sottoscrizione di un argomento è simile a una coda virtuale che riceve copie dei messaggi inviati all'argomento. La procedura di ricezione dei messaggi da parte di una sottoscrizione è identica a quella usata per la ricezione da parte di una coda.

Ai fini di un confronto, la funzionalità di invio dei messaggi di una coda esegue il mapping direttamente a un argomento e la funzionalità di ricezione dei messaggi esegue il mapping a una sottoscrizione. Questo significa anche che le sottoscrizioni supportano gli stessi modelli descritti prima in questa sezione in merito alle code: consumer concorrente, disaccoppiamento temporale, livellamento del carico e bilanciamento del carico.

La procedura per la creazione di un argomento è simile a quella per la creazione di una coda, come illustrato nell'esempio della sezione precedente. Creare l'URI del servizio e usare la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) per creare il client dello spazio dei nomi. È quindi possibile creare un argomento usando il metodo [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx). Ad esempio:

```
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Aggiungere quindi le sottoscrizioni desiderate:

```
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Sarà quindi possibile creare un client dell'argomento. Ad esempio:

```
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Usando il mittente del messaggio, è possibile inviare messaggi all'argomento e riceverli, come illustrato nella sezione precedente. Ad esempio:

```
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Come accade per le code, i messaggi vengono ricevuti da una sottoscrizione usando un oggetto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) invece di un oggetto [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Creare il client della sottoscrizione, passando il nome dell'argomento, il nome della sottoscrizione e (facoltativamente) la modalità di ricezione come parametri. Ad esempio, con la sottoscrizione **Inventory**:

```
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Regole e azioni
In molti scenari, i messaggi con caratteristiche specifiche devono essere elaborati in modi specifici. A questo scopo, è possibile configurare le sottoscrizioni in modo che trovino i messaggi che presentano le proprietà desiderate e apportare quindi alcune modifiche a tali proprietà. Mentre nelle sottoscrizioni del bus di servizio tutti i messaggi vengono inviati all'argomento, l'utente può copiare solo un subset di tali messaggi nella coda virtuale delle sottoscrizioni. Questa operazione viene eseguita usando i filtri della sottoscrizione. Queste modifiche sono chiamate *azioni di filtro*. Quando viene creata una sottoscrizione, è possibile specificare un'espressione di filtro che agisce sulle proprietà del messaggio, sulle proprietà del sistema, ad esempio **Label**, e sulle proprietà dell'applicazione personalizzata, ad esempio **StoreName**. In questo caso l'espressione di filtro SQL è facoltativa. Senza un'espressione di filtro SQL, qualsiasi azione di filtro definita in una sottoscrizione verrà eseguita in tutti i messaggi di tale sottoscrizione.

Facendo riferimento all'esempio precedente, per filtrare solo i messaggi provenienti da **Store1**, è necessario creare la sottoscrizione Dashboard come indicato nella procedura seguente:

```
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Dopo aver creato questo filtro della sottoscrizione, solo i messaggi con la proprietà `StoreName` impostata su `Store1` vengono copiati nella coda virtuale per la sottoscrizione `Dashboard`.

Per altre informazioni sui valori di filtro possibili, vedere la documentazione relativa alle classi [SqlFilter](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) e [SqlRuleAction](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlruleaction.aspx). Vedere anche gli esempi [Brokered Messaging: Advanced Filters](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) (Messaggistica negoziata: filtri avanzati) e [Topic Filters](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) (Filtri di argomento).

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi e informazioni sull'uso delle entità di messaggistica negoziata del bus di servizio, vedere gli argomenti avanzati seguenti.

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET](service-bus-brokered-tutorial-dotnet.md)
* [Esercitazione sulla messaggistica negoziata del bus di servizio - REST](service-bus-brokered-tutorial-rest.md)
* [Topic filters sample](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) (Esempio Filtri di argomento)
* [Brokered Messaging: Advanced Filters sample](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) (Esempio Messaggistica negoziata: filtri avanzati)




<!--HONumber=Nov16_HO3-->


