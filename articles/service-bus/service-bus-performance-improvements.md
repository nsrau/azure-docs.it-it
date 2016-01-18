<properties 
   pageTitle="Procedure consigliate per ottimizzare le prestazioni tramite il bus di servizio | Microsoft Azure"
   description="Descrive come usare il bus di servizio di Azure per ottimizzare le prestazioni durante gli scambi di messaggi negoziati."
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

# Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio

Questo argomento illustra come usare il bus di servizio di Azure per ottimizzare le informazioni durante lo scambio di messaggi negoziati. La prima parte di questo argomento descrive i vari meccanismi disponibili per aumentare le prestazioni. La seconda parte fornisce invece indicazioni su come usare il bus di servizio per garantire le prestazioni ottimali in uno scenario specifico.

In questo argomento il termine "client" fa riferimento a qualsiasi entità che accede al bus di servizio. Un client può assumere il ruolo di mittente o di ricevitore. Il termine "mittente" viene usato per un client di coda o argomento del bus di servizio che invia messaggi a una coda o a un argomento del bus di servizio. Il termine "ricevitore" da riferimento a un client di coda o sottoscrizione del bus di servizio che riceve messaggi da una coda o da una sottoscrizione del bus di servizio.

Queste sezioni presentano numerosi concetti usati dal bus di servizio per migliorare le prestazioni.

## Protocolli

Il bus di servizio consente ai client di inviare e ricevere messaggi tramite due protocolli: il protocollo client del bus di servizio e il protocollo HTTP (REST). Il protocollo client del bus di servizio è più efficiente, perché mantiene la connessione al servizio Bus di servizio purché esista la factory di messaggistica. Implementa anche le operazioni di invio in batch e prelettura. Il protocollo client del bus di servizio è disponibile per le applicazioni .NET tramite le API .NET.

Se non indicato in modo esplicito, il contenuto di questo argomento presuppone l'uso del protocollo client del bus di servizio.

## Riutilizzo di factory e client

Gli oggetti client del bus di servizio, ad esempio [QueueClient][] o [MessageSender][], vengono creati tramite un oggetto [MessagingFactory][] che fornisce anche la gestione interna delle connessioni. È consigliabile non chiudere le factory di messaggistica o i client di coda, argomento e sottoscrizione dopo aver inviato un messaggio e crearli di nuovo per l'invio del messaggio successivo. Quando si chiude una factory di messaggistica viene eliminata la connessione al servizio Bus di servizio e viene stabilita una nuova connessione quando si crea di nuovo la factory. Stabilire una nuova connessione è un'operazione costosa che si può evitare riutilizzando la stessa factory e gli stessi oggetti client per più operazioni.

## Operazioni simultanee

L'esecuzione di un'operazione (invio, ricezione, eliminazione e così via) richiede tempo. Questa volta include l'elaborazione dell'operazione dal servizio Bus di servizio oltre alla latenza della richiesta e risposta. Per aumentare il numero di operazioni per volta, è necessario eseguire le operazioni contemporaneamente. È possibile farlo in diversi modi:

-   **Operazioni asincrone**: il client pianifica le operazioni eseguendo operazioni asincrone. La richiesta successiva viene avviata prima del completamento della richiesta precedente. Ecco un esempio di operazione di invio in modalità asincrona:

	```
	BrokeredMessage m1 = new BrokeredMessage(body);
	BrokeredMessage m2 = new BrokeredMessage(body);
	
	Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
	  {
	    Console.WriteLine("Sent message #1");
	  });
	Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
	  {
	    Console.WriteLine("Sent message #2");
	  });
	Task.WaitAll(send1, send2);
	Console.WriteLine("All messages sent");
	```

	Ecco un esempio di operazione di ricezione in modalità asincrona:
	
	```
	Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
	Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
	
	Task.WaitAll(receive1, receive2);
	Console.WriteLine("All messages received");
	
	async void ProcessReceivedMessage(Task<BrokeredMessage> t)
	{
	  BrokeredMessage m = t.Result;
	  Console.WriteLine("{0} received", m.Label);
	  await m.CompleteAsync();
	  Console.WriteLine("{0} complete", m.Label);
	}
	```

