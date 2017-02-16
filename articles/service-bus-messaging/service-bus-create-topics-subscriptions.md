---
title: Creare applicazioni che usano argomenti e sottoscrizioni del bus di servizio | Documentazione Microsoft
description: "Introduzione alle funzionalità di pubblicazione/sottoscrizione offerte dagli argomenti e dalle sottoscrizioni del bus di servizio."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a48fc9b0-b7b0-464e-8187-a517bf8b4eb4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 799ef33c924a0067bb5e8da9d1b4e50091dbabf6


---
# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Creare applicazioni che usano argomenti e sottoscrizioni del bus di servizio
Il bus di servizio di Azure supporta un set di tecnologie middleware orientate ai messaggi e basate sul cloud, incluso l'accodamento dei messaggi affidabile e la messaggistica di pubblicazione e sottoscrizione permanente. Questo articolo si basa sulle informazioni presentate in [Creare applicazioni che usano code del bus di servizio](service-bus-create-queues.md) e include un'introduzione alle funzionalità di pubblicazione/sottoscrizione offerte da argomenti del bus di servizio.

## <a name="evolving-retail-scenario"></a>Scenario di vendita al dettaglio in evoluzione
Questo articolo usa lo scenario di vendita al dettaglio incluso in [Creare applicazioni che usano le code del bus di servizio](service-bus-create-queues.md). Richiamare i dati di vendita da singoli terminali di punto di vendita (POS) devono essere indirizzati a un sistema di gestione dell’inventario che utilizza i dati per determinare quando è necessario un nuovo approvvigionamento. Ogni terminale POS segnala i dati di vendita tramite l'invio di messaggi alla coda **DataCollectionQueue** dove rimangono fino a quando non vengono ricevuti dal sistema di gestione dell'inventario, come illustrato di seguito:

