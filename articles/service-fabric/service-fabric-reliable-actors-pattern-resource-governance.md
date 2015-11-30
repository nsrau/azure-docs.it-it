<properties
   pageTitle="Modello di progettazione della governance delle risorse | Microsoft Azure"
   description="Modello di progettazione sull'uso dei Reliable Actors dell’infrastruttura di servizi per modellare un'applicazione su cui sia possibile applicare la scalabilità ma che usi risorse vincolate"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Modello di progettazione di Reliable Actors: governance delle risorse

Questo modello e i relativi scenari sono facilmente riconoscibili dagli sviluppatori (aziendali e non solo) che dispongono di risorse vincolate in locale o nel cloud, non immediatamente scalabili, e che desiderano inviare nel cloud dati e applicazioni su larga scala.

Nelle organizzazioni, queste risorse vincolate, ad esempio i database, vengono eseguite su hardware ad alta scalabilità verticale. Chiunque disponga di una lunga esperienza in azienda sa che si tratta di una situazione comune a livello locale. A livello di cloud, invece, questa situazione si verifica quando un servizio cloud tenta di superare il limite TCP di 64 KB per le connessioni tra un indirizzo e una tupla di porta o quando si tenta di connettersi a un database basato su cloud che limita il numero di connessioni simultanee.

In passato, questo veniva risolto in genere ricorrendo alla limitazione tramite middleware basato su messaggi o a pool personalizzati e meccanismi di facciata. Queste operazioni, tuttavia, risultavano molto complesse, soprattutto se era necessario scalare il livello intermedio mantenendo il numero corretto di connessioni. Operazioni fragili e complesse.

In realtà, come il modello Smart Cache, anche questo modello coinvolge più scenari e clienti che dispongono già di sistemi con risorse vincolate e devono creare sistemi in cui è necessario scalare orizzontalmente non solo i servizi ma anche il relativo stato in memoria, nonché lo stato persistente in un archivio stabile.

Il diagramma seguente illustra questo scenario:

![][1]

## Modellazione di scenari di cache con attori

L'accesso alle risorse viene modellato come un attore o più attori che svolgono la funzione di proxy (ovvero di connessione) in una risorsa o un gruppo di risorse. È quindi possibile gestire direttamente la risorsa tramite singoli attori oppure usare un attore di coordinamento che gestisce gli attori risorsa. Per rendere questo concetto più concreto, si simulerà la necessità di lavorare con un livello di archiviazione partizionato per motivi di prestazioni e scalabilità. La prima opzione è piuttosto semplice: è possibile usare una funzione statica per eseguire il mapping e risolvere gli attori in risorse a valle. Una funzione di questo tipo può restituire, ad esempio, una stringa di connessione con un determinato input. La modalità di implementazione di questa funzione è interamente a propria discrezione. Questo approccio, ovviamente, presenta anche degli svantaggi, ad esempio un'affinità statica che rende molto difficile il ripartizionamento delle risorse o il remapping di un attore in risorse. Di seguito è riportato un esempio molto semplice in cui si usa il modulo aritmetico per determinare il nome di database tramite l'ID utente e si fa riferimento all'area geografica per identificare il server di database.

## Esempio di codice per la governance delle risorse: risoluzione statica

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

Semplice ma non molto flessibile. È possibile, a questo punto, esaminare un approccio più utile e avanzato. È necessario, in primo luogo, modellare l'affinità tra risorse fisiche e attori. Questa operazione può essere eseguita tramite un attore denominato Resolver in grado di riconoscere il mapping tra utenti, partizioni logiche e risorse fisiche. Resolver mantiene i dati in un archivio permanente ma, per agevolarne la ricerca, li trasferisce anche nella cache. Come illustrato nell'esempio del tasso di cambio relativo al modello Smart Cache, Resolver è in grado di recuperare dinamicamente le informazioni più recenti mediante un timer. Dopo aver risolto le risorse che deve usare, l'attore utente le memorizza nella cache in una variabile locale denominata \_resolution e le usa per tutta la propria durata. È stata preferita una risoluzione basata su ricerca (illustrata di seguito) rispetto all'hashing semplice o di intervallo per la flessibilità che è in grado di offrire a livello di scalabilità e di spostamento di un utente da una risorsa a un'altra.

![][2]

Nella figura sopra riportata, l'attore B23 prima risolve la risorsa DB1 (la cosiddetta "risoluzione"), quindi la memorizza nella cache. Nelle operazioni successive sarà quindi possibile usare la risoluzione memorizzata nella cache per accedere alla risorsa vincolata. Poiché gli attori supportano l'esecuzione a thread singolo, inoltre, gli sviluppatori non dovranno più preoccuparsi dei possibili accessi simultanei alla risorsa. Gli utenti User e Resolver avranno il seguente aspetto:

## Esempio di codice per la governance delle risorse: Resolver

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : StatefulActor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

