<properties
   pageTitle="Polimorfismo nel framework Reliable Actors | Microsoft Azure"
   description="Compilare gerarchie di interfacce e tipi .NET nel framework Reliable Actors per riutilizzare le funzionalità e le definizioni delle API."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/08/2016"
   ms.author="seanmck"/>

# Polimorfismo nel framework Reliable Actors

Il framework Reliable Actors semplifica la programmazione di sistemi distribuiti. Questo è possibile grazie alla creazione di un servizio basato sulle stesse numerose tecniche relative alla progettazione orientata agli oggetti. Una di queste tecniche è il polimorfismo, che consente a tipi e interfacce di ereditare da più elementi padre generalizzati. L'ereditarietà nel framework Reliable Actors in genere segue il modello .NET con alcuni vincoli aggiuntivi.

## Interfacce

Per il framework Reliable Actors è necessario definire almeno un'interfaccia da implementare attraverso il tipo di attore. Questa interfaccia viene utilizzata per generare una classe proxy che può essere utilizzata dai client per comunicare con gli attori. Le interfacce possono ereditare da altre interfacce, purché ogni interfaccia implementata da un tipo di attore e tutti i relativi elementi genitori derivino da IActor. IActor è l'interfaccia di base definita dalla piattaforma per gli attori. Pertanto, l'esempio di polimorfismo classico che utilizza le forme può apparire nel seguente modo:

![Gerarchia delle interfacce per gli attori della forma][shapes-interface-hierarchy]


## Types

È inoltre possibile creare una gerarchia di tipi di attore, derivati dalla classe Attore di base fornita dalla piattaforma. Per gli attori con stato, è possibile creare in modo analogo una gerarchia di tipi di stato. Nel caso delle forme, si potrebbe avere un tipo di base `Shape` con un tipo di stato di `ShapeState`.

    public abstract class Shape : Actor<ShapeState>, IShape
    {
        ...
    }

I sottotipi di `Shape` possono usare i sottotipi di `ShapeType` per l'archiviazione di proprietà più specifiche.

    [ActorService(Name = "Circle")]
    public class Circle : Shape, ICircle
    {
        private CircleState CircleState => this.State as CircleState;

        public override ShapeState InitializeState()
        {
            return new CircleState();
        }

        [Readonly]
        public override Task<int> GetVerticeCount()
        {
            return Task.FromResult(0);
        }

       [Readonly]
       public override Task<double> GetArea()
       {
           return Task.FromResult(
               Math.PI*
               this.CircleState.Radius*
               this.CircleState.Radius);
       }

       ...
    }

Si noti l’attributo `ActorService` nel tipo di attore. Questo attributo comunica all'SDK di Service Fabric di Azure di creare automaticamente un servizio per l'hosting di attori di questo tipo. In alcuni casi, si potrebbe creare un tipo di base progettato esclusivamente per la condivisione delle funzionalità con dei sottotipi e che non verrà mai usato per creare un'istanza di attori concreti. In questi casi, è necessario usare la parola chiave `abstract` per indicare che non si creerà mai un attore basato su quel tipo.


## Passaggi successivi

- Vedere [come il framework di Reliable Actors usufruisce della piattaforma Service Fabric](service-fabric-reliable-actors-platform.md) per fornire uno stato coerente, scalabilità e affidabilità.
- Informazioni sul [ciclo di vita degli attori](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

<!---HONumber=AcomDC_0309_2016-->