-   **Più factory**: tutti i client (mittenti e ricevitori) creati dalla stessa factory condividono la stessa connessione TCP. La velocità effettiva massima dei messaggi è limitata dal numero di operazioni che possono usare questa connessione TCP. La velocità effettiva che si può ottenere con una singola factory varia in modo significativo a seconda dei tempi di round trip TCP e delle dimensioni dei messaggi. Per ottenere una velocità effettiva più elevata, è consigliare usare più factory di messaggistica.

## Modalità di ricezione

Quando si crea un client di coda o sottoscrizione, è possibile specificare una modalità di ricezione: *PeekLock* o *ReceiveAndDelete*. La modalità di ricezione predefinita è [PeekLock][]. Quando si opera in questa modalità, il client invia una richiesta per la ricezione di un messaggio dal bus di servizio. Una volta che il client ha ricevuto il messaggio, invia una richiesta per il completamento del messaggio.

Quando la modalità di ricezione è impostata su [ReceiveAndDelete][], entrambi i passaggi vengono combinati in una singola richiesta. Questo permette di ridurre il numero complessivo di operazioni e di migliorare la velocità effettiva dei messaggi. Questo miglioramento delle prestazioni tuttavia comporta il rischio di perdere alcuni messaggi.

Il bus di servizio non supporta le transazioni per le operazioni receive-and-delete. Inoltre, è necessaria la semantica peek-lock (blocco anteprima) per qualsiasi scenario in cui il client voglia rinviare un messaggio o inserirlo nella coda dei messaggi non recapitabili.

## Invio in batch sul lato client

L'invio in batch sul lato client consente a un client di coda o argomento di ritardare l'invio di un messaggio per un determinato periodo di tempo. Se il client invia messaggi aggiuntivi durante questo periodo di tempo, trasmette i messaggi in un singolo batch. L'invio in batch sul lato client prevede anche che il client di coda/sottoscrizione invii in batch più richieste di tipo **Complete** in una singola richiesta. L'invio in batch è disponibile solo per le operazioni **Send** e **Complete** asincrone. Le operazioni sincrone vengono immediatamente inviate al servizio Bus di servizio. L'invio in batch non si verifica per le operazioni di anteprima (peek) o ricezione né tra più client.

Se il batch supera le dimensioni massime consentite per i messaggi, l'ultimo messaggio viene rimosso dal batch e i client invia immediatamente il batch. L'ultimo messaggio diventa quindi il primo messaggio del batch successivo. Per impostazione predefinita, i client usano un intervallo di invio in batch di 20 ms. È possibile modificare l'intervallo di invio in batch impostando la proprietà [BatchFlushInterval][] prima di creare la factory di messaggistica. Questa impostazione interessa tutti i client creati da questa factory.

Per disabilitare l'invio in batch, impostare la proprietà [BatchFlushInterval][] su **TimeSpan.Zero**. Ad esempio:

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

L'invio in batch non influisce sul numero di operazioni di messaggistica fatturabili ed è disponibile solo per il protocollo client del bus di servizio. Il protocollo HTTP non supporta l'invio in batch.

## Invio in batch per l'accesso all'archivio

Per aumentare la velocità effettiva di una coda, un argomento o una sottoscrizione, il bus di servizio invia in batch più messaggi quando esegue la scrittura nel proprio archivio interno. Se abilitata in una coda o in un argomento, la scrittura di messaggi nell'archivio verrà eseguita in batch. Se abilitata in una coda o in una sottoscrizione, l'eliminazione di messaggi dall'archivio verrà eseguita in batch. Se l'accesso in batch all'archivio è abilitato per un'entità, il bus di servizio ritarda l'operazione di scrittura nell'archivio relativa all'entità per un massimo di 20 ms. Le operazioni di scrittura aggiuntive che si verificano durante questo intervallo vengono aggiunte al batch. L'accesso in batch all'archivio influisce solo sulle operazioni **Send** e **Complete** e non sulle operazioni di ricezione. L'accesso in batch all'archivio è una proprietà di un'entità. L'invio in batch si verifica per tutte le entità per cui è abilitato l'accesso in batch all'archivio.

