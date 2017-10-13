---
title: Note sulla serializzazione dei tipi di attori di Reliable Actors | Documentazione Microsoft
description: Illustra i requisiti di base per la definizione delle classi serializzabili che possono essere usate per definire le interfacce e lo stato di Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 4b48b893e5a3bf5620f00a336576efe1ad63def8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Note sulla serializzazione dei tipi di Service Fabric Reliable Actors
Gli argomenti di tutti i metodi, i tipi di risultati delle attività restituiti da ogni metodo in un'interfaccia attore e gli oggetti archiviati nel gestore di stato di un attore devono essere [serializzabili in base al contratto dati](https://msdn.microsoft.com/library/ms731923.aspx). Questo vale anche per gli argomenti dei metodi definiti nelle [interfacce degli eventi dell'attore](service-fabric-reliable-actors-events.md). I metodi di interfaccia degli eventi dell'attore restituiscono sempre un valore void.

## <a name="custom-data-types"></a>Tipi di dati personalizzati
In questo esempio l'interfaccia attore seguente definisce un metodo che restituisce un tipo di dati personalizzato denominato `VoicemailBox`:

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

L'interfaccia è implementata da un attore, che usa il gestore di stato per archiviare un oggetto `VoicemailBox`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

In questo esempio l'oggetto `VoicemailBox` viene serializzato quando:

* L'oggetto viene trasmesso tra un'istanza di un attore e un chiamante.
* L'oggetto viene salvato nel gestore di stato in cui viene reso persistente sul disco e replicato in altri nodi.

Il framework Reliable Actors usa la serializzazione di DataContract. Gli oggetti dati personalizzati e i relativi membri devono quindi essere annotati con gli attributi **DataContract** e **DataMember** rispettivamente.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Passaggi successivi
* [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Timer e promemoria degli attori](service-fabric-reliable-actors-timers-reminders.md)
* [Eventi relativi agli attori](service-fabric-reliable-actors-events.md)
* [Rientranza di Reliable Actors](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfismo dell'attore e modelli di progettazione orientati a oggetti](service-fabric-reliable-actors-polymorphism.md)
* [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
