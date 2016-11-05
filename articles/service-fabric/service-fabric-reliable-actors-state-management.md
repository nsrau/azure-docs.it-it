---
title: Gestione dello stato di Reliable Actors | Microsoft Docs
description: Descrive come viene gestito lo stato di Reliable Actors, reso persistente e replicato per la disponibilità elevata.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2016
ms.author: vturecek

---
# Gestione dello stato di Reliable Actors
I Reliable Actors sono oggetti a thread singolo che possono incapsulare sia la logica che lo stato. Poiché gli attori vengono eseguiti nei servizi Reliable Services, possono mantenere lo stato affidabile con gli stessi meccanismi di persistenza e replica usati da Reliable Services. In questo modo, gli attori non perdono il proprio stato dopo gli errori, dopo la riattivazione in seguito a un'operazione di Garbage Collection o quando vengono spostati tra i nodi di un cluster a causa del bilanciamento delle risorse o degli aggiornamenti.

## Replica e persistenza dello stato
Tutti i Reliable Actors vengono considerati *con stato* perché ogni istanza dell'attore è mappata a un ID univoco. Ciò significa che le chiamate ripetute allo stesso ID attore verranno instradate alla stessa istanza dell'attore. Questo comportamento è diverso da quanto avviene in un sistema senza stato in cui le chiamate client non vengono instradate ogni volta necessariamente allo stesso server. Per questo motivo, i servizi Actor sono sempre servizi con stato.

Tuttavia, anche se gli attori sono considerati con stato, non significa che devono archiviare lo stato in modo affidabile. Gli attori possono scegliere il livello di replica e persistenza dello stato in base ai requisiti di archiviazione dei dati:

* **Stato persistente:** lo stato è persistente nel disco e viene replicato in almeno tre repliche. Questa è l'opzione di archiviazione dello stato più durevole, in cui lo stato può persistere anche in caso di interruzione di un cluster completo.
* **Stato volatile:** lo stato viene replicato in almeno tre repliche e viene mantenuto solo in memoria. Fornisce la resilienza in caso di errore del nodo, di errore dell'attore e durante gli aggiornamenti e il bilanciamento delle risorse. Tuttavia, lo stato non è persistente nel disco, per cui se vengono perse contemporaneamente tutte le repliche, si perderà anche lo stato.
* **Stato non persistente:** lo stato non viene replicato né viene scritto su disco. È appropriato per gli attori che non devono mantenere lo stato affidabile.

Ogni livello di persistenza è semplicemente un altro *provider di stato* e un'altra configurazione di *replica* del servizio. La scrittura su disco dello stato dipende dal *provider di stato*, ovvero il componente di un servizio Reliable Services che archivia lo stato, mentre la replica dipende dal numero di repliche con cui viene distribuito un servizio. In modo analogo ai servizi Reliable Services, il provider di stato e il numero di repliche possono essere facilmente impostati manualmente. Il framework per gli attori fornisce un attributo che, quando usato in un attore, selezionerà automaticamente un provider di stato predefinito e genererà automaticamente le impostazioni per il numero di repliche in modo da ottenere una di queste tre impostazioni di persistenza.

### Stato persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```  
Questa impostazione usa un provider di stato che archivia i dati su disco e imposta automaticamente il numero di repliche del servizio su 3.

### Stato volatile
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
Questa impostazione usa un provider di stato solo in memoria e imposta il numero di repliche su 3.

### Stato non persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
Questa impostazione usa un provider di stato solo in memoria e imposta il numero di repliche su 1.