Quando si crea una nuova coda, un nuovo argomento o una nuova sottoscrizione, l'accesso in batch all'archivio è abilitato per impostazione predefinita. Per disabilitare l'accesso in batch all'archivio, impostare la proprietà [EnableBatchedOperations][] su **false** prima di creare l'entità. Ad esempio:

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

L'accesso in batch all'archivio non influisce sul numero di operazioni di messaggistica fatturabili ed è una proprietà di una coda, di un argomento o di una sottoscrizione. È indipendente dalla modalità di ricezione e dal protocollo usato tra un client e il servizio Bus di servizio.

## Prelettura

La prelettura consente al client di coda o sottoscrizione di caricare messaggi aggiuntivi dal servizio durante l'esecuzione di un'operazione di ricezione. Il client archivia i messaggi in una cache locale. Le dimensioni della cache sono determinate dalle proprietà [QueueClient.PrefetchCount][] e [SubscriptionClient.PrefetchCount][]. Ogni client che abilita la prelettura mantiene la propria cache. La cache non è condivisa tra i client. Se il client avvia un'operazione di ricezione e la relativa cache è vuota, il servizio trasmette un batch di messaggi. Le dimensioni del batch corrispondono alle dimensioni della cache o a 256 KB, a seconda del valore minore. Se il client avvia un'operazione di ricezione e la cache contiene un messaggio, il messaggio viene recuperato dalla cache.

Quando un messaggio viene sottoposto a prelettura, il servizio lo blocca. In questo modo, il messaggio non potrà essere ricevuto da un ricevitore diverso. Se il ricevitore non può completare il messaggio prima della scadenza del blocco, il messaggio diventa disponibile per altri ricevitori. La copia del messaggio sottoposta a prelettura resta nella cache. Il ricevitore che usa la copia scaduta memorizzata nella cache riceve un'eccezione quando prova a completare il messaggio. Per impostazione predefinita, il blocco del messaggio scade dopo 60 secondi. Questo valore può essere esteso a 5 minuti. Per evitare che vengano usati messaggi scaduti, la dimensione della cache dovrebbe essere sempre inferiore al numero di messaggi che possono essere usati da un client nell'intervallo di timeout del blocco.

Se si usa l'impostazione predefinita di 60 secondi per la scadenza del blocco, è consigliabile impostare [SubscriptionClient.PrefetchCount][] su un valore pari a 20 volte la velocità massima di elaborazione di tutti i ricevitori della factory. Ad esempio, una factory crea tre ricevitori e ogni ricevitore può elaborare al massimo 10 messaggi al secondo. Il conteggio prelettura non deve superare 20*3*10 = 600. Per impostazione predefinita, la proprietà [QueueClient.PrefetchCount][] è impostata su 0, a indicare che non vengono recuperati altri messaggi dal servizio.

La prelettura dei messaggi comporta un aumento della velocità effettiva globale per una coda o una sottoscrizione perché riduce il numero complessivo di operazioni sui messaggi, o round trip. Il recupero del primo messaggio tuttavia richiede più tempo (a causa della dimensione del messaggio aumentata). La ricezione di messaggi sottoposti a prelettura sarà più rapida perché questi messaggi sono stati già scaricati dal client.

La proprietà di durata (TTL) di un messaggio viene controllata dal server nel momento in cui invia il messaggio al client. Il client non controlla la proprietà di durata (TTL) del messaggio al momento della ricezione. Il messaggio può essere ricevuto anche se la relativa durata (TTL) scade durante la memorizzazione nella cache da parte del client.

La prelettura non influisce sul numero di operazioni di messaggistica fatturabili ed è disponibile solo per il protocollo client del bus di servizio. Il protocollo HTTP non supporta la prelettura. Questa funzionalità è disponibile per le operazioni di ricezione sincrone e asincrone.

## Code e argomenti rapidi

