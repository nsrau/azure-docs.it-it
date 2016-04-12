<properties
   pageTitle="Panoramica di Service Fabric Reliable Actors| Microsoft Azure"
   description="Introduzione al modello di programmazione di Service Fabric Reliable Actors"
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
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Introduzione a Service Fabric Reliable Actors
Insieme all'[API Reliable Services](service-fabric-reliable-services-introduction.md), l'API Reliable Actors è uno dei due framework di alto livello forniti da [Azure Service Fabric](service-fabric-technical-overview.md).

Basata sul modello degli attori, l'API Reliable Actors fornisce un modello di programmazione asincrono a thread singolo che semplifica il codice, senza rinunciare ai vantaggi di affidabilità e scalabilità offerti da Service Fabric.

## Attori
Gli attori sono componenti isolati a thread singolo che incapsulano lo stato e il comportamento. Sono simili agli oggetti .NET e forniscono quindi un modello di programmazione naturale. Ogni attore è un'istanza di un tipo di attore, in modo analogo a come un oggetto .NET è un'istanza di un tipo .NET. Un tipo di attore, ad esempio, può implementare la funzionalità di una calcolatrice e sui vari nodi di un cluster possono essere distribuiti molti attori di quel tipo. Ciascuno di questi attori è identificato in modo univoco da un ID.

## Definizione e implementazione di interfacce attore

Gli attori interagiscono con il resto del sistema, inclusi altri attori, passando messaggi asincroni in base a un modello di tipo richiesta-risposta. Queste interazioni sono definite in un'interfaccia come metodi asincroni. Ad esempio, l'interfaccia per un tipo di attore che implementa la funzionalità di una calcolatrice può essere definita come segue:

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Un tipo di attore può implementare questa interfaccia nel seguente modo:

