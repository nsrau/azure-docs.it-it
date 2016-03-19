<properties 
   pageTitle="Creazione di applicazioni che utilizzano argomenti e sottoscrizioni del bus di servizio | Microsoft Azure"
   description="Introduzione alle funzionalità di pubblicazione-sottoscrizione offerte dagli argomenti e sottoscrizioni del Bus di servizio."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="sethm" />

# Crea applicazioni che utilizzano argomenti e sottoscrizioni del bus di servizio

Il bus di servizio di Azure supporta un set di tecnologie middleware orientate ai messaggi e basate sul cloud, incluso l'accodamento dei messaggi affidabile e la messaggistica di pubblicazione e sottoscrizione permanente. Questo articolo si basa sulle informazioni fornite in [Creare applicazioni che utilizzano le code del Bus di servizio](service-bus-create-queues.md) e offre un'introduzione alle funzionalità di pubblicazione/sottoscrizione offerte da argomenti del Bus di servizio.

## Scenario di vendita al dettaglio in evoluzione

In questo articolo continua lo scenario di vendita al dettaglio utilizzato in [Creare applicazioni che utilizzano le code del Bus di servizio](service-bus-create-queues.md). Richiamare i dati di vendita da singoli terminali di punto di vendita (POS) devono essere indirizzati a un sistema di gestione dell’inventario che utilizza i dati per determinare quando è necessario un nuovo approvvigionamento. Ogni terminale POS segnala i dati di vendita dall'invio di messaggi per la coda **DataCollectionQueue** dove rimangono fino a quando non vengono ricevuti dal sistema di gestione dell’inventario, come illustrato di seguito:

![Servizio Bus1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Per sviluppare questo scenario, è stato aggiunto un nuovo requisito al sistema: il proprietario dell'archivio vuole essere in grado di monitorare le prestazioni dell'archivio in tempo reale.

Per soddisfare questo requisito, il sistema deve "disattivare" il flusso di dati di vendita. Vogliamo ancora che ogni messaggio inviato dai terminali POS vengano inviati al sistema di gestione del magazzino come prima, ma si desidera un'altra copia di ogni messaggio che è possibile utilizzare per presentare la visualizzazione dashboard al proprietario del negozio.

In qualsiasi situazione come questa, nella quali si richiede che ogni messaggio venga utilizzato da più parti, è possibile utilizzare il Bus di servizio *argomenti*. Gli argomenti forniscono il modello pubblicazione/sottoscrizione, in cui ogni messaggio pubblicato viene reso disponibile a una o più sottoscrizioni registrate per l'argomento. Al contrario, con le code ogni messaggio viene ricevuto da un singolo consumer.

I messaggi vengono inviati a un argomento nello stesso modo in cui vengono trasmessi a una coda. Tuttavia, i messaggi non vengono ricevuti dall'argomento direttamente; vengono ricevuti dalle sottoscrizioni. È possibile pensare alla sottoscrizione a un argomento come a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. La procedura di ricezione dei messaggi da parte di una sottoscrizione è la stessa di quella usata per la ricezione da parte di una coda.

Tornando allo scenario di vendita al dettaglio, la coda viene sostituita da un argomento e viene aggiunta una sottoscrizione che verrà utilizzata dal componente di sistema di gestione inventario. Il sistema ora l'aspetto seguente:

![Servizio Bus2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

La configurazione qui si comporta in modo identico alla precedente progettazione basata su coda. Ovvero vengono instradati i messaggi inviati all'argomento attraverso la sottoscrizione **inventario**, da cui il **Sistema di gestione dell’inventario** li utilizza.

Per supportare il dashboard di gestione, creiamo una seconda sottoscrizione sull'argomento, come illustrato di seguito:

![Servizio Bus3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Con questa configurazione, ogni messaggio da terminali POS viene reso disponibile a entrambe le sottoscrizioni **Dashboard** e **inventario**.

## Mostra il codice

[Creare applicazioni che utilizzano le code del Bus di servizio](service-bus-create-queues.md) descrive come registrare un account per un Bus di servizio e creare uno spazio dei nomi del servizio. Per utilizzare uno spazio dei nomi del Bus di servizio, un'applicazione deve fare riferimento all'assembly del Bus di servizio, in particolare a Microsoft.ServiceBus.dll. Il modo più semplice per fare riferimento alle dipendenze del Bus di servizio è installare il [pacchetto Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/).del Bus di servizio È inoltre possibile trovare l'assembly come parte di Azure SDK. Il download è disponibile all'indirizzo di [pagina di download di Azure SDK](https://azure.microsoft.com/downloads/).

### Creare l'argomento e le sottoscrizioni

Operazioni di gestione per entità di messaggistica (code e pubblicazione/sottoscrizione di argomenti) per il Bus di servizio vengono eseguite tramite la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Sono necessarie le credenziali appropriate per creare un’istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) per un determinato spazio dei nomi. Il Bus di servizio utilizza un modello basato sulla sicurezza [Firma di accesso condiviso (SAS)](service-bus-sas-overview.md) La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) rappresenta un provider di token di sicurezza con metodi factory incorporati che restituiscono alcuni provider di token noti. Utilizzeremo un metodo [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) per contenere le credenziali di firma di accesso condiviso. L’istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene quindi costruita con l'indirizzo di base dello spazio dei nomi del Bus di servizio e il provider di token.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornisce i metodi per creare, enumerare ed eliminare le entità di messaggistica. Il codice riportato di seguito mostra come l’istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene creata e utilizzata per creare l’argomento **DataCollectionTopic**.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Si noti che esistono overload del metodo [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) che abilitano le impostazioni dell’argomento da ottimizzare. Ad esempio, è possibile impostare il valore di durata (TTL) predefinito per i messaggi inviati all’argomento. Aggiungere quindi le sottoscrizioni **Inventario** e **Dashboard**.

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### Invio di messaggi all’argomento

Per le operazioni di runtime su entità del Bus di servizio; ad esempio, l'invio e la ricezione di messaggi, un'applicazione deve prima creare un oggetto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Simile alla classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), l’istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) viene creata a partire dall'indirizzo di base dello spazio dei nomi del servizio e dal provider di token.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