Le entità rapide consentono scenari che prevedono velocità effettiva elevata e latenza minima. Quando si usano le entità rapide, un messaggio inviato a una coda o a un argomento non viene archiviato immediatamente nell'archivio di messaggistica, ma viene memorizzato nella cache. Se un messaggio resta nella coda per più di alcuni secondi, viene automaticamente scritto in un'archiviazione stabile, proteggendolo così da eventuali perdite a causa di un'interruzione. La scrittura di un messaggio in una cache consente di incrementare la velocità effettiva e di ridurre la latenza, perché al momento dell'invio del messaggio non si verificano tentativi di accesso all'archiviazione stabile. I messaggi usati nell'arco di pochi secondi non vengono inseriti nell'archivio di messaggistica. L'esempio seguente illustra la creazione di un argomento rapido.

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Se un messaggio contenente informazioni critiche che non devono andare perdute viene inviato a un'entità rapida, il mittente può forzare il bus di servizio perché mantenga subito e in modo permanente il messaggio nell'archivio stabile impostando la proprietà [ForcePersistence][] su **true**.

## Uso di code o argomenti partizionati

Internamente, il bus di servizio usa lo stesso nodo e lo stesso archivio di messaggistica per elaborare e archiviare tutti i messaggi per un'entità di messaggistica (coda o argomento). Una coda o un argomento partizionato, al contrario, viene distribuito tra più nodi e archivi di messaggistica. Le code e gli argomenti partizionati non solo registrano una velocità effettiva superiore rispetto a quella delle code e degli argomenti normali, ma presentano anche una maggiore disponibilità. Per creare un'entità partizionata, impostare la proprietà [EnablePartitioning][] su **true**, come illustrato nell'esempio seguente. Per altre informazioni sulle entità partizionate, vedere [Entità di messaggistica partizionate][].

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## Uso di più code

Se non è possibile usare una coda o un argomento partizionato o se il carico previsto non può essere gestito da una singola coda o argomento partizionato, è necessario usare più entità di messaggistica. Se si usano più entità, è consigliabile creare un client dedicato per ogni entità invece di usare lo stesso client per tutte.

## Scenari

Le sezioni seguenti descrivono scenari di messaggistica tipici e indicano le impostazioni preferibili del bus di servizio. La velocità effettiva è classificata come bassa (minore di 1 messaggio al secondo), moderata (maggiore o uguale a 1 messaggio al secondo ma minore di 100 messaggi al secondo) ed elevata (maggiore o uguale a 100 messaggi al secondo). Il numero di client è classificato come basso (minore o uguale a 5), moderato (maggiore di 5 ma minore o uguale a 20) ed elevato (maggiore di 20).

### Coda ad alta velocità effettiva

Obiettivo: aumentare la velocità effettiva di una singola coda. Il numero di mittenti e ricevitori è limitato.

-   Usare una coda partizionata per migliorare prestazioni e disponibilità.

-   Per aumentare la velocità di invio globale nella coda, usare più factory di messaggistica per la creazione di mittenti. Per ogni mittente usare operazioni asincrone o più thread.

-   Per aumentare la velocità di ricezione globale dalla coda, usare più factory di messaggistica per la creazione di ricevitori.

-   Usare operazioni asincrone per sfruttare i vantaggi dell'invio in batch sul lato client.

-   Impostare l'intervallo di invio in batch su 50 ms per ridurre il numero di trasmissioni tramite il protocollo client del bus di servizio. Se vengono usati più mittenti, aumentare l'intervallo di invio in batch impostandolo su 100 ms.

-   Lasciare abilitato l'accesso in batch all'archivio. In questo modo si aumenta la velocità complessiva per la scrittura dei messaggi nella coda.

-   Impostare il conteggio prelettura su un valore pari a 20 volte la velocità di elaborazione massima di tutti i ricevitori di una factory. Questo valore riduce il numero di trasmissioni tramite il protocollo client del bus di servizio.

### Code multiple ad alta velocità effettiva

Obiettivo: aumentare la velocità effettiva complessiva di più code. La velocità effettiva di una singola coda è moderata o elevata.

Per ottenere la velocità effettiva massima su più code, usare le impostazioni descritte per aumentare la velocità effettiva di una singola coda. Usare inoltre factory diverse per creare client che inviano o ricevono da code diverse.

### Coda a bassa latenza