```csharp
public class CalculatorActor : StatelessActor, ICalculatorActor
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

Poiché le chiamate ai metodi e le relative risposte risultano nelle richieste di rete eseguite nel cluster, gli argomenti e i tipi di risultati delle attività restituite devono essere serializzabili dalla piattaforma. In particolare, devono essere[contratto dati serializzabili](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.TIP] Il runtime di Service Fabric Actors emette alcuni [eventi e contatori delle prestazioni relativi ai metodi degli attori](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

Vale la pena ricordare le regole seguenti relative ai metodi di interfaccia dell'attore:
- I metodi di interfaccia dell'attore non possono essere sottoposti a overload.
- I metodi di interfaccia dell'attore non accettano parametri facoltativi, out o ref.

## Comunicazione tra attori
### Proxy dell'attore
L'API client Reliable Actors consente la comunicazione tra un'istanza di attore e un client di attore. Per comunicare con un attore, un client crea un oggetto proxy di attore che implementa l'interfaccia dell'attore. Il client interagisce con l'attore richiamando metodi sull'oggetto proxy. Il proxy dell'attore può essere usato per la comunicazione tra client e attore e tra attore e attore. Riprendendo l'esempio della calcolatrice, il codice client per un attore calcolatrice può essere scritto nel modo seguente:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Osservare come le due porzioni di informazioni usate per creare l'oggetto proxy dell'attore siano l'ID dell'attore e il nome dell'applicazione. L'ID dell'attore identifica in modo univoco l'attore, mentre il nome dell'applicazione identifica l'[applicazione di Service Fabric](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) in cui è distribuito l'attore.

### Durata dell'attore

Gli attori Service Fabric sono virtuali, vale a dire che la loro durata non è correlata alla loro rappresentazione in memoria. Di conseguenza, non devono essere esplicitamente create o eliminate. Il runtime di Actors attiva automaticamente un attore la prima volta che riceve una richiesta per tale attore. Se un attore non viene usato per un certo periodo di tempo, il runtime di Actors esegue un'operazione di garbage collection dell'oggetto in memoria. Mantiene inoltre conoscenza dell'esistenza dell'attore, nel caso in cui debba essere riattivato in un secondo momento. Per altre informazioni, vedere [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

### Trasparenza della posizione e failover automatico

Per garantire scalabilità e affidabilità elevate, Service Fabric distribuisce attori all'interno del cluster e automaticamente ne esegue la migrazione dai nodi non riusciti a quelli integri come richiesto. La`ActorProxy`classe sul lato client esegue la risoluzione necessaria per individuare l'attore dall'ID[partizione](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)e apre un canale di comunicazione con esso. In caso di errore di comunicazione e failover, la classe `ActorProxy` tenta di individuare nuovamente l'attore. In questo modo, i messaggi vengono recapitati correttamente nonostante la presenza di errori e un'implementazione di attore può ricevere messaggi duplicati dallo stesso client.

## Concorrenza
### Accesso basato su turni

Il runtime degli attori fornisce un semplice modello basato su turni accedere ai metodi degli attori. Ciò significa che, in qualsiasi momento, all'interno del codice dell'attore non può essere attivo più di un thread.

Un turno consiste nell'esecuzione completa di un metodo di un attore in risposta a una richiesta eseguita da altri attori o client oppure nell'esecuzione completa di un callback di [timer/promemoria](service-fabric-reliable-actors-timers-reminders.md). Anche se questi metodi e callback sono asincroni, il runtime di Actors non ne esegue l'interfoliazione. Un turno deve essere interamente completato prima che sia consentito un nuovo turno. In altre parole, un metodo di un attore o un callback di timer/promemoria in esecuzione deve essere interamente completato prima che sia consentita una nuova chiamata a un metodo o a un callback. Un metodo o un callback è considerato completato se è stata restituita l'esecuzione e se l'attività restituita dal metodo o dal callback è stata completata. È opportuno sottolineare che la concorrenza basata su turni viene rispettata anche su metodi, timer e callback diversi.

Per applicare la concorrenza basata su turni, il runtime di Actors acquisisce un blocco per ogni attore all'inizio di un turno e lo rilascia alla fine del turno. Pertanto, la concorrenza basata su turni viene applicata a livello di singolo attore e non per più attori. I metodi e i callback di timer/promemoria degli attori possono essere eseguiti simultaneamente per conto di diversi attori.

L'esempio seguente illustra questi concetti. Si consideri un tipo di attore che implementa due metodi asincroni, ad esempio *Method1* e *Method2*, un timer e un promemoria. Il diagramma seguente illustra un esempio di sequenza temporale per l'esecuzione di questi metodi e callback per conto di due attori, *ActorId1* e *ActorId2*, appartenenti a questo tipo.

![Accesso e concorrenza basata su turni del runtime di Reliable Actors][1]

Il diagramma precedente adotta queste convenzioni:

- Ogni linea verticale mostra il flusso logico di esecuzione di un metodo o di un callback per conto di un attore specifico.
- Gli eventi contrassegnati su ogni linea verticale sono riportati in ordine cronologico, con gli eventi più recenti elencati sotto quelli meno recenti.
- Per le sequenze temporali vengono usati colori diversi, corrispondenti a diversi attori.
- L'evidenziazione viene usata per indicare la durata in cui il blocco per attore è attivato per conto di un metodo o di un callback.

Per questo diagramma è opportuno evidenziare i punti seguenti:

- Mentre *Method1* è in esecuzione per conto di *ActorId2* in risposta alla richiesta client *xyz789*, arriva un'altra richiesta client *abc123* per l'esecuzione di *Method1* da parte di *ActorId2*. Tuttavia, la seconda esecuzione di *Method1* non inizia finché non è completata l'esecuzione precedente. Analogamente, un promemoria registrato da *ActorId2* viene attivato mentre *Method1* è in esecuzione in risposta alla richiesta client *xyz789*. Il callback di promemoria viene eseguito solo dopo che entrambe le esecuzioni di *Method1* sono state completate. Tutto ciò è regolato dalla concorrenza basata su turni applicata per *ActorId2*.
- Questa concorrenza viene applicata anche per *ActorId1*, come dimostrato dall'esecuzione di *Method1*, *Method2* e del callback del timer per conto di *ActorId1* eseguito in modalità seriale.
- L'esecuzione di *Method1* per conto di *ActorId1* si sovrappone all'esecuzione per conto di *ActorId2*. Questo accade perché la concorrenza basata su turni viene applicata solo all'interno di un attore e non tra più attori.
- In alcune esecuzioni di metodo o callback, l'oggetto `Task` restituito dal metodo o dal callback viene completato solo dopo la restituzione. In altre esecuzioni, l'oggetto `Task` risulta già completato al momento della restituzione del metodo o del callback. In entrambi i casi, il blocco per attore viene rilasciato solo dopo che l'oggetto `Task` viene restituito dal metodo o dal callback e completato.

### Rientranza

Per impostazione predefinita, il runtime di Actors consente la reentrancy. In altre parole, se un metodo di *Actor A* chiama un metodo su *Actor B*, che a sua volta chiama un altro metodo su *Actor A*, il metodo può essere eseguito perché fa parte dello stesso contesto logico della catena di chiamate. Tutte le chiamate di timer e promemoria iniziano con il nuovo contesto logico di chiamate. Per altre informazioni, vedere [Rientranza di Reliable Actors](service-fabric-reliable-actors-reentrancy.md).

### Ambito delle garanzie di concorrenza

Il runtime di Actors fornisce queste garanzie di concorrenza nelle situazioni in cui controlla le chiamate di questi metodi, ad esempio per le chiamate di metodi eseguite in risposta a una richiesta client e per i callback di promemoria e timer. Tuttavia, se il codice dell'attore richiama direttamente questi metodi al di fuori dei meccanismi forniti dal runtime di Actors, il runtime non può offrire alcuna garanzia di concorrenza. Ad esempio, se il metodo viene richiamato nel contesto di un'attività non associata all'attività restituita dai metodi dell'attore oppure se viene richiamato da un thread che l'attore crea in modo autonomo, il runtime non può fornire garanzie di concorrenza. Pertanto, per eseguire operazioni in background, gli attori devono usare specifici [timer e promemoria](service-fabric-reliable-actors-timers-reminders.md) che rispettano la concorrenza basata su turni.

> [AZURE.TIP] Il runtime di Service Fabric Actors emette alcuni [eventi e contatori delle prestazioni relativi alla concorrenza](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

## Gestione dello stato degli attori
Service Fabric consente di creare attori con o senza stato.

### Attori senza stato
Gli attori senza stato, che derivano dall aclasse di base `StatelessActor`, non dispongono di uno stato gestito dal runtime degli attori. Le variabili membro vengono mantenute per tutta la loro durata in memoria, come qualsiasi altro tipo .NET. Quando vengono sottoposte a garbage collection dopo un periodo di inattività, tuttavia, lo stato delle variabili viene perso. È possibile che lo stato venga perso anche a causa di failover eseguiti nel corso di aggiornamenti od operazioni di bilanciamento delle risorse oppure a seguito di errori nel processo dell'attore o del relativo nodo di hosting.

Di seguito è riportato un esempio di un attore senza stato.

```csharp
class HelloActor : StatelessActor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Attori con stato
Gli attori con stato dispongono di informazioni sullo stato che devono essere mantenute anche se si verificano episodi di Garbage Collection e failover. Essi derivano da `StatefulActor<TState>`, dove`TState`è il tipo di stato che deve essere mantenuto. Nei metodi degli attori, lo stato è accessibile mediante la proprietà `State` sulla classe di base.