Governance delle risorse: esempio di Resolver

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : StatefulActor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## Accesso alle risorse con capacità limitata

Si esaminerà ora un altro esempio: l'accesso esclusivo a risorse preziose, quali database, account di archiviazione e file system, con funzionalità a velocità effettiva limitata. In questo scenario si desidera elaborare gli eventi usando un attore denominato EventProcessor, responsabile di elaborare l'evento e di salvarlo in modo permanente, in questo caso in un file con estensione csv per maggiore semplicità. Sebbene sia possibile scalare orizzontalmente le risorse seguendo l'approccio di partizionamento illustrato in precedenza, rimane comunque la necessità di affrontare i problemi di concorrenza. Ecco perché è stato scelto un esempio basato su file per illustrare questo punto specifico: la presenza di più attori che scrivono in un unico file genera problemi di concorrenza. Per risolvere il problema è possibile introdurre un altro attore denominato EventWriter, che detiene la proprietà esclusiva delle risorse vincolate. Lo scenario è illustrato di seguito:

![][3]

Gli attori EventProcessor vengono contrassegnati come "processi di lavoro senza stato", in modo che il runtime possa eseguirne la scalabilità nel cluster, se necessario. Per questo motivo nell'illustrazione sopra riportata non è stato usato alcun identificatore per gli attori. In altre parole, gli attori senza stato sono costituiti da un pool di processi di lavoro gestiti dal runtime. Nell'esempio di codice seguente, l'attore EventProcessor esegue due operazioni: decide l'EventWriter (risorsa) da usare e richiama l'attore prescelto per scrivere l'evento elaborato. Per semplicità, si sceglierà Event Type come identificatore per l'attore EventWriter. In altre parole, per questo Event Type sarà presente uno e un solo EventWriter, che offrirà un accesso esclusivo e a thread singolo alla risorsa.

## Esempio di codice per la governance delle risorse: processore di eventi

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : StatelessActor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

Si passerà ora a esaminare l'attore EventWriter. Si limita essenzialmente a controllare l'accesso esclusivo alla risorsa vincolata, in questo caso il file, all'interno del quale scrive gli eventi.
## Esempio di codice per la governance delle risorse: Event Writer

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

La presenza di un unico attore responsabile della risorsa consente di aggiungere funzionalità come la memorizzazione nel buffer (buffering). È possibile memorizzare nel buffer gli eventi in ingresso e scriverli a intervalli regolari usando un timer o quando il buffer è pieno. L'esempio seguente usa un timer semplice:
## Esempio di codice per la governance delle risorse: Event Writer con buffer

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

Anche se il codice sopra riportato funziona correttamente, i clienti non conoscono l'esito del proprio evento nell'archivio sottostante. Per consentire il buffering e offrire ai clienti la possibilità di sapere cosa sta accadendo alla propria richiesta, si introduce l'approccio seguente che consente ai clienti di attendere che il proprio evento venga scritto nel file con estensione csv.

## Esempio di codice per la governance delle risorse: creazione in batch asincrono

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

Si userà questa classe per creare e gestire un elenco di attività non completate (per bloccare i client) e completarle in un'unica operazione dopo che gli eventi memorizzati nel buffer saranno stati scritti nell'archiviazione. Nella classe EventWriter è necessario eseguire tre operazioni: contrassegnare la classe attore come Reentrant, restituire il risultato di SubmitNext() e scaricare il timer. Il codice modificato avrà il seguente aspetto:

## Esempio di codice per la governance delle risorse: buffering con creazione in batch asincrono

```csharp
public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

Sembra facile? In effetti, lo è. Ma la semplicità, in questo caso, non deve distogliere l'attenzione dagli straordinari vantaggi che questo modello offre alle aziende. Questa architettura, infatti, offre:

* Indirizzamento delle risorse indipendente dalla posizione.
* Dimensioni del pool facilmente adattabili in base al numero di attori che agiscono per conto di una risorsa.
* Uso coordinato del pool sia dal lato client (come illustrato) sia dal lato server (è sufficiente immaginare un singolo attore davanti a ogni pool presente nell'immagine).
* Possibilità di aggiungere un pool scalabile (è sufficiente aggiungere attori che rappresentano una nuova risorsa).
* L'attore (come dimostrato in precedenza) può memorizzare nella cache i risultati provenienti dalla risorsa back-end su richiesta oppure in anticipo usando un timer, in modo da ridurre la necessità di accedere alla risorsa back-end.
* Invio asincrono efficiente.
* Un ambiente di codifica risulterà familiare per qualsiasi sviluppatore, non solo per gli esperti in middleware.

Questo modello è molto comune negli scenari in cui gli sviluppatori dispongono delle risorse vincolate necessarie per eseguire attività di sviluppo o creare sistemi a scalabilità orizzontale di grandi dimensioni.


## Passaggi successivi

[Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modello: Composizione dei servizi con stato](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

[Introduzione a Service Fabric Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=Nov15_HO4-->