Obiettivo: ridurre la latenza end-to-end di una coda o di un argomento. Il numero di mittenti e ricevitori è limitato. La velocità effettiva della coda è ridotta o moderata.

-   Usare una coda partizionata migliorare la disponibilità.

-   Disabilitare l'invio in batch sul lato client. Il client invia immediatamente un messaggio.

-   Disabilitare l'accesso in batch all'archivio. Il servizio scrive immediatamente il messaggio nell'archivio.

-   Se si usa un singolo client, impostare il conteggio prelettura su un valore pari a 20 volte la velocità di elaborazione del ricevitore. Se più messaggi arrivano nella coda allo stesso tempo, il protocollo client del bus di servizio li trasmette tutti contemporaneamente. Quando il client riceve il messaggio successivo, questo è già presente nella cache locale. La cache deve essere di dimensioni ridotte.

-   Se si usano più client, impostare il conteggio prelettura su 0. In questo modo, il secondo client può ricevere il secondo messaggio mentre il primo client sta ancora elaborando il primo messaggio.

### Coda con un numero elevato di mittenti

Obiettivo: aumentare la velocità effettiva di una coda o di un argomento con un numero elevato di mittenti. Ogni mittente invia messaggi con una velocità moderata. Il numero di ricevitori è limitato.

Il bus di servizio consente un massimo di 1000 connessioni simultanee a un'entità di messaggistica (o 5000 con AMQP). Questo limite viene applicato a livello di spazio dei nomi e le code, le sottoscrizioni o gli argomenti sono limitati in base al numero massimo di connessioni simultanee per spazio dei nomi. Per le code, questo numero è condiviso tra mittenti e ricevitori. Se sono necessarie tutte e 1000 le connessioni per i mittenti, è consigliabile sostituire la coda con un argomento e una singola sottoscrizione. Un argomento accetta fino a 1000 connessioni simultanee dai mittenti, mentre la sottoscrizione accetta altre 1000 connessioni simultanee dai ricevitori. Se sono necessari più di 1000 mittenti simultanei, i mittenti devono inviare i messaggi al protocollo del bus di servizio tramite HTTP.

Per ottimizzare la velocità effettiva, eseguire le operazioni seguenti:

-   Usare una coda partizionata per migliorare prestazioni e disponibilità.

-   Se ogni mittente si trova in un processo diverso, usare solo una singola factory per processo.

-   Usare operazioni asincrone per sfruttare i vantaggi dell'invio in batch sul lato client.

-   Usare l'intervallo di invio in batch predefinito di 20 ms per ridurre il numero di trasmissioni tramite il protocollo client del bus di servizio.

-   Lasciare abilitato l'accesso in batch all'archivio. In questo modo si aumenta la velocità complessiva per la scrittura dei messaggi nella coda o nell'argomento.

-   Impostare il conteggio prelettura su un valore pari a 20 volte la velocità di elaborazione massima di tutti i ricevitori di una factory. Questo valore riduce il numero di trasmissioni tramite il protocollo client del bus di servizio.

### Coda con un numero elevato di ricevitori

Obiettivo: aumentare la velocità di ricezione di una coda o di una sottoscrizione con un numero elevato di ricevitori. Ogni ricevitore riceve messaggi a una velocità moderata. Il numero di mittenti è limitato.

Il bus di servizio consente un massimo di 1000 connessioni simultanee a un'entità. Se una coda richiede più di 1000 ricevitori, è consigliabile sostituirla con un argomento e più sottoscrizioni. Ogni sottoscrizione può supportare fino a 1000 connessioni simultanee. In alternativa, i ricevitori possono accedere alla coda tramite il protocollo HTTP.

Per ottimizzare la velocità effettiva, eseguire le operazioni seguenti:

-   Usare una coda partizionata per migliorare prestazioni e disponibilità.

-   Se ogni ricevitore si trova in un processo diverso, usare solo una singola factory per processo.

-   I ricevitori possono usare operazioni sincrone o asincrone. Considerata la velocità di ricezione moderata di un singolo ricevitore, l'invio in batch sul lato client di una richiesta Complete non influisce sulla velocità effettiva del ricevitore.

