<properties
   pageTitle="Eventi di Reliable Actors | Microsoft Azure"
   description="Introduzione agli eventi per Service Fabric Reliable Actors"
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
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# Eventi relativi agli attori
Gli eventi relativi agli attori consentono l'invio di notifiche il più possibile accurate dall'attore ai client. Tali eventi sono stati progettati per la comunicazione tra attore e client e non è consigliabile usarli per la comunicazione tra attori.

I frammenti di codice seguenti mostrano come usare eventi relativi agli attori nella propria applicazione.

Definire un'interfaccia che descriva gli eventi pubblicati dall'attore. Tale interfaccia deve derivare dall'interfaccia `IActorEvents`. Gli argomenti dei metodi devono essere [serializzabili in base al contratto dati](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). I metodi devono restituire void, in quanto le notifiche degli eventi sono unidirezionali e sono il più accurate possibile.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Dichiarare gli eventi pubblicati dall'attore nella relativa interfaccia.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

Sul lato client implementare il gestore eventi.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

Sul client creare il proxy per l'attore che pubblica l'evento e sottoscrivere gli eventi.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

In caso di failover, l'attore può eseguire il failover su un processo o un nodo diverso. Il proxy dell'attore gestisce le sottoscrizioni attive e le rieffettua in modo automatico. È possibile controllare l'intervallo di risottoscrizione tramite l'API `ActorProxyEventExtensions.SubscribeAsync<TEvent>`. Per annullare la sottoscrizione usare l'`ActorProxyEventExtensions.UnsubscribeAsync<TEvent>`API.

Nell'attore pubblicare semplicemente gli eventi man mano che si verificano. Se vi sono sottoscrittori dell'evento, il runtime di Actors invierà loro la notifica.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## Passaggi successivi
 - [Rientranza di Reliable Actors](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
 - [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Codice di esempio](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0907_2016-->