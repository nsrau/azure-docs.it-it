<properties
   pageTitle="Panoramica di Service Fabric Reliable Actors"
   description="Introduzione al modello di programmazione di Service Fabric Reliable Actors"
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

# Introduzione a Service Fabric Reliable Actors
Le API Reliable Actors sono uno dei due Framework ad alto livello forniti da[Service Fabric](service-fabric-technical-overview.md)insieme alle[API Reliable Services](service-fabric-reliable-services-introduction.md).

Le API Reliable Actors basate sul modello attore, forniscono un modello di programmazione asincrono a thread singolo che semplifica il codice, sfruttando comunque le garanzie di affidabilità e scalabilità fornito da Service Fabric.

## Attori
Gli attori sono componenti isolati a thread singolo che incapsulano lo stato e il comportamento. Sono simili agli oggetti .NET e pertanto forniscono un modello di programmazione naturale agli sviluppatori. Ogni attore è un'istanza di un tipo di attore, in modo analogo a come un oggetto .NET è un'istanza di un tipo .NET. Ad esempio, può essere definito un tipo di attore che implementa la funzionalità di una calcolatrice e possono esistere molti attori di quel tipo distribuiti su vari nodi di un cluster. Ciascuno di questi attori è identificato in modo univoco da un ID.

## Definizione e implementazione di interfacce attore

Gli attori interagiscono con il resto del sistema, inclusi altri attori, passando messaggi asincroni in base a un modello di tipo richiesta-risposta. Queste interazioni sono definite in un'interfaccia come metodi asincroni. Ad esempio, l'interfaccia per un tipo di attore che implementa la funzionalità di una calcolatrice può essere definita come segue:

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Un tipo di attore può implementare l'interfaccia precedente come segue:

```csharp
public class CalculatorActor : Actor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

Poiché le chiamate ai metodi e le relative risposte risultato nelle richieste di rete nel cluster, gli argomenti e il tipo di risultato dell'attività restituita deve essere serializzabile dalla piattaforma. In particolare, devono essere[contratto dati serializzabili](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.TIP]Il runtime di Fabric Actors emette alcuni [eventi e contatori delle prestazioni relativi ai metodi degli attori](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

Vale la pena ricordare le regole seguenti relative ai metodi di interfaccia dell'attore: i metodi di interfaccia dell'attore non possono essere sottoposti a overload. I metodi di interfaccia dell'attore non accettano parametri facoltativi, out o ref.

## Comunicazione tra attori
### Il Proxy attore
L'API client Actors consente la comunicazione tra un'istanza di attore e un client di attore. Per comunicare con un attore, un client crea un oggetto proxy di attore che implementa l'interfaccia dell'attore. Il client interagisce con l'attore richiamando metodi sull'oggetto proxy. Il proxy dell'attore può essere usato per la comunicazione tra client e attore e tra attore e attore. Riprendendo l'esempio della calcolatrice, il codice client per un attore calcolatrice potrebbe essere scritto nel modo seguente:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Tenere presente le due porzioni di informazioni usate per creare l'oggetto proxy attore - l'ID dell'attore e il nome dell'applicazione. L'ID dell'attore è un identificatore che identifica in modo univoco l'attore, mentre il nome dell'applicazione identifica l'[applicazione di Service Fabric](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors)in cui l'attore è distribuito.

### Durata attore

Gli attori Service Fabric sono virtuali, vale a dire che la loro durata non è correlata alla loro rappresentazione in memoria. Di conseguenza, non devono essere esplicitamente create o eliminate. Il runtime di Actors attiva automaticamente un attore la prima volta che riceve una richiesta per tale attore. Se un attore non viene usato per un certo periodo di tempo, il runtime di Actors porrà l'oggetto in memoria nella garbage collection, mantenendo la conoscenza dell'esistenza dell'attore nel caso in cui dovesse essere riattivato in un secondo momento. Per ulteriori informazioni, vedere[ciclo di vita degli attore e Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

### Trasparenza di percorso e Failover automatico

Per garantire scalabilità e affidabilità elevate, Service Fabric distribuisce attori all'interno del cluster e automaticamente ne esegue la migrazione dai nodi non riusciti a quelli integri come richiesto. La`ActorProxy`classe sul lato client esegue la risoluzione necessaria per individuare l'attore dall'ID[partizione](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)e apre un canale di comunicazione con esso. Il`ActorProxy`ritenta nel caso di errori di comunicazione e di failover. Ciò garantisce che i messaggi vengono recapitati in modo affidabile nonostante la presenza di errori, ma significa anche che è possibile che un'implementazione attore ottenga messaggi duplicati dallo stesso client.

## Concorrenza
### Concorrenza basata su turni.

Il runtime di Actors fornisce una concorrenza semplice basata su turni per i metodi degli attori. Ciò significa che, in qualsiasi momento, all'interno del codice dell'attore non può essere attivo più di un thread.

Un turno consiste nell'esecuzione completa di un metodo di attore in risposta alla richiesta di altri attori o client o nell'esecuzione completa di un callback di [timer/promemoria](service-fabric-reliable-actors-timers-reminders.md). Anche se questi metodi e i callback sono asincroni, il runtime di Actors non ne esegue l'interfoliazione: un turno essere interamente completato prima che venga consentito un nuovo turno. In altre parole, un metodo di attore o un callback di timer/promemoria in esecuzione deve essere interamente completato prima che sia consentita una nuova chiamata a un metodo o a un callback. Un metodo o un callback è considerato completato se è stata restituita l'esecuzione e se l'attività restituita dal metodo o dal callback è stata completata. È opportuno sottolineare che la concorrenza basata su turni viene rispettata anche su metodi, timer e callback diversi.

Per applicare la concorrenza basata su turni, il runtime di Actors acquisisce un blocco per ogni attore all'inizio di un turno e lo rilascia alla fine del turno. Pertanto, la concorrenza basata su turni viene applicata a livello di singolo attore e non per più attori. I metodi e i callback di timer/promemoria degli attori possono essere eseguiti simultaneamente per conto di diversi attori.

L'esempio seguente illustra questi concetti. Si consideri un tipo di attore che implementa due metodi asincroni (ad esempio *Method1* e *Method2*), un timer e un promemoria. Il diagramma seguente illustra un esempio di una sequenza temporale per l'esecuzione di questi metodi e callback per conto di due attori, *ActorId1* e *ActorId2*, appartenenti a questo tipo.

![][1]

Nel diagramma sono adottate le convenzioni seguenti:

- Ogni linea verticale mostra il flusso logico di esecuzione di un metodo o di un callback per conto di un attore specifico.
- Gli eventi contrassegnati su ogni linea verticale si verificano in ordine cronologico, con gli eventi più recenti riportati al di sotto di quelli meno recenti.
- Per le sequenze temporali vengono usati colori diversi, corrispondenti a diversi attori.
- L'evidenziazione viene usata per indicare la durata in cui il blocco per attore è attivato per conto di un metodo o di un callback.

Per questo diagramma è opportuno evidenziare i punti seguenti:

- Quando *Method1* è in esecuzione per conto di *ActorId2* in risposta alla richiesta client *xyz789*, arriva un'altra richiesta client *abc123* per l'esecuzione di *Method1* da parte di *ActorId2*. Tuttavia, l'esecuzione di *Method1* da parte del secondo attore non inizia finché non è completata l'esecuzione precedente. Analogamente, un promemoria registrato da *ActorId2* viene attivato mentre *Method1* è in esecuzione in risposta alla richiesta client *xyz789*. Il callback di promemoria viene eseguito solo dopo che entrambe le esecuzioni di *Method1* sono state completate. Tutto ciò è regolato dalla concorrenza basata su turni applicata per *ActorId2*.
- Questa concorrenza viene applicata anche per *ActorId1*, come dimostrato dall'esecuzione di *Method1*, *Method2* e del callback di timer per conto di *ActorId1* in modalità seriale.
- L'esecuzione di *Method1* per conto di *ActorId1* si sovrappone all'esecuzione per conto di *ActorId2*. Questo accade perché la concorrenza basata su turni viene applicata solo all'interno di un attore e non tra più attori.
- In alcune esecuzioni di metodo o callback, l'oggetto `Task` restituito dal metodo o dal callback viene completato dopo la restituzione. In altre esecuzioni, l'oggetto `Task` risulta già completato al momento della restituzione del metodo o del callback. In entrambi i casi, il blocco per ogni attore viene rilasciato solo dopo che entrambe le azioni si sono verificate, ad esempio dopo la restituzione del metodo o del callback e dopo il completamento di `Task`.

### Rientranza

Per impostazione predefinita, il runtime di Actors consente la reentrancy. In altre parole, se un metodo dell'attore A chiama un metodo sull'attore B, che a sua volta chiama un altro metodo sull'attore A, tale metodo può essere eseguito perché fa parte dello stesso contesto logico della catena di chiamate. Tutte le chiamate di timer e promemoria iniziano con il nuovo contesto logico di chiamate. Per informazioni dettagliate, vedere la sezione [Reentrancy](service-fabric-reliable-actors-reentrancy.md).

### Ambito delle garanzie di concorrenza

Il runtime di Actors fornisce queste garanzie di concorrenza nelle situazioni in cui controlla le chiamate di questi metodi, ad esempio quelle eseguite in risposta a una richiesta client e per i callback di timer e promemoria. Tuttavia, se il codice dell'attore richiama direttamente questi metodi al di fuori dei meccanismi forniti dal runtime di Actors, il runtime non può offrire alcuna garanzia di concorrenza. Ad esempio, se il metodo viene richiamato nel contesto di un'attività che non è associata all'attività restituita dai metodi dell'attore oppure se viene richiamato da un thread che l'attore crea autonomamente, il runtime non può fornire garanzie di concorrenza. Pertanto, per eseguire operazioni in background, gli attori devono usare specifici [timer o promemoria](service-fabric-reliable-actors-timers-reminders.md) che rispettano la concorrenza basata su turni.

> [AZURE.TIP]Il runtime di Fabric Actors emette alcuni [eventi e contatori delle prestazioni relativi alla concorrenza](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

## Gestione dello stato degli attori
Fabric Actors consente di creare attori con o senza stato.

### Attori senza stato
Gli attori senza stato, che derivano dalla``Actor``classe di base, non dispongono di uno stato gestito dal runtime di Actors. Le variabili membro vengono mantenute per tutta la loro durata in memoria come qualsiasi altro tipo .NET. Tuttavia, quando sono sottoposte a garbage collection dopo un periodo di inattività, lo stato viene perso. Analogamente, lo stato può essere perso a causa di failover che si verificano durante gli aggiornamenti, le operazioni di bilanciamento delle risorse o come risultato di errori nel processo attore o del suo nodo di hosting.

Di seguito è riportato un esempio di un attore senza stato.

```csharp
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Attori con stato
Gli attori con stato dispongono di informazioni sullo stato che devono essere mantenute anche se si verificano episodi di Garbage Collection e failover. Essi derivano dalla`Actor<TState>`classe base, dove`TState`è il tipo di stato che deve essere mantenuto. Lo stato è accessibile nei metodi degli attori mediante la proprietà `State` sulla classe base .