-   Lasciare abilitato l'accesso in batch all'archivio. In questo modo si riduce il carico complessivo dell'entità. Si riduce anche la velocità globale per la scrittura dei messaggi nella coda o nell'argomento.

-   Impostare il conteggio prelettura su un valore ridotto, ad esempio PrefetchCount = 10. In questo modo si evita che i ricevitori rimangano inattivi mentre per altri è presente un numero elevato di messaggi memorizzati nella cache.

### Argomento con un numero limitato di sottoscrizioni

Obiettivo: aumentare la velocità effettiva di un argomento con un numero limitato di sottoscrizioni. Poiché un messaggio viene ricevuto da molte sottoscrizioni, la velocità di ricezione combinata su tutte le sottoscrizioni è superiore alla velocità di invio. Il numero di mittenti è limitato. Il numero di ricevitori per sottoscrizione è limitato.

Per ottimizzare la velocità effettiva, eseguire le operazioni seguenti:

-   Usare un argomento partizionato per migliorare prestazioni e disponibilità.

-   Per aumentare la velocità di invio globale nell'argomento, usare più factory di messaggistica per la creazione di mittenti. Per ogni mittente usare operazioni asincrone o più thread.

-   Per aumentare la velocità di ricezione globale di una sottoscrizione, usare più factory di messaggistica per la creazione di ricevitori. Per ogni ricevitore usare operazioni asincrone o più thread.

-   Usare operazioni asincrone per sfruttare i vantaggi dell'invio in batch sul lato client.

-   Usare l'intervallo di invio in batch predefinito di 20 ms per ridurre il numero di trasmissioni tramite il protocollo client del bus di servizio.

-   Lasciare abilitato l'accesso in batch all'archivio. In questo modo si aumenta la velocità globale per la scrittura dei messaggi nell'argomento.

-   Impostare il conteggio prelettura su un valore pari a 20 volte la velocità di elaborazione massima di tutti i ricevitori di una factory. Questo valore riduce il numero di trasmissioni tramite il protocollo client del bus di servizio.

### Argomento con un numero elevato di sottoscrizioni

Obiettivo: aumentare la velocità effettiva di un argomento con un numero elevato di sottoscrizioni. Poiché un messaggio viene ricevuto da molte sottoscrizioni, la velocità di ricezione combinata su tutte le sottoscrizioni è superiore rispetto alla velocità di invio. Il numero di mittenti è limitato. Il numero di ricevitori per sottoscrizione è limitato.

Gli argomenti con un numero elevato di sottoscrizioni in genere espongono una velocità effettiva globale ridotta se tutti i messaggi vengono instradati a tutte le sottoscrizioni. Questo dipende dal fatto che ogni messaggio viene ricevuto più volte e che tutti i messaggi contenuti in un argomento e tutte le relative sottoscrizioni vengono salvati nello stesso archivio. Si presuppone che il numero di mittenti e il numero di ricevitori per sottoscrizione sia ridotto. Il bus di servizio supporta fino a 2.000 sottoscrizioni per argomento.

Per ottimizzare la velocità effettiva, eseguire le operazioni seguenti:

-   Usare un argomento partizionato per migliorare prestazioni e disponibilità.

-   Usare operazioni asincrone per sfruttare i vantaggi dell'invio in batch sul lato client.

-   Usare l'intervallo di invio in batch predefinito di 20 ms per ridurre il numero di trasmissioni tramite il protocollo client del bus di servizio.

-   Lasciare abilitato l'accesso in batch all'archivio. In questo modo si aumenta la velocità globale per la scrittura dei messaggi nell'argomento.

-   Impostare il conteggio prelettura su un valore pari a 20 volte la velocità di ricezione prevista in secondi. Questo valore riduce il numero di trasmissioni tramite il protocollo client del bus di servizio.

## Passaggi successivi

Per altre informazioni sull'ottimizzazione delle prestazioni del bus di servizio, vedere [Entità di messaggistica partizionate][].

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
  [EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
  [QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
  [SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
  [ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
  [EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [Entità di messaggistica partizionate]: service-bus-partitioning.md
  

<!---HONumber=AcomDC_0107_2016-->