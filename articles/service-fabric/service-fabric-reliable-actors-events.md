---
title: Eventi in Azure Service Fabric Actors basati su attore
description: Informazioni sugli eventi per Service Fabric Reliable Actors, una soluzione efficace per la comunicazione tra attore e client.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.custom: devx-track-csharp
ms.openlocfilehash: f5634a33dccb06437f2e5f095e7880221dba9d6e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007911"
---
# <a name="actor-events"></a>Eventi relativi agli attori
Gli eventi relativi agli attori consentono l'invio di notifiche il più possibile accurate dall'attore ai client. Tali eventi sono progettati per la comunicazione tra attore e client e non è consigliabile usarli per la comunicazione tra attori.

I frammenti di codice seguenti mostrano come usare eventi relativi agli attori nella propria applicazione.

Definire un'interfaccia che descriva gli eventi pubblicati dall'attore. Tale interfaccia deve derivare dall'interfaccia `IActorEvents` . Gli argomenti dei metodi devono essere [serializzabili in base al contratto dati](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). I metodi devono restituire void, in quanto le notifiche degli eventi sono unidirezionali e sono il più accurate possibile.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
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
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
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

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Sul client creare il proxy per l'attore che pubblica l'evento e sottoscrivere gli eventi.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

In caso di failover, l'attore può eseguire il failover su un processo o un nodo diverso. Il proxy dell'attore gestisce le sottoscrizioni attive e le rieffettua in modo automatico. È possibile controllare l'intervallo di risottoscrizione tramite l'API `ActorProxyEventExtensions.SubscribeAsync<TEvent>` . Per annullare la sottoscrizione usare l' `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Nell'attore pubblicare gli eventi man mano che si verificano. Se ci sono sottoscrittori dell'evento, il runtime di Actors invierà loro la notifica.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Passaggi successivi
* [Rientranza attore](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Documentazione di riferimento delle API di Actors](/previous-versions/azure/dn971626(v=azure.100))
* [Codice di esempio C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Codice di esempio di base C# .NET](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Codice di esempio Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
