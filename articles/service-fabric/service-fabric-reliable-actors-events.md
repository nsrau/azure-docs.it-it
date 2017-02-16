---
title: Eventi nei microservizi di Azure basati su attori | Documentazione Microsoft
description: Introduzione agli eventi per Service Fabric Reliable Actors
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2016
ms.author: amanbha
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 92df9505416c5b4326f007dbf4b920f2c7ec3bd8


---
# <a name="actor-events"></a>Eventi relativi agli attori
Gli eventi relativi agli attori consentono l'invio di notifiche il più possibile accurate dall'attore ai client. Tali eventi sono stati progettati per la comunicazione tra attore e client e non è consigliabile usarli per la comunicazione tra attori.

I frammenti di codice seguenti mostrano come usare eventi relativi agli attori nella propria applicazione.

Definire un'interfaccia che descriva gli eventi pubblicati dall'attore. Tale interfaccia deve derivare dall'interfaccia `IActorEvents` . Gli argomenti dei metodi devono essere [serializzabili in base al contratto dati](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). I metodi devono restituire void, in quanto le notifiche degli eventi sono unidirezionali e sono il più accurate possibile.

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

In caso di failover, l'attore può eseguire il failover su un processo o un nodo diverso. Il proxy dell'attore gestisce le sottoscrizioni attive e le rieffettua in modo automatico. È possibile controllare l'intervallo di risottoscrizione tramite l'API `ActorProxyEventExtensions.SubscribeAsync<TEvent>` . Per annullare la sottoscrizione usare l' `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Nell'attore pubblicare semplicemente gli eventi man mano che si verificano. Se vi sono sottoscrittori dell'evento, il runtime di Actors invierà loro la notifica.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Passaggi successivi
* [Rientranza di Reliable Actors](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Jan17_HO4-->