![Bus di servizio 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Per sviluppare questo scenario, è stato aggiunto un nuovo requisito al sistema: il proprietario dell'archivio vuole essere in grado di monitorare le prestazioni dell'archivio in tempo reale.

Per soddisfare questo requisito, il sistema deve "disattivare" il flusso di dati di vendita. Vogliamo ancora che ogni messaggio inviato dai terminali POS vengano inviati al sistema di gestione del magazzino come prima, ma si desidera un'altra copia di ogni messaggio che è possibile utilizzare per presentare la visualizzazione dashboard al proprietario del negozio.

In qualsiasi situazione come questa, in cui si richiede che ogni messaggio venga letto da più parti, è possibile usare gli *argomenti* del bus di servizio. Gli argomenti forniscono un modello di pubblicazione/sottoscrizione, in cui ogni messaggio pubblicato viene reso disponibile a una o più sottoscrizioni registrate con l'argomento. Al contrario, con le code ogni messaggio viene ricevuto da un singolo consumer.

I messaggi vengono inviati a un argomento nello stesso modo in cui vengono trasmessi a una coda. Tuttavia, i messaggi non vengono ricevuti dall'argomento direttamente, ma dalle sottoscrizioni. È possibile pensare alla sottoscrizione a un argomento come a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. La procedura di ricezione dei messaggi da parte di una sottoscrizione è la stessa usata per la ricezione da parte di una coda.

Tornando allo scenario di vendita al dettaglio, la coda viene sostituita da un argomento e viene aggiunta una sottoscrizione che verrà usata dal componente del sistema di gestione del magazzino. Il sistema ora l'aspetto seguente:

![Bus di servizio 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

La configurazione qui si comporta in modo identico alla precedente progettazione basata su coda. Ovvero i messaggi inviati all'argomento vengono instradati alla sottoscrizione **Inventory** e letti dal **sistema di gestione del magazzino**.

Per supportare il dashboard di gestione, viene creata una seconda sottoscrizione nell'argomento, come illustrato di seguito:

![Bus di servizio 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Con questa configurazione, ogni messaggio da terminali POS viene reso disponibile a entrambe le sottoscrizioni **Dashboard** e **Inventory**.

## <a name="show-me-the-code"></a>Mostra il codice
L'articolo [Creare applicazioni che usano le code del bus di servizio](service-bus-create-queues.md) descrive come registrare un account di Azure e creare uno spazio dei nomi del servizio. Per utilizzare uno spazio dei nomi del Bus di servizio, un'applicazione deve fare riferimento all'assembly del Bus di servizio, in particolare a Microsoft.ServiceBus.dll. Il modo più semplice per fare riferimento alle dipendenze del bus di servizio è di installare il [pacchetto Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) del bus di servizio. È inoltre possibile trovare l'assembly come parte di Azure SDK. Il download è disponibile nella [pagina di download di Azure SDK](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Creare l'argomento e le sottoscrizioni
Le operazioni di gestione per entità di messaggistica (code e argomenti di pubblicazione/sottoscrizione) del bus di servizio vengono eseguite tramite la classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager). Sono necessarie le credenziali appropriate per creare un'istanza **NamespaceManager** per un determinato spazio dei nomi. Il bus di servizio usa un modello di sicurezza basato su [SAS (Shared Access Signature, Firma di accesso condiviso)](service-bus-sas-overview.md). La classe [TokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) rappresenta un provider di token di sicurezza con metodi factory incorporati che restituiscono alcuni provider di token noti. Per memorizzare le credenziali SAS, verrà usato un metodo [CreateSharedAccessSignatureTokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_). L'istanza [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) viene costruita con l'indirizzo di base dello spazio dei nomi del bus di servizio e con il provider di token.

La classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) offre i metodi per creare, enumerare ed eliminare entità di messaggistica. Il codice riportato di seguito visualizza come l'istanza **NamespaceManager** viene creata e usata per definire l'argomento **DataCollectionTopic**.

```csharp
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";

TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);

namespaceManager.CreateTopic("DataCollectionTopic");
```

Si noti che sono disponibili overload del metodo [CreateTopic](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) che consentono di impostare le proprietà dell'argomento. Ad esempio, è possibile impostare il valore di durata (TTL) predefinito per i messaggi inviati all'argomento. Aggiungere successivamente le sottoscrizioni **Inventory** e **Dashboard**.

```csharp
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Inviare messaggi all'argomento
Per le operazioni di runtime su entità del bus di servizio, ad esempio l'invio e la ricezione di messaggi, un'applicazione deve prima creare un oggetto [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#microsoft_servicebus_messaging_messagingfactory). Simile alla classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager), l'istanza **MessagingFactory** viene creata a partire dall'indirizzo di base dello spazio dei nomi del servizio e dal provider di token.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

I messaggi inviati e ricevuti dagli argomenti del bus di servizio sono istanze della classe [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Questa classe consiste di un insieme di proprietà standard, (ad esempio, [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) e [TimeToLive](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), di un dizionario usato per mantenere le proprietà dell'applicazione e di un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo passando qualsiasi oggetto serializzabile (nell'esempio seguente passa un oggetto **SalesData** che rappresenta i dati di vendita dal terminale POS), che userà [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx) per serializzare l'oggetto. In alternativa, può essere specificato un oggetto [Flusso](https://msdn.microsoft.com/library/system.io.stream.aspx).

```csharp
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Il modo più semplice per inviare messaggi all'argomento consiste nell'usare [CreateMessageSender](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageSender_System_String_) per creare un oggetto [MessageSender](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesender) direttamente dall'istanza di [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory).

```csharp
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
Similmente a quanto accade con l'uso delle code, per ricevere messaggi da una sottoscrizione, è possibile usare un oggetto [MessageReceiver](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagereceiver) che si crea direttamente da [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory) tramite [CreateMessageReceiver](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_). È possibile usare una delle due diverse modalità di ricezione, **ReceiveAndDelete** e **PeekLock**, come illustrato in [Creare applicazioni che usano le code del bus di servizio](service-bus-create-queues.md).

Si noti che, quando si crea un oggetto **MessageReceiver** per le sottoscrizioni, il parametro *entityPath* segue il formato `topicPath/subscriptions/subscriptionName`. Per creare quindi un oggetto **MessageReceiver** per la sottoscrizione **Inventory** dell'argomento **DataCollectionTopic**, *entityPath* deve essere impostato su `DataCollectionTopic/subscriptions/Inventory`. Il codice viene visualizzato come segue:

```csharp
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## <a name="subscription-filters"></a>Filtri di sottoscrizione
Finora, in questo scenario tutti i messaggi inviati all'argomento sono resi disponibili per tutte le sottoscrizioni registrate. La frase chiave è "reso disponibile." Mentre nelle sottoscrizioni del bus di servizio tutti i messaggi vengono inviati all'argomento, l'utente può copiare solo un subset di tali messaggi nella coda virtuale delle sottoscrizioni. Questa operazione viene eseguita usando i *filtri* della sottoscrizione. Quando si crea una sottoscrizione, è possibile definire un'espressione di filtro sotto forma di predicato di stile SQL92 che usa le proprietà del messaggio, ovvero le proprietà di sistema, ad esempio [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label), e le proprietà dell'applicazione, ad esempio **StoreName** nell'esempio precedente.

Nell’evoluzione dello scenario per illustrare questo concetto, un secondo archivio deve essere aggiunto a questo scenario di vendita al dettaglio. I dati di vendita da tutti i terminali POS da entrambi gli archivi devono essere instradati al sistema di gestione centralizzato dell'inventario, ma un gestore dell'archivio che utilizza lo strumento dashboard è solo interessato sulle prestazioni di tale archivio. È possibile usare i filtri della sottoscrizione per ottenere questo risultato. Si noti che quando i terminali POS pubblicano messaggi, impostano anche la proprietà di applicazione **StoreName** nel messaggio. Ad esempio, in due archivi denominati **Redmond** e **Seattle**, i terminali POS dell'archivio di Redmond contrassegnano i messaggi di dati di vendita con uno **StoreName** uguale a **Redmond**, mentre i terminali POS dell'archivio di Seattle usano uno **StoreName** uguale a **Seattle**. Il gestore dell'archivio di Redmond desidera solo visualizzare i dati dai propri terminali POS. Il sistema viene visualizzato come segue:

![Servizio Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Per configurare questo routing, si crea la sottoscrizione **Dashboard** come segue:

```csharp
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Con questo [filtro della sottoscrizione](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter), solo i messaggi con la proprietà **StoreName** impostata su **Redmond** vengono copiati nella coda virtuale per la sottoscrizione **Dashboard**. Tuttavia, i filtri della sottoscrizione presentano molti altri vantaggi. Le applicazioni possono avere più regole di filtro per ogni sottoscrizione oltre alla possibilità di modificare le proprietà di un messaggio quando passa alla coda virtuale di una sottoscrizione.

## <a name="summary"></a>Riepilogo
Tutti i motivi per usare l'accodamento descritti in [Creare applicazioni che usano code del bus di servizio](service-bus-create-queues.md) sono validi anche per gli argomenti, in particolare:

* Disaccoppiamento temporale - producer e consumer del messaggio non devono necessariamente essere online contemporaneamente.
* Livellamento del carico: i picchi di carico vengono livellati dall'argomento consentendo l'utilizzo di applicazioni di cui effettuare il provisioning per il carico medio invece che per il picco di carico.
* Bilanciamento del carico: come con una coda, è possibile avere più consumer concorrenti in ascolto su una singola sottoscrizione, con ogni messaggio passato a uno solo dei consumer, ottenendo così il bilanciamento del carico.
* Regime di controllo libero: è possibile evolvere la rete di messaggistica senza influire sugli endpoint esistenti, ad esempio, aggiungendo sottoscrizioni o modificando i filtri per un argomento per consentire nuovi consumer.

## <a name="next-steps"></a>Passaggi successivi
Vedere [Creare applicazioni che usano le code del bus di servizio](service-bus-create-queues.md) per informazioni su come usare le code nello scenario di vendita al dettaglio POS.




<!--HONumber=Jan17_HO2-->


