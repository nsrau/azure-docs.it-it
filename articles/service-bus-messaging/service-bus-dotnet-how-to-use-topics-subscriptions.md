---
title: Usare gli argomenti del bus di servizio di Azure con .NET | Microsoft Docs
description: Informazioni su come usare le sottoscrizioni e gli argomenti del bus di servizio con .NET in Azure. Gli esempi di codice sono scritti per applicazioni .NET.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: bec18e91ef8798a791d4b1fe93bd529593197e01
ms.lasthandoff: 03/24/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Come usare gli argomenti e le sottoscrizioni del bus di servizio
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questo articolo descrive come usare gli argomenti e le sottoscrizioni del bus di servizio. Gli esempi sono scritti in C# e usano le API .NET. Gli scenari presentati includono creazione di argomenti e sottoscrizioni, creazione di filtri per le sottoscrizioni, invio di messaggi a un argomento, ricezione di messaggi da una sottoscrizione ed eliminazione di argomenti e sottoscrizioni. Per altre informazioni sugli argomenti e sulle sottoscrizioni, vedere la sezione [Passaggi successivi](#next-steps).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio
Quando si crea un'applicazione che usa il bus di servizio, è necessario aggiungere un riferimento all'assembly del bus di servizio e includere gli spazi dei nomi corrispondenti. Il modo più semplice per eseguire questa operazione consiste nello scaricare il pacchetto [NuGet](https://www.nuget.org) appropriato.

## <a name="get-the-service-bus-nuget-package"></a>Ottenere il pacchetto NuGet del bus di servizio
Il [pacchetto NuGet del bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus) è il modo più semplice per configurare l'applicazione con tutte le dipendenze necessarie del bus di servizio. Per installare il pacchetto NuGet del bus di servizio nel progetto, eseguire queste operazioni:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Gestisci pacchetti NuGet**.
2. Fare clic su **Sfoglia**, cercare "Bus di servizio di Azure" e quindi selezionare la voce relativa al **bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione e quindi chiudere la finestra di dialogo:
   
   ![][7]

È ora possibile scrivere codice per il bus di servizio.

## <a name="create-a-service-bus-connection-string"></a>Creare una stringa di connessione per il bus di servizio
Per archiviare endpoint e credenziali, nel bus di servizio viene usata una stringa di connessione. È possibile inserire la stringa di connessione in un file di configurazione, anziché impostarla come hardcoded:

* Quando si usano i servizi di Azure, è consigliabile archiviare la stringa di connessione usando il sistema di configurazione dei servizi di Azure (file con estensione csdef e cscfg).
* Quando si usa Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file Web.config.

In entrambi i casi è possibile recuperare la stringa di connessione usando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### <a name="configure-your-connection-string"></a>Configurare la stringa di connessione
Il meccanismo di configurazione dei servizi consente di modificare dinamicamente le impostazioni di configurazione dal [portale di Azure][Azure portal] senza ridistribuire l'applicazione. Aggiungere ad esempio un'etichetta `Setting` al file di definizione del servizio **.csdef**, come illustrato nell'esempio seguente.

```xml
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Specificare quindi i valori nel file di configurazione del servizio (.cscfg).

```xml
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Usare i valori della chiave e il nome della chiave di firma di accesso condiviso recuperati dal portale come descritto prima.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurare la stringa di connessione per l'uso con Siti Web di Azure o Macchine virtuali di Azure
Quando si usa Siti Web o Macchine virtuali di Azure, è consigliabile usare il sistema di configurazione .NET, ad esempio il file Web.config. Per archiviare la stringa di connessione, usare l'elemento `<appSettings>`.

```xml
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Usare i valori relativi alla chiave e al nome della firma di accesso condiviso recuperati dal [portale di Azure][Azure portal] come descritto in precedenza.

## <a name="create-a-topic"></a>Creare un argomento
Per eseguire operazioni di gestione per gli argomenti e le sottoscrizioni del bus di servizio, è possibile usare la classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). Questa classe fornisce metodi per creare, enumerare ed eliminare gli argomenti.

L'esempio seguente crea un oggetto `NamespaceManager` usando la classe `CloudConfigurationManager` di Azure con una stringa di connessione composta dall'indirizzo di base di uno spazio dei nomi del bus di servizio e dalle credenziali di firma di accesso condiviso appropriate con le autorizzazioni per gestirlo. Il formato della stringa di connessione è il seguente:

```xml
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Usare l'esempio seguente, per le impostazioni di configurazione della sezione precedente.

```csharp
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Per il metodo [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager) sono disponibili overload che consentono di configurare le proprietà dell'argomento, ad esempio impostare il valore predefinito della durata TTL da applicare ai messaggi inviati all'argomento. Per applicare queste impostazioni viene usata la classe [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription). L'esempio seguente mostra come creare un argomento denominato **TestTopic** con una dimensione massima pari a 5 GB e una durata TTL predefinita per i messaggi pari a 1 minuto.

```csharp
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> È possibile usare il metodo [TopicExists](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_TopicExists_System_String_) su oggetti [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) per verificare se in uno spazio dei nomi del servizio esiste già un argomento con il nome specificato.
> 
> 

## <a name="create-a-subscription"></a>Creare una sottoscrizione
È anche possibile usare la classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) per creare sottoscrizioni di argomenti. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi passati alla coda virtuale della sottoscrizione.

> [!IMPORTANT]
> Per far sì che una sottoscrizione riceva i messaggi, è necessario creare la sottoscrizione prima di inviare messaggi all'argomento. Se non sono presenti sottoscrizioni per un argomento, questi messaggi verranno ignorati dall'argomento.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare una sottoscrizione con il filtro (MatchAll) predefinito
Se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione, viene usato il filtro predefinito **MatchAll**. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "AllMessages" e viene usato il filtro predefinito **MatchAll**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Creare sottoscrizioni con i filtri
È anche possibile configurare filtri che consentono di specificare quali messaggi inviati a un argomento devono essere presenti in una specifica sottoscrizione dell'argomento.

