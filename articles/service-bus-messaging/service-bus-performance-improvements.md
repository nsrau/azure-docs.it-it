---
title: Procedure consigliate per migliorare le prestazioni con il bus di servizio di Azure
description: Descrive come usare il bus di servizio per ottimizzare le prestazioni durante gli scambi di messaggi negoziati.
ms.topic: article
ms.date: 11/11/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9104c5f4a01459c00327da1b60ad811787b7e22f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541267"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica del bus di servizio

Questo articolo descrive come usare il bus di servizio di Azure per ottimizzare le prestazioni durante lo scambio di messaggi negoziati. La prima parte di questo articolo descrive i diversi meccanismi per migliorare le prestazioni. La seconda parte fornisce indicazioni sull'uso del bus di servizio in modo da garantire prestazioni ottimali in un determinato scenario.

In questo articolo il termine "client" fa riferimento a qualsiasi entità che accede al bus di servizio. Un client può assumere il ruolo di mittente o di ricevitore. Il termine "mittente" viene usato per un client di coda del bus di servizio o un client dell'argomento che invia messaggi a una coda o a un argomento del bus di servizio. Il termine "ricevitore" si riferisce a un client della coda del bus di servizio o a un client di sottoscrizione che riceve messaggi da una coda o una sottoscrizione del bus di servizio.

## <a name="protocols"></a>Protocolli
Il bus di servizio consente ai client di inviare e ricevere messaggi tramite uno dei tre protocolli:

1. Advanced Message Queuing Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. Hypertext Transfer Protocol (HTTP)

AMQP è la più efficiente, perché mantiene la connessione al bus di servizio. Implementa anche le operazioni di invio in batch e prelettura. Se non è indicato in modo esplicito, tutti i contenuti di questo articolo presuppongono l'uso di AMQP o SBMP.

> [!IMPORTANT]
> SBMP è disponibile solo per .NET Framework. AMQP è l'impostazione predefinita per .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Scelta dell'SDK .NET di Service Bus appropriato
Sono supportati due SDK .NET del bus di servizio di Azure. Le API sono simili e possono confondere la scelta. Vedere la tabella seguente per guidare la decisione. È consigliabile usare Microsoft. Azure. ServiceBus SDK perché è più moderno, efficiente ed è compatibile multipiattaforma. Supporta inoltre AMQP su WebSocket ed è parte della raccolta di progetti open source di Azure .NET SDK.

| Pacchetto NuGet | Spazi dei nomi primari | Piattaforma/i minima | Protocolli |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft. Azure. ServiceBus <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Piattaforma UWP (Universal Windows Platform) 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure. ServiceBus <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Per ulteriori informazioni sul supporto della piattaforma .NET Standard minima, vedere [supporto dell'implementazione di .NET](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Riutilizzo di factory e client

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Gli oggetti client del bus di servizio, ad esempio le implementazioni di [`IQueueClient`][QueueClient] o [`IMessageSender`][MessageSender] , devono essere registrati per l'inserimento di dipendenze come singleton (o per cui è stata creata un'istanza una volta e condivise). Si consiglia di non chiudere le factory di messaggistica, la coda, l'argomento o i client di sottoscrizione dopo l'invio di un messaggio e quindi ricrearli quando si invia il messaggio successivo. La chiusura di una factory di messaggistica comporta l'eliminazione della connessione al servizio del bus di servizio. Quando si ricrea la factory viene stabilita una nuova connessione. Stabilire una nuova connessione è un'operazione costosa che si può evitare riutilizzando la stessa factory e gli stessi oggetti client per più operazioni. È possibile usare gli oggetti del client per operazioni asincrone simultanee e da più thread.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Gli oggetti client del bus di servizio, ad esempio `QueueClient` o `MessageSender` , vengono creati tramite un oggetto [MessagingFactory][MessagingFactory] , che fornisce anche la gestione interna delle connessioni. Si consiglia di non chiudere le factory di messaggistica, la coda, l'argomento o i client di sottoscrizione dopo l'invio di un messaggio e quindi ricrearli quando si invia il messaggio successivo. Quando si chiude una factory di messaggistica viene eliminata la connessione al servizio Bus di servizio e viene stabilita una nuova connessione quando si crea di nuovo la factory. Stabilire una nuova connessione è un'operazione costosa che si può evitare riutilizzando la stessa factory e gli stessi oggetti client per più operazioni. È possibile usare gli oggetti del client per operazioni asincrone simultanee e da più thread.

---