I messaggi inviati e ricevuti dagli argomenti del bus di servizio sono istanze della classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Questa classe include un insieme di proprietà standard, (ad esempio [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) and [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dizionario utilizzato per contenere le proprietà dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo passando qualsiasi oggetto serializzabile (nell'esempio seguente passa in un oggetto **SalesData** che rappresenta i dati di vendita dal terminale POS), che utilizzerà il [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) per serializzare l'oggetto. In alternativa, un oggetto [Flusso](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) può essere fornito.

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Il modo più semplice per inviare messaggi a un argomento, consiste nell'utilizzare [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) per creare un oggetto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) direttamente dall'istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### Ricevere messaggi da una sottoscrizione

Similmente a quanto accade con le code, per ricevere messaggi da una sottoscrizione è possibile utilizzare un oggetto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) che è possibile creare direttamente da [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) utilizzando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). È possibile utilizzare una delle due diverse modalità di ricezione (**ReceiveAndDelete** e **PeekLock**), come illustrato in [Creare applicazioni che utilizzano le code del Bus di servizio](service-bus-create-queues.md).

Si noti che quando si crea un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) per le sottoscrizioni, il parametro *entityPath* è nel modulo `topicPath/subscriptions/subscriptionName`. Pertanto, per creare un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) per la sottoscrizione **Inventario** dell’argomento **DataCollectionTopic**, *entityPath* deve essere impostato su `DataCollectionTopic/subscriptions/Inventory`. Il codice viene visualizzato come segue:

```
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

## Filtri di sottoscrizione

Finora, in questo articolo vengono resi disponibili a tutte le sottoscrizioni registrate tutti i messaggi inviati all'argomento. La frase chiave è "reso disponibile." Mentre nelle sottoscrizioni del bus di servizio tutti i messaggi vengono inviati all'argomento, l'utente può copiare solo un subset di tali messaggi nella coda virtuale delle sottoscrizioni. Questa operazione viene eseguita mediante la sottoscrizione *filtri*. Quando si crea una sottoscrizione, è possibile fornire un'espressione di filtro sotto forma di un predicato stile SQL92 che funziona tramite le proprietà del messaggio,sia delle proprietà di sistema (ad esempio, [etichetta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) che le proprietà dell'applicazione, ad esempio **StoreName** nell'esempio precedente.

Nell’evoluzione dello scenario per illustrare questo concetto, un secondo archivio deve essere aggiunto a questo scenario di vendita al dettaglio. I dati di vendita da tutti i terminali POS da entrambi gli archivi devono essere instradati al sistema di gestione centralizzato dell'inventario, ma un gestore dell'archivio che utilizza lo strumento dashboard è solo interessato sulle prestazioni di tale archivio. È possibile utilizzare il filtro di sottoscrizione per ottenere questo risultato. Si noti che quando i terminali POS pubblicano messaggi, viene impostate la proprietà di applicazione **StoreName** nel messaggio. Dati due archivi, ad esempio **Redmond** e **Seattle**, i terminali POS nell'archivio di Redmond contrassegnano i messaggi di dati di vendita con uno **StoreName** uguale a **Redmond**, mentre i terminali POS dell’archivio Seattle utilizzano uno **StoreName** uguale a **Seattle**. Il gestore dell'archivio di Redmond desidera solo visualizzare i dati dai propri terminali POS. Il sistema viene visualizzato come segue:

![Servizio Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Per impostare questo ciclo, si crea la sottoscrizione **Dashboard** come segue:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Con questo filtro della sottoscrizione, solo i messaggi con la proprietà **StoreName** impostata su **Redmond** vengono copiati nella coda virtuale per la sottoscrizione **Dashboard**. Tuttavia, c'è molto di più nel filtro di sottoscrizione. Le applicazioni possono avere più regole di filtro per ogni sottoscrizione oltre alla possibilità di modificare le proprietà di un messaggio quando viene posizionato nella coda virtuale della sottoscrizione.

## Riepilogo

Tutti i motivi per utilizzare l’accodamento messaggi descritto in [Creare applicazioni che utilizzano le code del Bus di servizio](service-bus-create-queues.md) si applicano anche agli argomenti, in particolare:

- Disaccoppiamento temporale - producer e consumer del messaggio non devono necessariamente essere online contemporaneamente.

- Livellamento del carico - i picchi di carico sono smussati dall'argomento consentendo l'utilizzo di applicazioni per eseguire il provisioning per un carico medio anziché il carico massimo.

- Bilanciamento del carico - simile a una coda, è possibile disporre di più consumer concorrenti in ascolto su una singola sottoscrizione con ogni messaggio passato a uno solo dei consumatori, ottenendo il bilanciamento del carico.

- Accoppiamento di tipo loose - è possibile sviluppare la rete di messaggistica senza influire sugli endpoint esistenti; ad esempio, aggiungere sottoscrizioni o modificare i filtri a un argomento per consentire nuovi clienti.

## Passaggi successivi

Vedere [Creare applicazioni che utilizzano le code del Bus di servizio](service-bus-create-queues.md) per informazioni su come usare le code nello scenario di vendita al dettaglio POS.

<!---HONumber=AcomDC_0128_2016-->