Il tipo di filtro più flessibile supportato dalle sottoscrizioni è la classe [SqlFilter][SqlFilter], che implementa un subset di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altre informazioni sulle espressioni che possono essere usate con un filtro SQL, vedere la sintassi di [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

L'esempio seguente crea una sottoscrizione denominata **HighMessages** con un oggetto [SqlFilter][SqlFilter] che seleziona solo i messaggi che hanno una proprietà **MessageNumber** personalizzata maggiore di 3.

```csharp
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageId > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Analogamente, l'esempio seguente crea una sottoscrizione denominata **LowMessages** con un filtro [SqlFilter][SqlFilter] che seleziona solo i messaggi in cui il valore della proprietà **MessageNumber** è minore o uguale a 3.

```csharp
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageId <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Quando un messaggio viene inviato a `TestTopic`, viene sempre recapitato ai ricevitori aderenti alla sottoscrizione dell'argomento **AllMessages** e viene recapitato selettivamente ai ricevitori aderenti alle sottoscrizioni dell'argomento **HighMessages** e **LowMessages**, a seconda del contenuto del messaggio.

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
Per inviare un messaggio a un argomento del bus di servizio, nell'applicazione viene usata la stringa di connessione per creare un oggetto [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient).

Il codice seguente illustra come creare un oggetto [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) per l'argomento **TestTopic** creato in precedenza usando l'API [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.topicclient#Microsoft_ServiceBus_Messaging_TopicClient_CreateFromConnectionString_System_String_System_String_).

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

I messaggi inviati ad argomenti del bus di servizio sono istanze della classe [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Gli oggetti **BrokeredMessage** includono un insieme di proprietà standard, ad esempio [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) e [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive), un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo del messaggio passando un oggetto serializzabile al costruttore dell'oggetto **BrokeredMessage** e l'elemento **DataContractSerializer** appropriato viene quindi usato per serializzare l'oggetto. In alternativa, è possibile specificare un oggetto **System.IO.Stream**.

L'esempio seguente illustra come inviare cinque messaggi di prova all'oggetto [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) di **TestTopic** ottenuto nell'esempio di codice precedente. Si noti che il valore della proprietà [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) di ogni messaggio varia a seconda dell'iterazione del ciclo, determinando le sottoscrizioni che lo riceveranno.

```csharp
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageId"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e pari a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB. Se il partizionamento è abilitato, il limite superiore è più elevato. Per altre informazioni, vedere [Entità di messaggistica partizionate](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Come ricevere messaggi da una sottoscrizione
Il modo consigliato per ricevere i messaggi da una sottoscrizione consiste nell'usare un oggetto [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient). Gli oggetti **SubscriptionClient** agiscono in due modalità diverse: [*ReceiveAndDelete* e *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** è la modalità predefinita.

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve una richiesta di lettura relativa a un messaggio in una sottoscrizione, lo contrassegna come usato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio ha contrassegnato il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** predefinita, il processo di ricezione viene suddiviso in due fasi, per poter consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) sul messaggio ricevuto. Quando il bus di servizio vede la chiamata **Complete**, contrassegna il messaggio come utilizzato e lo rimuove dalla sottoscrizione.

L'esempio seguente illustra come ricevere ed elaborare messaggi usando la modalità **PeekLock** predefinita. Per specificare un valore diverso per [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode), è possibile usare un altro overload per [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_CreateFromConnectionString_System_String_System_String_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_). Questo esempio usa il callback [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) per elaborare i messaggi non appena arrivano nella sottoscrizione **HighMessages**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Questo esempio configura il callback [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) usando un oggetto [OnMessageOptions](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions). [AutoComplete](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoComplete) è impostato su **false** per abilitare il controllo manuale della chiamata [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) sul messaggio ricevuto. Se [AutoRenewTimeout](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout) è impostato su 1 minuto, il client dovrà attendere fino a un minuto prima di terminare la funzionalità di rinnovo automatico ed eseguirà una nuova chiamata per verificare la presenza di messaggi. Il valore di questa proprietà riduce il numero di volte in cui il client effettua chiamate addebitabili che non consentono di recuperare i messaggi.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non può elaborare il messaggio per un motivo qualsiasi, può chiamare il metodo [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon_System_Collections_Generic_IDictionary_System_String_System_Object__), anziché [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), sul messaggio ricevuto. In questo modo, il bus di servizio sblocca il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è associato anche un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima dell'invio della richiesta [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), il messaggio verrà recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare argomenti e sottoscrizioni
L'esempio seguente illustra come eliminare l'argomento **TestTopic** dallo spazio dei nomi del servizio **HowToSample**.

```csharp
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Se si elimina un argomento, vengono eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il codice seguente illustra come eliminare una sottoscrizione denominata **HighMessages** dall'argomento **TestTopic**.

```csharp
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base degli argomenti e delle sottoscrizioni del bus di servizio, usare i collegamenti seguenti per altre informazioni.

* [Code, argomenti e sottoscrizioni][Queues, topics, and subscriptions].
* [Esempio di filtri di argomento][Topic filters sample].
* Informazioni di riferimento sulle API per [SqlFilter][SqlFilter].
* Per creare un'applicazione funzionante che invia e riceve messaggi verso e da una coda del bus di servizio, vedere [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET][Service Bus brokered messaging .NET tutorial].
* Per esempi relativi al bus di servizio, eseguire il download dagli [esempi di Azure][Azure samples] oppure vedere la [panoramica](service-bus-samples.md).

[Azure portal]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Topic filters sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

