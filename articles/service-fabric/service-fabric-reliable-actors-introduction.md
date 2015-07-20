<properties
   pageTitle="Panoramica di Azure Service Fabric Actors"
   description="Introduzione al modello di programmazione di Azure Service Fabric Actors"
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
   ms.date="03/02/2015"
   ms.author="claudioc"/>

# Introduzione ad Azure Service Fabric Actors
Azure Service Fabric Actors è un modello di programmazione basato su attori per [Service Fabric](service-fabric-technical-overview.md), una piattaforma per la creazione di applicazioni altamente affidabili e scalabili, da implementare sia in locale sia nel cloud, di facile sviluppo e gestione.

Fabric Actors offre un modello basato su attori asincrono e a thread singolo. Un attore rappresenta un'unità di stato e calcolo. Gli attori vengono distribuiti in tutto il cluster per ottenere scalabilità elevata. In caso di errore di un processo che ospita l'attore, il sistema ricrea l'attore in un altro processo. Analogamente, in caso di errore sul nodo in cui viene eseguito il processo host dell'attore, il sistema ricrea l'attore in un processo host su un altro nodo. Il runtime di Actors sfrutta l'archivio distribuito fornito dalla piattaforma Service Fabric sottostante per garantire una gestione dello stato altamente disponibile e coerente per gli attori. Questo semplifica notevolmente lo sviluppo e la gestione di applicazioni cloud distribuite basate su attori.

## Attori
Gli attori sono componenti isolati a thread singolo che incapsulano lo stato e il comportamento. Sono simili agli oggetti .NET e pertanto forniscono un modello di programmazione naturale agli sviluppatori. Ogni attore è un'istanza di un tipo di attore, in modo analogo a come un oggetto .NET è un'istanza di un tipo .NET. Ad esempio, può essere definito un tipo di attore che implementa la funzionalità di una calcolatrice e possono esistere molti attori di quel tipo distribuiti su vari nodi di un cluster. Ciascuno di questi attori è identificato in modo univoco da un ID.

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

Per ogni metodo di interfaccia, gli argomenti e il tipo di risultato dell'attività restituita devono essere [serializzabili in base al contratto dati](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