### Impostazioni predefinite e impostazioni generate
Quando si usa l'attributo `StatePersistence`, viene selezionato automaticamente un provider di stato in fase di esecuzione all'avvio del servizio attore. Il numero di repliche, tuttavia, viene impostato in fase di compilazione dagli strumenti di compilazione dell'attore di Visual Studio. Gli strumenti di compilazione generano automaticamente un *servizio predefinito* per il servizio attore in ApplicationManifest.xml. I parametri vengono creati per le **dimensioni minime del set di repliche** e le **dimensioni del set di repliche di destinazione**. Naturalmente è possibile modificare questi parametri manualmente, tuttavia, ogni volta che l'attributo `StatePersistence` viene modificato, i parametri verranno impostati sui valori predefiniti delle dimensioni del set di repliche per l'attributo `StatePersistence` selezionato, eseguendo l'override di eventuali valori precedenti. In altri termini, l'override dei valori impostati in ServiceManifest.xml verrà eseguito **solo** in fase di compilazione quando si modifica l'attributo `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="2" />
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

## State Manager
Ogni istanza dell'attore ha un proprio State Manager: una struttura di dati simile a un dizionario che archivia in modo affidabile le coppie chiave-valore. State Manager è un wrapper per il provider di stato. Può essere usato per archiviare i dati indipendentemente dall'impostazione di persistenza usata, ma non garantisce assolutamente che un servizio attore in esecuzione possa essere modificato da un'impostazione di stato volatile (solo in memoria) in un'impostazione di stato persistente tramite un aggiornamento in sequenza mantenendo al tempo stesso i dati. Tuttavia, è possibile modificare il numero di repliche per un servizio in esecuzione.

Le chiavi di State Manager devono essere stringhe, mentre i valori sono generici e possono essere di qualsiasi tipo, incluso i tipi personalizzati. I valori archiviati in State Manager devono essere serializzabili in base al contratto dati perché possono essere trasmessi in rete ad altri nodi durante la replica e possono essere scritti su disco, a seconda dell'impostazione di persistenza dello stato di un attore.

State Manager espone i metodi di dizionario comuni per la gestione dello stato, simili a quelli disponibili in Reliable Dictionary.

### Accesso allo stato
Lo stato è accessibile tramite State Manager dalla chiave. I metodi di State Manager sono tutti asincroni perché possono richiedere operazioni di I/O del disco quando gli attori hanno uno stato persistente. Al primo accesso, gli oggetti di stato vengono memorizzati nella cache. Le operazioni di accesso ripetute accedono agli oggetti direttamente dalla memoria e vengono restituite in modo sincrono senza incorrere nel sovraccarico di operazioni di I/O del disco o di cambio di contesto asincrono. Un oggetto di stato viene rimosso dalla cache nei casi seguenti:

* Il metodo di un attore genera un'eccezione non gestita dopo il recupero di un oggetto da State Manager.
* Un attore viene riattivato dopo essere stato disattivato o a causa di un errore.
* Se il provider di stato invia lo stato al disco. Questo comportamento dipende dall'implementazione del provider di stato. Il provider di stato predefinito per l'impostazione `Persisted` ha questo comportamento.

Lo stato può essere recuperato con un'operazione *Get* standard che genera l'eccezione `KeyNotFoundException` se non esiste una voce per la chiave specificata:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```

Lo stato può essere recuperato anche con un metodo *TryGet* che non genera alcuna eccezione se non esiste una voce per la chiave specificata:

```csharp
class MyActor : Actor, IMyActor
{
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

### Salvataggio dello stato
I metodi di recupero di State Manager restituiscono un riferimento a un oggetto nella memoria locale. Se questo oggetto viene modificato solo nella memoria locale non verrà salvato in modo permanente. Quando un oggetto viene recuperato da State Manager e modificato, deve essere reinserito in State Manager per essere salvato in modo permanente.

Lo stato può essere inserito usando un metodo *Set* non condizionale, che è l'equivalente della sintassi `dictionary["key"] = value`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```

Lo stato può essere aggiunto con un metodo *Add*, che genererà l'eccezione `InvalidOperationException` quando si prova ad aggiungere una chiave già esistente:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```

Lo stato può essere aggiunto anche con un metodo *TryAdd*, che non genererà alcuna eccezione quando si prova ad aggiungere una chiave già esistente:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
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

Alla fine di un metodo di un attore, State Manager salva automaticamente tutti i valori che sono stati aggiunti o modificati da un'operazione di inserimento o aggiornamento. Un'operazione di salvataggio può includere il salvataggio su disco in modo permanente e la replica, a seconda delle impostazioni usate. I valori che non sono stati modificati non vengono salvati in modo permanente né replicati. Se non è stato modificato alcun valore, l'operazione di salvataggio non eseguirà alcuna azione. Nel caso in cui il salvataggio non riesce, lo stato modificato verrà eliminato e verrà ricaricato lo stato originale.

Lo stato può anche essere salvato manualmente chiamando il metodo `SaveStateAsync` sulla base dell'attore:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```

### Rimozione dello stato
Lo stato può essere rimosso in modo permanente da State Manager di un attore chiamando il metodo *Remove*. Questo metodo genererà l'eccezione `KeyNotFoundException` quando si prova a rimuovere una chiave che non esiste:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```

Lo stato può essere rimosso in modo permanente anche con il metodo *TryRemove* che non genera alcuna eccezione quando si prova a rimuovere una chiave che non esiste:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
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

## Passaggi successivi
* [Serializzazione del tipo di attore](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
* [Polimorfismo dell'attore e modelli di progettazione orientati a oggetti](service-fabric-reliable-actors-polymorphism.md)
* [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->