## <a name="concurrent-operations"></a>Operazioni simultanee
Operazioni quali invio, ricezione, eliminazione e così via derivano da qualche tempo. Questa volta include il tempo impiegato dal servizio del bus di servizio per elaborare l'operazione e la latenza della richiesta e della risposta. Per aumentare il numero di operazioni per volta, è necessario eseguire le operazioni contemporaneamente.

Il client pianifica le operazioni simultanee eseguendo operazioni **asincrone** . La richiesta successiva viene avviata prima del completamento della richiesta precedente. Il frammento di codice seguente è un esempio di operazione di invio in modalità asincrona:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Il codice seguente è un esempio di operazione di ricezione in modalità asincrona.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Per <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">esempi <span class="docon docon-navigate-external x-hidden-focus"></span> completi di codice sorgente </a>, vedere il repository GitHub:

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

`MessageReceiver`Viene creata un'istanza dell'oggetto con la stringa di connessione, il nome della coda e una modalità di ricezione Peek-look. Successivamente, l' `receiver` istanza viene utilizzata per registrare il gestore di messaggi.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Per <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">esempi <span class="docon docon-navigate-external x-hidden-focus"></span> completi di codice sorgente </a>, vedere il repository GitHub:

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

`MessagingFactory`Tramite viene creato un `factory` oggetto dalla stringa di connessione. Con l' `factory` istanza viene creata un'istanza di un oggetto `MessageReceiver` . Successivamente, l' `receiver` istanza viene utilizzata per registrare il gestore di messaggi.

---

## <a name="receive-mode"></a>Modalità di ricezione

Quando si crea un client di coda o sottoscrizione, è possibile specificare una modalità di ricezione: *PeekLock* o *ReceiveAndDelete*. La modalità di ricezione predefinita è `PeekLock`. Quando si opera in modalità predefinita, il client invia una richiesta di ricezione di un messaggio dal bus di servizio. Una volta che il client ha ricevuto il messaggio, invia una richiesta per il completamento del messaggio.

Quando si imposta la modalità di ricezione su `ReceiveAndDelete`, entrambi i passaggi vengono combinati in un'unica richiesta. Questi passaggi consentono di ridurre il numero complessivo di operazioni e di migliorare la velocità effettiva dei messaggi. Questo miglioramento delle prestazioni tuttavia comporta il rischio di perdere alcuni messaggi.

Il bus di servizio non supporta le transazioni per le operazioni di ricezione ed eliminazione. Inoltre, la semantica del blocco di visualizzazione è obbligatoria per qualsiasi scenario in cui il client desideri rinviare o [recapitare](service-bus-dead-letter-queues.md) un messaggio.

## <a name="client-side-batching"></a>Invio in batch sul lato client

L'invio in batch sul lato client consente a un client di coda o argomento di ritardare l'invio di un messaggio per un determinato periodo di tempo. Se il client invia messaggi aggiuntivi durante questo periodo di tempo, trasmette i messaggi in un singolo batch. L'invio in batch sul lato client prevede anche che il client di coda o la sottoscrizione invii in batch più richieste di tipo **Complete** in una singola richiesta. L'invio in batch è disponibile solo per operazioni di **invio** e **completamento** asincrone. Le operazioni sincrone vengono immediatamente inviate al servizio Bus di servizio. La suddivisione in batch non si verifica per le operazioni di visualizzazione o ricezione, né l'invio in batch tra i client.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

La funzionalità di invio in batch per l'SDK .NET Standard, non espone ancora una proprietà da modificare.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Per impostazione predefinita, i client usano un intervallo di invio in batch di 20 ms. È possibile modificare l'intervallo di invio in batch impostando la proprietà [BatchFlushInterval][BatchFlushInterval] prima di creare la factory di messaggistica. Questa impostazione interessa tutti i client creati da questa factory.

Per disabilitare l'invio in batch, impostare la proprietà [BatchFlushInterval][BatchFlushInterval] su **TimeSpan.Zero**. Ad esempio:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

L'invio in batch non influisce sul numero di operazioni di messaggistica fatturabili ed è disponibile solo per il protocollo client del bus di servizio tramite la libreria [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . Il protocollo HTTP non supporta l'invio in batch.

> [!NOTE]
> `BatchFlushInterval`L'impostazione garantisce che il batch sia implicito dal punto di vista dell'applicazione. i.e. l'applicazione effettua `SendAsync` e `CompleteAsync` chiama e non effettua chiamate batch specifiche.
>
> L'invio in batch sul lato client esplicito può essere implementato usando la chiamata al metodo seguente:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> In questo caso la dimensione combinata dei messaggi deve essere inferiore alla dimensione massima supportata dal piano tariffario.