L'implementazione di questi metodi non comporta problemi di blocco o altri problemi di concorrenza poiché il runtime di Actors fornisce la concorrenza basata su turni per i metodi degli attori. Altre informazioni sull'argomento sono disponibili nella sezione [Concorrenza](#concurrency).

> [AZURE.TIP]Il runtime di Fabric Actors emette alcuni [eventi e contatori delle prestazioni relativi ai metodi degli attori](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

Vale la pena ricordare le seguenti regole relative ai metodi di interfaccia dell’attore: i metodi di interfaccia dell’attore non possono essere sottoposti a overload. I metodi di interfaccia dell’attore non accettano parametri facoltativi, out o ref.

## Comunicazione tra attori
L'API client Actors consente la comunicazione tra un'istanza di attore e un client di attore. Per comunicare con un attore, un client crea un oggetto proxy di attore che implementa l'interfaccia dell'attore. Il client interagisce con l'attore richiamando metodi sull'oggetto proxy. Il proxy dell'attore può essere usato per la comunicazione tra client e attore e tra attore e attore. Riprendendo l'esempio della calcolatrice, il codice client per un attore calcolatrice potrebbe essere scritto nel modo seguente:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Come mostrato nell'esempio precedente, per creare l'oggetto proxy dell'attore sono stati usati due tipi di informazioni: l'ID dell'attore e il nome dell'applicazione. L'ID dell'attore è un identificatore univoco dell'attore. Il nome dell'applicazione è il nome dell'[applicazione di Service Fabric](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) in base alla quale viene distribuito l'attore.

Gli attori sono virtuali, ossia esistono sempre. Non è necessario crearli né eliminarli in modo esplicito. Il runtime di Actors attiva automaticamente un attore la prima volta che riceve una richiesta per tale attore. Se per un certo periodo di tempo un attore non viene usato, il runtime di Actors ne eseguirà la Garbage Collection e lo attiverà in un momento successivo, se necessario. Altre informazioni su questo argomento sono disponibili nella sezione [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

L'API client Actors fornisce inoltre la trasparenza del percorso e il failover. La classe `ActorProxy` sul lato client esegue la risoluzione necessaria e individua la [partizione](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) del servizio attore, dove l'attore con l'ID specificato è ospitato e apre un canale di comunicazione con tale servizio. La classe `ActorProxy` ripete i tentativi in caso di errori di comunicazione e di failover. Ciò significa che un'implementazione di Actors può ottenere messaggi duplicati dallo stesso client.

## Concorrenza
Il runtime di Actors fornisce una concorrenza semplice basata su turni per i metodi degli attori. Ciò significa che, in qualsiasi momento, all'interno del codice dell'attore non può essere attivo più di un thread.

Un turno consiste nell'esecuzione completa di un metodo di attore in risposta alla richiesta di altri attori o client o nell'esecuzione completa di un callback di [timer/promemoria](service-fabric-reliable-actors-timers-reminders.md). Anche se questi metodi e callback sono asincroni, il runtime di Actors non ne esegue l'interfoliazione. Un turno deve essere interamente completato prima che sia consentito un nuovo turno. In altre parole, un metodo di attore o un callback di timer/promemoria in esecuzione deve essere interamente completato prima che sia consentita una nuova chiamata a un metodo o a un callback. Un metodo o un callback è considerato completato se è stata restituita l'esecuzione e se l'attività restituita dal metodo o dal callback è stata completata. È opportuno sottolineare che la concorrenza basata su turni viene rispettata anche su metodi, timer e callback diversi.

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

Per impostazione predefinita, il runtime di Actors consente la reentrancy. In altre parole, se un metodo dell'attore A chiama un metodo sull'attore B, che a sua volta chiama un altro metodo sull'attore A, tale metodo può essere eseguito perché fa parte dello stesso contesto logico della catena di chiamate. Tutte le chiamate di timer e promemoria iniziano con il nuovo contesto logico di chiamate. Per informazioni dettagliate, vedere la sezione [Reentrancy](service-fabric-reliable-actors-reentrancy.md).

Il runtime di Actors fornisce queste garanzie di concorrenza nelle situazioni in cui controlla le chiamate di questi metodi, ad esempio quelle eseguite in risposta a una richiesta client e per i callback di timer e promemoria. Tuttavia, se il codice dell'attore richiama direttamente questi metodi al di fuori dei meccanismi forniti dal runtime di Actors, il runtime non può offrire alcuna garanzia di concorrenza. Ad esempio, se il metodo viene richiamato nel contesto di un'attività che non è associata all'attività restituita dai metodi dell'attore oppure se viene richiamato da un thread che l'attore crea autonomamente, il runtime non può fornire garanzie di concorrenza. Pertanto, per eseguire operazioni in background, gli attori devono usare specifici [timer o promemoria](service-fabric-reliable-actors-timers-reminders.md) che rispettano la concorrenza basata su turni.

> [AZURE.TIP]Il runtime di Fabric Actors emette alcuni [eventi e contatori delle prestazioni relativi alla concorrenza](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

## Gestione dello stato degli attori
Fabric Actors consente di creare attori con o senza stato.

### Attori senza stato
Gli attori senza stato, come suggerito dal nome, non dispongono di alcuna informazione sullo stato gestita dal runtime di Actors. Questi attori derivano dalla classe base Actor. Possono avere variabili di membro che vengono mantenute per tutto il ciclo di vita dell'attore come qualsiasi altro tipo .NET. Tuttavia, se l'istanza dell'attore è sottoposta a Garbage Collection dopo un determinato tempo di inattività, le informazioni sullo stato memorizzate nelle variabili di membro vengono perse. Analogamente, tali informazioni possono essere perse a causa di failover, ad esempio in seguito ad aggiornamenti, a operazioni di bilanciamento delle risorse o a un errore di un processo dell'attore o del nodo che lo ospita.

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
Gli attori con stato dispongono di informazioni sullo stato che devono essere mantenute anche se si verificano episodi di Garbage Collection e failover. Gli attori con stato derivano dalla classe base `Actor<TState>`, dove `TState` è il tipo dello stato che deve essere mantenuto nei casi di Garbage Collection e failover. Lo stato è accessibile nei metodi degli attori mediante la proprietà `State` sulla classe base `Actor<TState>`. Di seguito è riportato un esempio di un attore con stato che accede alle informazioni sullo stato.

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

Il tipo di stato dell'attore deve essere [serializzabile in base al contratto dati](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [Nota] vedere l'articolo contenente le [note di Reliable Actors sulla serializzazione](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) per informazioni dettagliate sulla modalità di definizione delle interfacce e dei tipi di stato degli attori.

#### Provider dello stato degli attori
Il servizio di archiviazione e recupero dello stato viene fornito da un provider di stato, che può essere configurato per singolo attore o per tutti gli attori all'interno di un assembly in base a un attributo specifico. Nel runtime di Actors sono inclusi alcuni provider di stato degli attori predefiniti. La durata e l'affidabilità dello stato sono determinate dalle garanzie offerte da tale provider. Quando un attore viene attivato, lo stato dell'attore viene caricato in memoria. Quando un metodo dell'attore viene completato, lo stato modificato viene salvato automaticamente dal runtime di Actors mediante la chiamata a un metodo sul provider di stato. Se si verifica un errore durante il salvataggio dello stato, il runtime di Actors ricicla l'istanza dell'attore, creando e caricando una nuova istanza con l'ultimo stato coerente fornito dal provider di stato.

Per impostazione predefinita, un attore con stato usa il provider di stato basato sull'archivio chiave-valore distribuito che viene fornito dalla piattaforma Service Fabric. Per altre informazioni, vedere l'argomento relativo alla [scelta del provider di stato](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP]Il runtime di Fabric Actors emette alcuni [eventi e contatori delle prestazioni relativi alla gestione dello stato degli attori](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

#### Metodi di sola lettura
Per impostazione predefinita, il runtime di Actors salva lo stato di un attore dopo il completamento di una chiamata al metodo o al callback di timer e promemoria dell'attore. Non sono consentite altre chiamate dell'attore finché il salvataggio dello stato non è completato. A seconda del provider di stato, questa operazione di salvataggio può richiedere del tempo, durante il quale non sono consentite altre chiamate per l'attore.

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

[Configurazione dell'attore KVSActorStateProvider](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[Diagnostica e monitoraggio delle prestazioni per Fabric Actors](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
 

<!---HONumber=July15_HO2-->