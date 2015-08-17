<properties
   pageTitle="Modello di progettazione Smart Cache di Reliable Actors"
   description="Modello di progettazione su come usare Reliable Actors come infrastruttura di caching in applicazioni basate sul Web"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="claudioc"/>

# Modello di progettazione di Reliable Actors: Smart Cache
Le nuove applicazioni sono generalmente costituite dalla combinazione di un livello Web, un livello di caching, un livello di archiviazione e, in alcuni casi, un livello di lavoro. Il livello di caching, in genere, è essenziale per le prestazioni e può essere costituito da più sotto-livelli. In molti casi, le cache sono costituite da semplici coppie chiave-valore. In altri casi, vengono usati come cache sistemi simili a [Redis](http://redis.io), che offrono una semantica più completa. Seppur speciale, tuttavia, qualsiasi livello di caching è limitato nella semantica e, soprattutto, costituisce un ulteriore livello da gestire. E se invece gli oggetti si limitassero a mantenere lo stato in variabili locali e potessero essere automaticamente salvati in uno snapshot o in modo permanente in un archivio stabile? Raccolte complete come elenchi, set ordinati, code e altri tipi personalizzati verrebbero semplicemente modellate come metodi e variabili membro.

![][1]

## Esempio della classifica
Le classifiche costituiscono un esempio interessante. Un oggetto classifica deve mantenere un elenco ordinato di giocatori con i relativi punteggi, in modo che possa essere interrogato, ad esempio per trovare i "100 migliori giocatori" o per individuare la posizione di un giocatore rispetto ai +- N giocatori sopra/sotto in classifica. In una soluzione basata su strumenti tradizionali, sarebbe necessario eseguire il GET dell'oggetto classifica (raccolta che supporta l'inserimento di una nuova tupla<Player  Points> denominata Score), ordinarlo e infine eseguirne il "PUT" nella cache. Per coerenza, inoltre, si bloccherebbe l'oggetto classifica (GETLOCK, PUTLOCK). Si pensi invece a una soluzione basata su attori in cui lo stato e il comportamento siano insieme. Sono disponibili due opzioni:

* Implementare la raccolta Classifica come parte dell'attore
* Usare l'attore come interfaccia di interazione con la raccolta, in modo che sia possibile mantenerlo in una variabile membro. L'interfaccia dovrebbe avere un aspetto simile al seguente:

## Esempio di codice per Smart Cache: interfaccia della classifica

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

Si implementa quindi l'interfaccia e, adottando la seconda opzione, si incapsula il comportamento della raccolta nell'attore:

## Esempio di codice per Smart Cache: attore Classifica

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
        return TaskDone.Done;
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

Nell'esempio di codice sopra riportato, il membro stato della classe indica lo stato dell'attore i metodi per leggere e scrivere dati.

## Esempio di codice per Smart Cache: raccolta Classifica

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

Nessun dato da inviare, nessun blocco, solo la modifica di oggetti remoti in un runtime distribuito, gestendo più client come se fossero singoli oggetti di un'unica applicazione che gestisce solo un client. Di seguito è illustrato il client di esempio:

## Esempio di codice per Smart Cache: chiamata dell'attore Classifica

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

L'output è simile al seguente:

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## Scalabilità dell'architettura
Può sembrare che l'esempio precedente crei un collo di bottiglia nell'istanza della classifica. Cosa accadrebbe, quindi, se si dovesse pianificare il supporto per centinaia o migliaia di giocatori? Un modo per soddisfare questa esigenza potrebbe essere quello di introdurre aggregatori senza stato che agiscano come un buffer, ovvero conservare i punteggi parziali (i subtotali) e inviarli periodicamente all'attore Classifica, che potrebbe quindi mantenere aggiornata la classifica finale. La tecnica di aggregazione verrà esaminata in dettaglio nelle sezioni successive. Non sarebbe inoltre necessario tener conto di mutex, semafori o altri costrutti di concorrenza tradizionalmente richiesti dai programmi simultanei con comportamento corretto. Di seguito è riportato un altro esempio di cache che dimostra come con gli attori sia possibile implementare una semantica completa. In questo caso si decide di implementare la logica della coda di priorità (ridurre il numero, aumentare la priorità) nell'ambito dell'implementazione del modello Actor. L'interfaccia di IJobQueue ha un aspetto simile al seguente:

## Esempio di codice per Smart Cache: interfaccia della coda di processi

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

È inoltre necessario definire l'elemento processo:

## Esempio di codice per Smart Cache: processo

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

Infine, si implementa l'interfaccia IJobQueue nel livello di dettaglio. Per maggiore chiarezza sono stati appositamente omessi i dettagli di implementazione della coda di priorità. Un'implementazione di esempio è disponibile negli esempi di accompagnamento.

## Esempio di codice per Smart Cache: coda di processi

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

        return TaskDone.Done;
    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

L'output è simile al seguente:

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## Flessibilità degli attori
Negli esempi precedenti, Classifica e JobQueue, sono state usate due tecniche diverse:

* Nell'esempio della classifica, nell'attore è stato incapsulato un oggetto Classifica come una variabile di membro privata, a cui è stata semplicemente associata un'interfaccia, a livello sia di stato sia di funzionalità.

* Nell'esempio di JobQueue, invece, è stato deciso di implementare l'attore come una coda di priorità, anziché fare riferimento a un altro oggetto definito altrove.

Gli attori offrono agli sviluppatori la flessibilità necessaria per definire strutture di oggetti complete all'interno degli attori o nei grafici oggetti di riferimento all'esterno degli attori. In termini di caching, gli attori possono eseguire operazioni di write-behind o write-through. È possibile anche usare tecniche diverse con una granularità a livello di variabile membro. In altre parole, si dispone di un controllo completo su cosa e quando salvare in modo permanente. Non è necessario, inoltre, salvare in modo permanente uno stato transitorio o uno stato che è possibile creare a partire dallo stato salvato. Per quanto riguarda la necessità di popolare le cache degli attori, inoltre, sono disponibili varie opzioni. Gli attori offrono metodi virtuali denominati OnActivateAsync() e OnDectivateAsync() per segnalare quando un'istanza dell'attore è attivata o disattivata. Si ricordi che l'attore viene attivato su richiesta nel momento in cui viene inviata ad esso la prima richiesta. È possibile usare OnActivateAsync() per popolare lo stato su richiesta come nel read-through, ad esempio da un archivio esterno stabile. Oppure è possibile popolare lo stato in base a un timer, ad esempio un attore "tasso di cambio" che offre la funzione di conversione in base ai più recenti tassi di cambio. Questo attore può popolare periodicamente (ad esempio, ogni 5 secondi) il proprio stato facendo ricorso a un servizio esterno e usare lo stato per la funzione di conversione. Vedere l'esempio seguente:

## Esempio di codice per Smart Cache: convertitore di frequenza

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

    return TaskDone.Done;
}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
    return TaskDone.Done;
}

```

Il modello Smart Cache offre, essenzialmente, i seguenti vantaggi:

* Elevata velocità effettiva/latenza ridotta da richieste di servizio provenienti dalla memoria.
* Routing a istanza singola e serializzazione a singolo thread di richieste verso un elemento, senza conflitti nell'archivio permanente.
* Operazioni semantiche, ad esempio Enqueue (elemento di processo).
* Operazioni write-through o write-behind di facile implementazione.
* Rimozione automatica degli elementi usati meno di recente (gestione delle risorse).
* Affidabilità ed elasticità automatica.


## Passaggi successivi
[Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modello: Composizione dei servizi con stato](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

[Introduzione a Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=August15_HO6-->