Di seguito è riportato un esempio di un attore con stato che accede alle informazioni sullo stato.

```csharp
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Lo stato attore viene mantenuto tra le operazioni di garbage collection e failover salvandolo su disco e replicandolo in più nodi del cluster. Ciò significa che, come gli argomenti del metodo e i valori restituiti, il tipo di stato attore deve essere[contratto dati serializzabile](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.NOTE][Nota] vedere l'articolo contenente le [note di Reliable Actors sulla serializzazione](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) per informazioni dettagliate sulla modalità di definizione delle interfacce e dei tipi di stato degli attori.

#### Provider dello stato degli attori
Il servizio di archiviazione e recupero dello stato viene fornito da un provider di stato, I provider di stato possono essere configurati per singolo attore o per tutti gli attori all'interno di un assembly in base a un attributo specifico del provider di stato. Quando un attore viene attivato, lo stato dell'attore viene caricato in memoria. Quando un metodo dell'attore viene completato, lo stato modificato viene salvato automaticamente dal runtime di Actors mediante la chiamata a un metodo sul provider di stato. Se si verifica un errore durante il salvataggio, il runtime di Actors crea una nuova istanza dell'attore e carica l'ultimo stato coerente dal provider di stato.

Per impostazione predefinita, gli attori con stato usano il provider di stato attore per l'archivio chiave-valore, che si basa sull'archivio chiave-valore distribuito della piattaforma di Service Fabric. Per altre informazioni, vedere l'argomento relativo alla [scelta del provider di stato](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP]Il runtime di Actors emette alcuni [eventi e contatori delle prestazioni relativi alla gestione dello stato degli attori](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

#### Metodi di sola lettura
Per impostazione predefinita, il runtime di Actors salva lo stato di un attore dopo il completamento di una chiamata al metodo o al callback di timer e promemoria dell'attore. Non sono consentite altre chiamate dell'attore finché il salvataggio dello stato non è completato.

Possono esistere anche metodi di attore che non modificano lo stato. In tal caso, il tempo aggiuntivo dedicato al salvataggio dello stato può avere effetti negativi sulla velocità effettiva globale del sistema. Per ovviare a questo problema, è possibile contrassegnare come di sola lettura i metodi e i callback di timer che non modificano lo stato.

L'esempio seguente illustra come contrassegnare un metodo di attore come di sola lettura usando l'attributo `Readonly`.

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

I callback di timer possono essere contrassegnati in modo analogo con l'attributo `Readonly`. Per i promemoria, il flag di sola lettura viene passato come argomento al metodo `RegisterReminder`, che viene richiamato per la registrazione del promemoria.

## Passaggi successivi
### Concetti
[Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)

[Timer e promemoria degli attori](service-fabric-reliable-actors-timers-reminders.md)

[Eventi degli attori](service-fabric-reliable-actors-events.md)

[Reentrancy degli attori](service-fabric-reliable-actors-reentrancy.md)

[Modalità d'uso della piattaforma Service Fabric da parte di Fabric Actors](service-fabric-reliable-actors-platform.md)

[Configurazione dell'attore KVSActorStateProvider](service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[Diagnostica e monitoraggio delle prestazioni per Fabric Actors](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=August15_HO6-->