---

## <a name="batching-store-access"></a>Invio in batch per l'accesso all'archivio

Per aumentare la velocità effettiva di una coda, un argomento o una sottoscrizione, il bus di servizio invia in batch più messaggi quando esegue la scrittura nel proprio archivio interno. 

- Quando si Abilita l'invio in batch in una coda, la scrittura di messaggi nell'archivio e l'eliminazione di messaggi dall'archivio verranno raggruppati in batch. 
- Quando si Abilita la suddivisione in batch in un argomento, la scrittura di messaggi nell'archivio viene suddivisa in batch. 
- Quando si Abilita l'invio in batch per una sottoscrizione, l'eliminazione di messaggi dall'archivio viene in batch. 
- Quando l'accesso in batch all'archivio è abilitato per un'entità, il bus di servizio ritarda un'operazione di scrittura dell'archivio per tale entità fino a 20 ms.

> [!NOTE]
> Non vi è alcun rischio di perdita dei messaggi con l'invio in batch, anche se si verifica un errore del bus di servizio alla fine di un intervallo di invio in batch di 20 ms.

Le operazioni di scrittura aggiuntive che si verificano durante questo intervallo vengono aggiunte al batch. L'accesso in batch all'archivio influiscono solo sulle operazioni di **invio** e **completamento** ; le operazioni di ricezione non sono interessate. L'accesso in batch all'archivio è una proprietà di un'entità. L'invio in batch si verifica per tutte le entità per cui è abilitato l'accesso in batch all'archivio.

