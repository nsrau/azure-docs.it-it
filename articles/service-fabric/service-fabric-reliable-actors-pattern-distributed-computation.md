<properties
   pageTitle="Azure Service Fabric Actors - Modello Calcolo distribuito"
   description="Azure Service Fabric è la soluzione ideale per la messaggistica asincrona parallela, per il calcolo parallelo e per la semplicità di gestione degli stati distribuiti."
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
   ms.date="04/01/2015"
   ms.author="claudioc"/>

# Modello di progettazione di Service Fabric Actors: calcolo distribuito
Questo modello è stato realizzato dopo aver osservato un cliente estrarre un calcolo finanziario in Azure Service Fabric Actors in un intervallo di tempo molto breve: una simulazione Monte Carlo per un preciso calcolo dei rischi.

In un primo momento, per coloro che non dispongono di una conoscenza specifica del dominio, la gestione in Azure Service Fabric di questo tipo di carico di lavoro può non essere immediata, soprattutto se paragonata ad approcci più tradizionali come Map/Reduce o MPI.

Azure Service Fabric, tuttavia, risulta essere la scelta ideale per la messaggistica asincrona parallela, per il calcolo parallelo e per la semplicità di gestione degli stati distribuiti, come illustrato nel diagramma seguente:

![][1]

Nell'esempio seguente si calcolerà semplicemente il pi greco eseguendo una simulazione Monte Carlo con gli attori seguenti:

* Processore responsabile del calcolo del pi greco mediante attori PoolTask.

* Responsabile PoolTask della simulazione Monte Carlo e di inviare i risultati all'aggregatore.

* Aggregatore responsabile dell'aggregazione dei risultati e di inviarli al finalizzatore.

* Finalizzatore responsabile di calcolare il risultato finale e di stamparlo su schermo.

## Esempio di codice per il calcolo distribuito: simulazione Monte Carlo

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(0); // stateless
            tasks.Add(task.CalculateAsync(tries, seed));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(0);
        return agg.AggregateAsync(pi);
    }
}
```

I timer costituiscono uno degli strumenti più comuni per aggregare risultati in Azure Service Fabric. Si usano attori senza stato per due motivi principali: il runtime potrà decidere dinamicamente quanti aggregatori sono necessari, offrendo in tal modo una scalabilità su richiesta, e creerà un'istanza degli attori a livello locale, ovvero nello stesso silo dell'attore chiamante, riducendo così gli hop di rete. L'aggregatore e il finalizzatore dovrebbero avere questo aspetto:

## Esempio di codice per il calcolo distribuito: aggregatore

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : Actor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return TaskDone.Done;
    }
}
```

A questo punto è evidente come sia possibile migliorare l'esempio della classifica con un aggregatore per la scalabilità e le prestazioni.

Con questo non si desidera in alcun modo affermare che Azure Service Fabric debba essere considerato un prodotto sostitutivo di altre soluzione per il calcolo distribuito di framework per Big Data o di elaborazione ad alte prestazioni. Alcuni prodotti, semplicemente, sono progettati in modo da poter essere meglio gestiti. È comunque possibile modellare i flussi di lavoro e il calcolo parallelo distribuito in Azure Service Fabric e continuare a usufruire dei vantaggi di semplicità che offre.

## Passaggi successivi
[Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modello: Composizione dei servizi con stato](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

[Introduzione a Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png
 

<!---HONumber=July15_HO2-->