Di seguito è riportato un esempio di un attore con stato che accede alle informazioni sullo stato.

```csharp
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Durante le operazioni di garbage collection e failover, lo stato dell'attore viene mantenuto se è stato salvato su disco e replicato in più nodi del cluster. Come per gli argomenti del metodo e i valori restituiti, il tipo di stato dell'attore deve essere quindi [serializzabile in base al contratto dati](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.NOTE] Per altre informazioni sulle modalità di definizione delle interfacce e dei tipi di stato degli attori, vedere [Note sulla serializzazione dei tipi di Reliable Actors](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

#### Provider dello stato degli attori
I servizi di archiviazione e recupero dello stato vengono forniti da un provider di stato degli attori. I provider di stato possono essere configurati per singolo attore o per tutti gli attori all'interno di un assembly in base a un attributo specifico del provider di stato. Nel momento in cui viene attivato un attore, ne viene caricato lo stato in memoria. Quando un metodo dell'attore viene completato, il runtime di Actors salva automaticamente lo stato modificato chiamando un metodo sul provider di stato. Se si verifica un errore durante il **salvataggio**, il runtime di Actors crea una nuova istanza dell'attore e carica l'ultimo stato coerente dal provider di stato.

Per impostazione predefinita, gli attori con stato usano il provider di stato attore per l'archivio chiave-valore, che si basa sull'archivio chiave-valore distribuito della piattaforma di Service Fabric. Per altre informazioni, vedere l'argomento relativo alla [scelta del provider di stato](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP] Il runtime di Actors emette alcuni [eventi e contatori delle prestazioni relativi alla gestione dello stato degli attori](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters), utili per la diagnostica e il monitoraggio delle prestazioni.

#### Metodi di sola lettura
Per impostazione predefinita, il runtime di Actors salva lo stato di un attore dopo il completamento di una chiamata al metodo o al callback di timer o promemoria dell'attore. Non sono consentite altre chiamate dell'attore finché il salvataggio dello stato non è completato.

Possono tuttavia esistere anche metodi di attori che non modificano lo stato. In questo caso, il tempo aggiuntivo dedicato al salvataggio dello stato può influire sulla velocità effettiva complessiva del sistema. Per ovviare a questo problema, è possibile contrassegnare come di sola lettura i metodi e i callback di timer che non modificano lo stato.

L'esempio seguente illustra come contrassegnare un metodo di un attore come di sola lettura usando l'attributo `Readonly`.

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

I callback di timer possono essere contrassegnati in modo analogo con l'attributo `Readonly`. Per i promemoria, il flag di sola lettura viene passato come argomento al metodo `RegisterReminder`, che viene richiamato per la registrazione del promemoria.

## Passaggi successivi
[Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)

[Timer e promemoria degli attori](service-fabric-reliable-actors-timers-reminders.md)

[Eventi relativi agli attori](service-fabric-reliable-actors-events.md)

[Rientranza di Reliable Actors](service-fabric-reliable-actors-reentrancy.md)

[Modalità d'uso della piattaforma Service Fabric da parte di Reliable Actors](service-fabric-reliable-actors-platform.md)

[Configurazione di KVSActorStateProvider di Reliable Actors](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)

[Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=AcomDC_0323_2016-->