Quando si crea una nuova coda, un nuovo argomento o una nuova sottoscrizione, l'accesso in batch all'archivio è abilitato per impostazione predefinita.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Per disabilitare l'accesso in batch all'archivio, è necessaria un'istanza di `ManagementClient` . Creare una coda da una descrizione della coda che imposta la `EnableBatchedOperations` proprietà su `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Per altre informazioni, vedere gli articoli seguenti:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Per disabilitare l'accesso in batch all'archivio, è necessaria un'istanza di `NamespaceManager` . Creare una coda da una descrizione della coda che imposta la `EnableBatchedOperations` proprietà su `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Per altre informazioni, vedere gli articoli seguenti:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

L'accesso in batch all'archivio non influisce sul numero di operazioni di messaggistica fatturabili. Si tratta di una proprietà di una coda, un argomento o una sottoscrizione. È indipendente dalla modalità di ricezione e dal protocollo usato tra un client e il servizio del bus di servizio.

## <a name="prefetching"></a>Prelettura

La [prelettura](service-bus-prefetch.md) consente al client di coda o sottoscrizione di caricare messaggi aggiuntivi dal servizio quando riceve i messaggi. Il client archivia i messaggi in una cache locale. Le dimensioni della cache sono determinate dalle `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` proprietà o. Ogni client che abilita la prelettura mantiene la propria cache. Una cache non è condivisa tra i client. Se il client avvia un'operazione di ricezione e la relativa cache è vuota, il servizio trasmette un batch di messaggi. Le dimensioni del batch corrispondono alle dimensioni della cache o a 256 KB, a seconda del valore minore. Se il client avvia un'operazione di ricezione e la cache contiene un messaggio, il messaggio viene ricavato dalla cache.

Quando un messaggio viene sottoposto a prelettura, il servizio lo blocca. Con il blocco, il messaggio prerecuperato non può essere ricevuto da un ricevitore diverso. Se il ricevitore non è in grado di completare il messaggio prima della scadenza del blocco, il messaggio diventa disponibile per altri ricevitori. La copia del messaggio sottoposta a prelettura resta nella cache. Il ricevitore che usa la copia scaduta memorizzata nella cache riceve un'eccezione quando prova a completare il messaggio. Per impostazione predefinita, il blocco del messaggio scade dopo 60 secondi. Questo valore può essere esteso a 5 minuti. Per evitare il consumo di messaggi scaduti, impostare la dimensione della cache inferiore al numero di messaggi che un client può utilizzare entro l'intervallo di timeout del blocco.

Se si usa l'impostazione predefinita di 60 secondi per la scadenza del blocco, è consigliabile impostare `PrefetchCount` su un valore pari a 20 volte la velocità massima di elaborazione di tutti i ricevitori della factory. Ad esempio, una factory crea tre ricevitori e ogni ricevitore può elaborare al massimo 10 messaggi al secondo. Il conteggio prelettura non deve superare 20 X 3 X 10 = 600. Per impostazione predefinita, `PrefetchCount` è impostato su 0, il che significa che non vengono recuperati altri messaggi dal servizio.

La prelettura dei messaggi comporta un aumento della velocità effettiva globale per una coda o una sottoscrizione perché riduce il numero complessivo di operazioni sui messaggi, o round trip. Il recupero del primo messaggio, tuttavia, può richiedere più tempo (a causa della dimensione aumentata dei messaggi). La ricezione di messaggi prelettura dalla cache sarà più rapida perché questi messaggi sono già stati scaricati dal client.

La proprietà di durata (TTL) di un messaggio viene controllata dal server nel momento in cui invia il messaggio al client. Il client non controlla la proprietà TTL del messaggio quando viene ricevuto il messaggio. Al contrario, il messaggio può essere ricevuto anche se la durata (TTL) del messaggio è passata mentre il messaggio è stato memorizzato nella cache dal client.

La prelettura non influisce sul numero di operazioni di messaggistica fatturabili ed è disponibile solo per il protocollo client del bus di servizio. Il protocollo HTTP non supporta la prelettura. Questa funzionalità è disponibile per le operazioni di ricezione sincrone e asincrone.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Per ulteriori informazioni, vedere le `PrefetchCount` proprietà seguenti:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Per ulteriori informazioni, vedere le `PrefetchCount` proprietà seguenti:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Prelettura e ReceiveBatch

> [!NOTE]
> Questa sezione si applica solo a WindowsAzure. ServiceBus SDK, perché Microsoft. Azure. ServiceBus SDK non espone le funzioni batch.

Sebbene i concetti relativi alla prelettura di più messaggi abbiano una semantica simile all'elaborazione dei messaggi in un batch ( `ReceiveBatch` ), è necessario tenere presenti alcune piccole differenze quando si utilizzano questi approcci insieme.

La prelettura è una configurazione (o modalità) nel client ( `QueueClient` e `SubscriptionClient` ) ed `ReceiveBatch` è un'operazione (con semantica di richiesta-risposta).

Quando si usano questi approcci insieme, prendere in considerazione i casi seguenti:

* La prelettura deve essere maggiore o uguale al numero di messaggi che si prevede di ricevere `ReceiveBatch` .
* La prelettura può essere fino a n/3 volte il numero di messaggi elaborati al secondo, dove n è la durata predefinita del blocco.

Ci sono alcune difficoltà nell'avere un approccio greedy, ovvero mantenendo il conteggio prelettura elevato, perché implica che il messaggio è bloccato a un destinatario specifico. Si consiglia di provare a eseguire la prelettura dei valori tra le soglie indicate in precedenza e identificare in modo empirico l'elemento appropriato.

## <a name="multiple-queues"></a>Più code

Se una singola coda o un argomento non è in grado di gestire il previsto, usare più entità di messaggistica. Se si usano più entità, è consigliabile creare un client dedicato per ogni entità invece di usare lo stesso client per tutte.

## <a name="development-and-testing-features"></a>Funzionalità di sviluppo e test

> [!NOTE]
> Questa sezione si applica solo a WindowsAzure. ServiceBus SDK, perché Microsoft. Azure. ServiceBus SDK non espone questa funzionalità.

Il bus di servizio dispone di una funzionalità, usata specificamente per lo sviluppo, che **non deve mai essere usata nelle configurazioni di produzione** : [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] .

Quando vengono aggiunti nuovi filtri o regole all'argomento, è possibile utilizzare [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] per verificare che la nuova espressione di filtro funzioni come previsto.

## <a name="scenarios"></a>Scenari

Le sezioni seguenti descrivono scenari di messaggistica tipici e indicano le impostazioni preferibili del bus di servizio. La velocità effettiva è classificata come bassa (minore di 1 messaggio al secondo), moderata (maggiore o uguale a 1 messaggio al secondo ma minore di 100 messaggi al secondo) ed elevata (maggiore o uguale a 100 messaggi al secondo). Il numero di client è classificato come basso (minore o uguale a 5), moderato (maggiore di 5 ma minore o uguale a 20) ed elevato (maggiore di 20).

### <a name="high-throughput-queue"></a>Coda ad alta velocità effettiva

Obiettivo: aumentare la velocità effettiva di una singola coda. Il numero di mittenti e ricevitori è limitato.

* Per aumentare la velocità di invio globale nella coda, usare più factory di messaggistica per la creazione di mittenti. Per ogni mittente usare operazioni asincrone o più thread.
* Per aumentare la velocità di ricezione globale dalla coda, usare più factory di messaggistica per la creazione di ricevitori.
* Usare operazioni asincrone per sfruttare i vantaggi dell'invio in batch sul lato client.
* Impostare l'intervallo di invio in batch su 50 ms per ridurre il numero di trasmissioni tramite il protocollo client del bus di servizio. Se vengono usati più mittenti, aumentare l'intervallo di invio in batch impostandolo su 100 ms.
* Lasciare abilitato l'accesso in batch all'archivio. Questo accesso aumenta la velocità complessiva per la scrittura dei messaggi nella coda.
* Impostare il conteggio prelettura su un valore pari a 20 volte la velocità di elaborazione massima di tutti i ricevitori di una factory. Questo conteggio riduce il numero di trasmissioni tramite il protocollo client del bus di servizio.

### <a name="multiple-high-throughput-queues"></a>Code multiple ad alta velocità effettiva

Obiettivo: aumentare la velocità effettiva complessiva di più code. La velocità effettiva di una singola coda è moderata o elevata.

Per ottenere la velocità effettiva massima su più code, usare le impostazioni descritte per aumentare la velocità effettiva di una singola coda. Usare inoltre Factory diverse per creare client che inviano o ricevono da code diverse.

### <a name="low-latency-queue"></a>Coda a bassa latenza

Obiettivo: ridurre al minimo la latenza di una coda o di un argomento. Il numero di mittenti e ricevitori è limitato. La velocità effettiva della coda è ridotta o moderata.

* Disabilitare l'invio in batch sul lato client. Il client invia immediatamente un messaggio.
* Disabilitare l'accesso in batch all'archivio. Il servizio scrive immediatamente il messaggio nell'archivio.
* Se si usa un singolo client, impostare il conteggio prelettura su un valore pari a 20 volte la velocità di elaborazione del ricevitore. Se più messaggi arrivano nella coda allo stesso tempo, il protocollo client del bus di servizio li trasmette tutti contemporaneamente. Quando il client riceve il messaggio successivo, questo è già presente nella cache locale. La cache deve essere di dimensioni ridotte.
* Se si usano più client, impostare il conteggio prelettura su 0. Impostando il conteggio, il secondo client può ricevere il secondo messaggio mentre il primo client sta ancora elaborando il primo.

### <a name="queue-with-a-large-number-of-senders"></a>Coda con un numero elevato di mittenti

Obiettivo: aumentare la velocità effettiva di una coda o di un argomento con un numero elevato di mittenti. Ogni mittente invia messaggi con una velocità moderata. Il numero di ricevitori è limitato.

Il bus di servizio consente fino a 1000 connessioni simultanee a un'entità di messaggistica. Questo limite viene applicato a livello di spazio dei nomi e le code, gli argomenti o le sottoscrizioni sono limitati dal limite di connessioni simultanee per ogni spazio dei nomi. Per le code, questo numero è condiviso tra mittenti e ricevitori. Se sono necessarie tutte e 1000 le connessioni per i mittenti, sostituire la coda con un argomento e una singola sottoscrizione. Un argomento accetta fino a 1000 connessioni simultanee dai mittenti. La sottoscrizione accetta altre 1000 connessioni simultanee dai ricevitori. Se sono necessari più di 1000 mittenti simultanei, i mittenti devono inviare i messaggi al protocollo del bus di servizio tramite HTTP.

Per ottimizzare la velocità effettiva, attenersi alla procedura seguente:

* Se ogni mittente si trova in un processo diverso, usare solo una singola factory per processo.
* Usare operazioni asincrone per sfruttare i vantaggi dell'invio in batch sul lato client.
* Usare l'intervallo di invio in batch predefinito di 20 ms per ridurre il numero di trasmissioni tramite il protocollo client del bus di servizio.
* Lasciare abilitato l'accesso in batch all'archivio. Questo accesso aumenta la velocità complessiva per la scrittura dei messaggi nella coda o nell'argomento.
* Impostare il conteggio prelettura su un valore pari a 20 volte la velocità di elaborazione massima di tutti i ricevitori di una factory. Questo conteggio riduce il numero di trasmissioni tramite il protocollo client del bus di servizio.

### <a name="queue-with-a-large-number-of-receivers"></a>Coda con un numero elevato di ricevitori

Obiettivo: aumentare la velocità di ricezione di una coda o di una sottoscrizione con un numero elevato di ricevitori. Ogni ricevitore riceve messaggi a una velocità moderata. Il numero di mittenti è limitato.

Il bus di servizio consente un massimo di 1000 connessioni simultanee a un'entità. Se una coda richiede più di 1000 ricevitori, sostituirla con un argomento e più sottoscrizioni. Ogni sottoscrizione può supportare fino a 1000 connessioni simultanee. In alternativa, i ricevitori possono accedere alla coda tramite il protocollo HTTP.

Per ottimizzare la velocità effettiva, attenersi alle seguenti linee guida:

* Se ogni ricevitore si trova in un processo diverso, usare solo una singola factory per processo.
* I ricevitori possono usare operazioni sincrone o asincrone. Data la frequenza di ricezione moderata di un singolo ricevitore, l'invio in batch sul lato client di una richiesta completa non influisce sulla velocità effettiva del ricevitore.
* Lasciare abilitato l'accesso in batch all'archivio. Questo accesso riduce il carico complessivo dell'entità. Si riduce anche la velocità globale per la scrittura dei messaggi nella coda o nell'argomento.
* Impostare il conteggio prelettura su un valore ridotto, ad esempio PrefetchCount = 10. Questo conteggio evita che i ricevitori rimangano inattivi mentre per altri è presente un numero elevato di messaggi memorizzati nella cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Argomento con un numero limitato di sottoscrizioni

Obiettivo: aumentare la velocità effettiva di un argomento con un numero limitato di sottoscrizioni. Poiché un messaggio viene ricevuto da molte sottoscrizioni, la velocità di ricezione combinata su tutte le sottoscrizioni è superiore alla velocità di invio. Il numero di mittenti è limitato. Il numero di ricevitori per sottoscrizione è limitato.

Per ottimizzare la velocità effettiva, attenersi alle seguenti linee guida:

* Per aumentare la velocità di invio globale nell'argomento, usare più factory di messaggistica per la creazione di mittenti. Per ogni mittente usare operazioni asincrone o più thread.
* Per aumentare la velocità di ricezione globale di una sottoscrizione, usare più factory di messaggistica per la creazione di ricevitori. Per ogni ricevitore usare operazioni asincrone o più thread.
* Usare operazioni asincrone per sfruttare i vantaggi dell'invio in batch sul lato client.
* Usare l'intervallo di invio in batch predefinito di 20 ms per ridurre il numero di trasmissioni tramite il protocollo client del bus di servizio.
* Lasciare abilitato l'accesso in batch all'archivio. Questo accesso aumenta la velocità complessiva per la scrittura dei messaggi nell'argomento.
* Impostare il conteggio prelettura su un valore pari a 20 volte la velocità di elaborazione massima di tutti i ricevitori di una factory. Questo conteggio riduce il numero di trasmissioni tramite il protocollo client del bus di servizio.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Argomento con un numero elevato di sottoscrizioni

Obiettivo: aumentare la velocità effettiva di un argomento con un numero elevato di sottoscrizioni. Poiché un messaggio viene ricevuto da molte sottoscrizioni, la velocità di ricezione combinata su tutte le sottoscrizioni è superiore rispetto alla velocità di invio. Il numero di mittenti è limitato. Il numero di ricevitori per sottoscrizione è limitato.

Gli argomenti con un numero elevato di sottoscrizioni in genere espongono una velocità effettiva globale ridotta se tutti i messaggi vengono instradati a tutte le sottoscrizioni. Il motivo è che ogni messaggio viene ricevuto più volte e tutti i messaggi in un argomento e tutte le relative sottoscrizioni vengono archiviati nello stesso archivio. Il presupposto è che il numero di mittenti e il numero di ricevitori per sottoscrizione è ridotto. Il bus di servizio supporta fino a 2.000 sottoscrizioni per argomento.

Per ottimizzare la velocità effettiva, procedere come segue:

* Usare operazioni asincrone per sfruttare i vantaggi dell'invio in batch sul lato client.
* Usare l'intervallo di invio in batch predefinito di 20 ms per ridurre il numero di trasmissioni tramite il protocollo client del bus di servizio.
* Lasciare abilitato l'accesso in batch all'archivio. Questo accesso aumenta la velocità complessiva per la scrittura dei messaggi nell'argomento.
* Impostare il conteggio prelettura su un valore pari a 20 volte la velocità di ricezione prevista in secondi. Questo conteggio riduce il numero di trasmissioni tramite il protocollo client del bus di servizio.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md