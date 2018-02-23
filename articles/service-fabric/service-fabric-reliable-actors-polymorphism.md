---
title: Polimorfismo nel framework Reliable Actors | Microsoft Docs
description: "Compilare gerarchie di interfacce e tipi .NET nel framework Reliable Actors per riutilizzare le funzionalità e le definizioni delle API."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 38a86b25b30420c6f0b3027258fa094529c90278
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo nel framework Reliable Actors
Il framework Reliable Actors consente di creare attori usando molte delle tecniche usate per la progettazione orientata agli oggetti. Una di queste tecniche è il polimorfismo, che consente a tipi e interfacce di ereditare da più elementi padre generalizzati. L'ereditarietà nel framework Reliable Actors in genere segue il modello .NET con alcuni vincoli aggiuntivi. In caso di Java/Linux, segue il modello Java.

## <a name="interfaces"></a>Interfacce
Per il framework Reliable Actors è necessario definire almeno un'interfaccia da implementare attraverso il tipo di attore. Questa interfaccia viene utilizzata per generare una classe proxy che può essere utilizzata dai client per comunicare con gli attori. Le interfacce possono ereditare da altre interfacce, purché ogni interfaccia implementata da un tipo di attore e tutte le relative entità principali derivino da IActor (C#) o da Actor (Java). IActor (C#) e Actor (Java) sono le interfacce di base definite dalla piattaforma per gli attori rispettivamente nei framework .NET e Java. Pertanto, l'esempio di polimorfismo classico che utilizza le forme può apparire nel seguente modo:

![Gerarchia delle interfacce per gli attori della forma][shapes-interface-hierarchy]

## <a name="types"></a>Tipi
È inoltre possibile creare una gerarchia di tipi di attore, derivati dalla classe Attore di base fornita dalla piattaforma. Nel caso delle forme può essere presente un tipo `Shape` (C#) o `ShapeImpl` (Java) di base.

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

I sottotipi di `Shape` (C#) o `ShapeImpl` (Java) possono eseguire l'override dei metodi dalla base.

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
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Si noti l’attributo `ActorService` nel tipo di attore. Questo attributo indica al framework Reliable Actors che deve creare automaticamente un servizio per l'hosting di attori di questo tipo. In alcuni casi, si potrebbe creare un tipo di base progettato esclusivamente per la condivisione delle funzionalità con dei sottotipi e che non verrà mai usato per creare un'istanza di attori concreti. In questi casi, è necessario usare la parola chiave `abstract` per indicare che non si creerà mai un attore basato su quel tipo.

## <a name="next-steps"></a>Passaggi successivi
* Vedere [come il framework di Reliable Actors usufruisce della piattaforma Service Fabric](service-fabric-reliable-actors-platform.md) per fornire uno stato coerente, scalabilità e affidabilità.
* Informazioni sul [ciclo di vita degli attori](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
