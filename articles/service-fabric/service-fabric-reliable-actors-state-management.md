---
title: Gestione dello stato di Reliable Actors | Documentazione Microsoft
description: "Descrive come viene gestito lo stato di Reliable Actors, reso persistente e replicato per la disponibilità elevata."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 18a4ab09d83c0a664317191ef15834cc7bf335fc
ms.lasthandoff: 04/14/2017


---
# <a name="reliable-actors-state-management"></a>Gestione dello stato di Reliable Actors
I Reliable Actors sono oggetti a thread singolo che possono incapsulare sia la logica che lo stato. Poiché gli attori vengono eseguiti nei servizi Reliable Services, possono mantenere lo stato in modo affidabile con gli stessi meccanismi di persistenza e replica utilizzati da Reliable Services. In questo modo, gli attori non perdono il proprio stato dopo gli errori, dopo la riattivazione in seguito a un'operazione di garbage collection o quando vengono spostati tra i nodi di un cluster a causa del bilanciamento delle risorse o degli aggiornamenti.

## <a name="state-persistence-and-replication"></a>Replica e persistenza dello stato
Tutti i Reliable Actors vengono considerati *con stato* perché ogni istanza dell'attore è mappata a un ID univoco. Ciò significa che le chiamate ripetute allo stesso ID attore vengono instradate alla stessa istanza dell'attore. In un sistema senza stato invece le chiamate client non vengono instradate ogni volta necessariamente allo stesso server. Per questo motivo, i servizi Actor sono sempre servizi con stato.

Anche se gli attori sono considerati con stato, non significa che devono archiviare lo stato in modo affidabile. Gli attori possono scegliere il livello di replica e persistenza dello stato in base ai requisiti di archiviazione dei dati:

* **Stato persistente:** lo stato è persistente nel disco e viene replicato in almeno tre repliche. Questa è l'opzione di archiviazione dello stato più durevole, in cui lo stato può persistere anche in caso di interruzione di un cluster completo.
* **Stato volatile:** lo stato viene replicato in almeno tre repliche e viene mantenuto solo in memoria. Fornisce la resilienza in caso di errore del nodo, di errore dell'attore e durante gli aggiornamenti e il bilanciamento delle risorse. Lo stato tuttavia non è persistente nel disco. Perciò, se vengono perse contemporaneamente tutte le repliche, si perderà anche lo stato.
* **Stato non persistente:** lo stato non viene replicato né viene scritto su disco. Questo livello è appropriato per gli attori che non devono mantenere lo stato affidabile.

Ogni livello di persistenza è semplicemente un altro *provider di stato* e un'altra configurazione di *replica* del servizio. La scrittura su disco dello stato dipende dal provider di stato, ovvero il componente di un servizio Reliable Services che archivia lo stato. La replica dipende dal numero di repliche con cui viene distribuito un servizio. In modo analogo ai servizi Reliable Services, il provider di stato e il numero di repliche possono essere impostati manualmente con facilità. Il framework per gli attori fornisce un attributo che, quando viene usato in un attore, seleziona automaticamente un provider di stato predefinito e genera automaticamente le impostazioni per il numero di repliche in modo da ottenere una di queste tre impostazioni di persistenza.

### <a name="persisted-state"></a>Stato persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Questa impostazione usa un provider di stato che archivia i dati su disco e imposta automaticamente il numero di repliche del servizio su 3.

### <a name="volatile-state"></a>Stato volatile
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Questa impostazione usa un provider di stato solo in memoria e imposta il numero di repliche su 3.

### <a name="no-persisted-state"></a>Stato non persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Questa impostazione usa un provider di stato solo in memoria e imposta il numero di repliche su 1.

### <a name="defaults-and-generated-settings"></a>Impostazioni predefinite e impostazioni generate
Quando si usa l'attributo `StatePersistence`, viene selezionato automaticamente un provider di stato in fase di esecuzione all'avvio del servizio attore. Il numero di repliche, tuttavia, viene impostato in fase di compilazione dagli strumenti di compilazione dell'attore di Visual Studio. Gli strumenti di compilazione generano automaticamente un *servizio predefinito* per il servizio attore in ApplicationManifest.xml. I parametri vengono creati per le **dimensioni minime del set di repliche** e le **dimensioni del set di repliche di destinazione**. 

È possibile cambiare questi parametri manualmente. Tuttavia, ogni volta che l'attributo `StatePersistence` viene modificato, i parametri vengono impostati sui valori predefiniti delle dimensioni del set di repliche per l'attributo `StatePersistence` selezionato, eseguendo l'override di eventuali valori precedenti. In altri termini, l'override dei valori impostati in ServiceManifest.xml viene eseguito *solo* in fase di compilazione quando si modifica l'attributo `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>State Manager
Ogni istanza dell'attore ha un proprio gestore di stato: una struttura di dati simile a un dizionario che archivia in modo affidabile le coppie chiave-valore. Il gestore di stato è un wrapper per il provider di stato. Può essere usato per archiviare i dati indipendentemente dall'impostazione di persistenza utilizzata. Non garantisce assolutamente che un servizio attore in esecuzione possa essere modificato da un'impostazione di stato volatile (solo in memoria) in un'impostazione di stato persistente tramite un aggiornamento in sequenza mantenendo al tempo stesso i dati. Tuttavia, è possibile modificare il numero di repliche per un servizio in esecuzione.

Le chiavi di gestione dello stato devono essere stringhe. I valori sono di tipo generico e possono essere di qualsiasi tipo, inclusi i tipi personalizzati. I valori archiviati nel gestore di stato devono essere serializzabili in base al contratto dati perché possono essere trasmessi in rete ad altri nodi durante la replica e possono essere scritti su disco, a seconda dell'impostazione di persistenza dello stato di un attore.

Il gestore di stato espone i metodi di dizionario comuni per la gestione dello stato, simili a quelli disponibili in Reliable Dictionary.

### <a name="accessing-state"></a>Accesso allo stato
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

### <a name="saving-state"></a>Salvataggio dello stato
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

### <a name="removing-state"></a>Rimozione dello stato
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
* [Serializzazione del tipo di attore](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
* [Polimorfismo dell'attore e modelli di progettazione orientati a oggetti](service-fabric-reliable-actors-polymorphism.md)
* [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio C#](https://github.com/Azure/servicefabric-samples)
* [Codice di esempio Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

