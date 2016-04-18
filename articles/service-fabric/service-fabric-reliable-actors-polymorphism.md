<properties
   pageTitle="Polimorfismo nel framework Reliable Actors | Microsoft Azure"
   description="Compilare gerarchie di interfacce e tipi .NET nel framework Reliable Actors per riutilizzare le funzionalità e le definizioni delle API."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="seanmck"/>

# Polimorfismo nel framework Reliable Actors

Il framework Reliable Actors consente di creare attori usando molte delle tecniche usate per la progettazione orientata agli oggetti. Una di queste tecniche è il polimorfismo, che consente a tipi e interfacce di ereditare da più elementi padre generalizzati. L'ereditarietà nel framework Reliable Actors in genere segue il modello .NET con alcuni vincoli aggiuntivi.

## Interfacce

Per il framework Reliable Actors è necessario definire almeno un'interfaccia da implementare attraverso il tipo di attore. Questa interfaccia viene utilizzata per generare una classe proxy che può essere utilizzata dai client per comunicare con gli attori. Le interfacce possono ereditare da altre interfacce, purché ogni interfaccia implementata da un tipo di attore e tutti i relativi elementi genitori derivino da IActor. IActor è l'interfaccia di base definita dalla piattaforma per gli attori. Pertanto, l'esempio di polimorfismo classico che utilizza le forme può apparire nel seguente modo:

![Gerarchia delle interfacce per gli attori della forma][shapes-interface-hierarchy]


## Types

È inoltre possibile creare una gerarchia di tipi di attore, derivati dalla classe Attore di base fornita dalla piattaforma. Nel caso delle forme può essere presente un tipo `Shape` di base.

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();
    
    public abstract Task<double> GetAreaAsync();
}
```

I sottotipi di `Shape` possono eseguire l'override dei metodi dalla base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Si noti l’attributo `ActorService` nel tipo di attore. Questo attributo indica al framework Reliable Actors che deve creare automaticamente un servizio per l'hosting di attori di questo tipo. In alcuni casi, si potrebbe creare un tipo di base progettato esclusivamente per la condivisione delle funzionalità con dei sottotipi e che non verrà mai usato per creare un'istanza di attori concreti. In questi casi, è necessario usare la parola chiave `abstract` per indicare che non si creerà mai un attore basato su quel tipo.


## Passaggi successivi

- Vedere [come il framework di Reliable Actors usufruisce della piattaforma Service Fabric](service-fabric-reliable-actors-platform.md) per fornire uno stato coerente, scalabilità e affidabilità.
- Informazioni sul [ciclo di vita degli attori](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

## Passaggi successivi
 - [Gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md)
 - [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
 - [Timer e promemoria degli attori](service-fabric-reliable-actors-timers-reminders.md)
 - [Eventi relativi agli attori](service-fabric-reliable-actors-events.md)
 - [Rientranza di Reliable Actors](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)

<!---HONumber=AcomDC_0406_2016-->