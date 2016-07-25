<properties
   pageTitle="Introduzione a Service Fabric Reliable Actors| Microsoft Azure"
   description="Questa esercitazione illustra la procedura per la creazione, il debug e la distribuzione di un semplice servizio basato su attore usando Service Fabric Reliable Actors."
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
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Introduzione a Reliable Actors
Questo articolo illustra le nozioni fondamentali di Azure Service Fabric Reliable Actors e le procedure per creare, eseguire il debug e distribuire una semplice applicazione Reliable Actors in Visual Studio.

## Installazione e configurazione
Prima di iniziare, assicurarsi che nel computer sia configurato l'ambiente di sviluppo di Service Fabric. Se è necessario configurarlo, vedere le relative istruzioni dettagliate su [come configurare l'ambiente di sviluppo](service-fabric-get-started.md).

## Concetti di base
Per iniziare a usare Reliable Actors, è sufficiente comprendere quattro concetti di base:

* **Servizio attore**. Reliable Actors viene fornito in pacchetti di servizi Reliable Services che possono essere distribuiti nell'infrastruttura Service Fabric. Un servizio può ospitare uno o più attori. Di seguito verranno fornite informazioni più dettagliate sui vantaggi e gli svantaggi legati alla scelta di uno o più attori per servizio. Per il momento, si presuppone che sia necessario implementare un solo attore.
* **Interfaccia attore**. Questa interfaccia viene usata per definire l'interfaccia pubblica fortemente tipizzata di un attore. In base alla terminologia modello di Reliable Actors, questa interfaccia definisce i tipi di messaggi che l'attore può comprendere ed eseguire. L'interfaccia attore viene usata da altri attori o applicazioni client per "inviare" messaggi all'attore (in modo asincrono). Reliable Actors può implementare più interfacce. Come illustrato più avanti, un attore HelloWorld può implementare più interfacce IHelloWorld, ma anche un'interfaccia ILogging che definisce diversi messaggi e/o funzionalità.
* **Registrazione attore**. Nel servizio Reliable Actors, è necessario registrare il tipo di attore. In questo modo, Service Fabric è a conoscenza del nuovo tipo e può usarlo per creare nuovi attori.
* **Classe ActorProxy**. La classe ActorProxy viene usata dalle applicazioni client per richiamare i metodi esposti tramite le relative interfacce. La classe ActorProxy fornisce due funzionalità importanti:
	* Risolve i nomi. La classe è in grado di individuare l'attore nel cluster, ossia trovare il nodo del cluster in cui è ospitato.
	* Gestisce gli errori. La classe può ripetere le chiamate al metodo e determinare nuovamente la posizione dell'attore, ad esempio dopo un errore che richiede lo spostamento dell'attore in un altro nodo del cluster.

È opportuno citare le regole seguenti relative alle interfacce dell'attore:

- I metodi di interfaccia dell'attore non possono essere sottoposti a overload.
- I metodi di interfaccia dell'attore non accettano parametri facoltativi, out o ref.
- Non sono supportate interfacce generiche.

## Creare un nuovo progetto in Visual Studio
Dopo aver installato gli strumenti di Service Fabric per Visual Studio, è possibile creare nuovi tipi di progetto. Questi tipi sono inclusi nella categoria **Cloud** della finestra di dialogo **Nuovo progetto**.


![Strumenti di Service Fabric per Visual Studio - nuovo progetto][1]

Nella finestra di dialogo successiva è possibile scegliere il tipo di progetto che si vuole creare.

![Modelli di progetto di Service Fabric][5]

Per il progetto HelloWorld si userà il servizio Reliable Actors di Service Fabric.

Dopo aver creato la soluzione, verrà visualizzata la struttura seguente:

![Struttura del progetto di Service Fabric][2]

## Blocchi predefiniti di base di Reliable Actors

Una tipica soluzione Reliable Actors è costituita da tre progetti:

* **Il progetto di applicazione (MyActorApplication)**, in cui sono inclusi tutti i servizi per la distribuzione. Contiene il file *ApplicationManifest.xml* e gli script di PowerShell per gestire l'applicazione.

* **Il progetto dell'interfaccia (MyActor.Interfaces)**, in cui è inclusa la definizione dell'interfaccia per l'attore. Nel progetto MyActor.Interfaces è possibile definire le interfacce che verranno usate dagli attori nella soluzione. Le interfacce degli attori possono essere definite in qualsiasi progetto con un nome qualsiasi, tuttavia l'interfaccia definisce il contratto di attore che è condiviso dall'implementazione dell'attore e i client che chiamano l'attore, quindi è in genere opportuno definirlo in un assembly separato rispetto all'implementazione dell'attore e può essere condiviso da diversi altri progetti.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Il progetto di servizio Actor (MyActor)**, usato per definire il servizio di Service Fabric che ospiterà l'attore. Contiene l'implementazione dell'attore. L'implementazione di un attore è una classe che deriva dal tipo di base `Actor` e implementa le interfacce definite nel progetto MyActor.Interfaces.

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class MyActor : Actor, IMyActor
{
    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

Il servizio Actor deve essere registrato con un tipo di servizio nel runtime di Service Fabric. Perché il servizio Actor possa eseguire le istanze degli attori, è necessario che anche il proprio tipo di attore sia registrato con il servizio Actor. Il metodo di registrazione `ActorRuntime` esegue questa attività per gli attori.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se si inizia un nuovo progetto in Visual Studio ed è presente una sola definizione di attore, la registrazione viene inclusa per impostazione predefinita nel codice generato da Visual Studio. Se si definiscono altri attori nel servizio, è necessario aggiungere la registrazione dell'attore, come indicato di seguito:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] Il runtime di Service Fabric Actors emette alcuni [eventi e contatori delle prestazioni correlati ai metodi degli attori](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters), che sono utili per la diagnostica e il monitoraggio delle prestazioni.


## Debug

Gli strumenti di Service Fabric per Visual Studio supportano il debug nel computer locale. Per avviare una sessione di debug, premere F5. Visual Studio esegue, se necessario, la compilazione dei pacchetti e la distribuzione dell'applicazione nel cluster locale di Service Fabric e infine collega il debugger.

Durante il processo di distribuzione è possibile visualizzare lo stato di avanzamento nella finestra **Output**.

![Finestra di output del debug di Service Fabric][3]


## Passaggi successivi
 - [Modalità d'uso della piattaforma Service Fabric da parte di Reliable Actors](service-fabric-reliable-actors-platform.md)
 - [Gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md)
 - [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
 - [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Codice di esempio](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0713_2016-->