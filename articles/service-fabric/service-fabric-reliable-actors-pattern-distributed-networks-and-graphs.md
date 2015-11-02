<properties
   pageTitle="Azure Service Fabric Actors - Modello di progettazione Reti distribuite e grafici"
   description="Modello di progettazione sull'uso di Service Fabric Actors per modellare un'applicazione come reti distribuite e grafici"
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
   ms.date="09/29/2015"
   ms.author="claudioc"/>

# Modello di progettazione di Reliable Actors: reti distribuite e grafici
Service Fabric Reliable Actors è la scelta naturale per la modellazione di soluzioni complesse che contengono relazioni e per la modellazione di tali relazioni come oggetti.

![][1]

Come illustrato nel diagramma, modellare un utente come un'istanza dell'attore (nodo nella rete) è un'operazione molto semplice. Il "Feed amici" (talvolta definito come il problema del "follower") consente, ad esempio, di visualizzare gli aggiornamenti di stato degli utenti a si cui è connessi, come avviene per Facebook e Twitter. Il modello Actor offre la flessibilità necessaria per affrontare il problema di materializzazione. È possibile popolare il Feed amici al momento dell'evento, ovvero aggiornare il feed nel momento in cui viene pubblicato un aggiornamento, come illustrato di seguito:

![][2]


## Esempio di codice per Smart Cache: Feed amici nei social network (ora dell'evento)

Esempio di codice per il popolamento del Feed amici:

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : Actor<SocialPersonState>, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

In alternativa, è possibile modellare Actors in modo che esegua il fan-out e compili il Feed amici nel timer di query, ovvero quando l'utente richiede il feed dei propri amici. O ancora, è possibile materializzare il Feed amici su un timer, ad esempio ogni 5 minuti. È possibile anche ottimizzare il modello e combinare l'elaborazione dell'ora di evento e dell'ora di query con un modello basato su timer personalizzato sulle abitudini dell'utente, ad esempio sulla frequenza con cui accede o pubblica un aggiornamento. Quando si modella un attore in un social network, è opportuno considerare anche i "super utenti", ovvero gli utenti con milioni di follower. Per soddisfare una tale richiesta, lo stato e il comportamento di questi utenti devono essere modellati in modo diverso. È possibile anche modellare un'attività che connetta più attori utente a un singolo attore di attività (hub e spoke). Le chat di gruppo e il game hosting sono due possibili scenari di questo tipo. Nelle chat di gruppo, ad esempio, un gruppo di partecipanti crea un attore chat di gruppo in grado di distribuire all'intero gruppo i messaggi inviati da un partecipante, come illustrato nell'esempio seguente:

## Esempio di codice per Smart Cache: GroupChat

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : Actor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

In questo caso si sfrutta semplicemente la capacità di Reliable Actors di consentire a qualsiasi attore di connettersi a qualsiasi altro attore nel cluster in base all'ID e di comunicare con esso senza preoccuparsi degli aspetti di posizionamento, indirizzamento, caching, messaggistica, serializzazione o routing.

## Passaggi successivi
[Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modello: Composizione dei servizi con stato](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

[Introduzione a Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=Oct15_HO4-->