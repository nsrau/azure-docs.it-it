---
title: Gestire lo stato di Azure Service Fabric | Microsoft Docs
description: Informazioni su come accedere, salvare e rimuovere lo stato di Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 7c10d00916ef65767c98616c7337bfa444c339a9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664727"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Accedere, salvare e rimuovere lo stato di Reliable Actors
I [Reliable Actors](service-fabric-reliable-actors-introduction.md) sono oggetti a thread singolo che possono incapsulare sia la logica che lo stato e mantenere lo stato in modo affidabile. Ogni istanza dell'attore ha un proprio [gestore di stato](service-fabric-reliable-actors-state-management.md): una struttura di dati simile a un dizionario che archivia in modo affidabile le coppie chiave-valore. Il gestore di stato è un wrapper per il provider di stato. Può essere usato per archiviare i dati indipendentemente dall'[impostazione di persistenza](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) usata.

Le chiavi di gestione dello stato devono essere stringhe. I valori sono di tipo generico e possono essere di qualsiasi tipo, inclusi i tipi personalizzati. I valori archiviati nel gestore di stato devono essere serializzabili in base al contratto dati perché possono essere trasmessi in rete ad altri nodi durante la replica e possono essere scritti su disco, a seconda dell'impostazione di persistenza dello stato di un attore.

Il gestore di stato espone i metodi di dizionario comuni per la gestione dello stato, simili a quelli disponibili in Reliable Dictionary.

Per informazioni, vedere le [procedure consigliate per la gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Accedere allo stato
Lo stato è accessibile tramite il gestore di stato mediante la chiave. I metodi del gestore di stato sono tutti asincroni perché possono richiedere operazioni di I/O del disco quando gli attori hanno uno stato persistente. Al primo accesso, gli oggetti di stato vengono memorizzati nella cache. Le operazioni di accesso ripetute accedono agli oggetti direttamente dalla memoria e vengono restituite in modo sincrono senza incorrere nel sovraccarico di operazioni di I/O del disco o di cambio di contesto asincrono. Un oggetto di stato viene rimosso dalla cache nei casi seguenti:

* Il metodo di un attore genera un'eccezione non gestita dopo il recupero di un oggetto dal gestore di stato.
* Un attore viene riattivato dopo essere stato disattivato o dopo un errore.
* Il provider di stato invia lo stato al disco. Questo comportamento dipende dall'implementazione del provider di stato. Il provider di stato predefinito per l'impostazione `Persisted` ha questo comportamento.

Lo stato può essere recuperato con un'operazione *Get* standard che genera l'eccezione `KeyNotFoundException`(C#) o `NoSuchElementException`(Java) se non esiste una voce per la chiave:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Lo stato può essere recuperato anche con un metodo *TryGet* che non genera alcuna eccezione se non esiste una voce per la chiave:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Salvare lo stato
I metodi di recupero del gestore di stato restituiscono un riferimento a un oggetto nella memoria locale. Se questo oggetto viene modificato solo nella memoria locale non verrà salvato in modo permanente. Quando un oggetto viene recuperato dal gestore di stato e modificato, deve essere reinserito nel gestore di stato per essere salvato in modo permanente.

Lo stato può essere inserito usando un metodo *Set* non condizionale, che è l'equivalente della sintassi `dictionary["key"] = value`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

È possibile aggiungere lo stato tramite il metodo *Add*. Questo metodo genera l'eccezione `InvalidOperationException`(C#) o `IllegalStateException`(Java) se si tenta di aggiungere una chiave già esistente.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

È possibile aggiungere lo stato anche con il metodo *TryAdd*. Questo metodo non genera eccezioni se si tenta di aggiungere una chiave già esistente.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Alla fine di un metodo di un attore, il gestore di stato salva automaticamente tutti i valori che sono stati aggiunti o modificati da un'operazione di inserimento o aggiornamento. Un'operazione di salvataggio può includere il salvataggio su disco in modo permanente e la replica, a seconda delle impostazioni usate. I valori che non sono stati modificati non vengono salvati in modo permanente né replicati. Se non è stato modificato alcun valore, l'operazione di salvataggio non eseguirà alcuna azione. Se il salvataggio non riesce, lo stato modificato verrà eliminato e verrà ricaricato lo stato originale.

Lo stato può anche essere salvato manualmente chiamando il metodo `SaveStateAsync` sulla base dell'attore:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Rimuovere lo stato
Lo stato può essere rimosso in modo permanente dal gestore di stato di un attore chiamando il metodo *Remove*. Questo metodo genera l'eccezione `KeyNotFoundException` (C#) o `NoSuchElementException`(Java) se si tenta di rimuovere una chiave che non esiste.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

È possibile rimuovere in modo permanente lo stato anche con il metodo *TryRemove*. Questo metodo non genera eccezioni se si tenta di rimuovere una chiave che non esiste.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Lo stato archiviato in Reliable Actors deve essere serializzato prima di essere scritto sul disco e replicato per la disponibilità elevata. Altre informazioni sulla [serializzazione del tipo di attore](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Vedere anche [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
