---
title: Reliable Actors in Service Fabric | Documentazione Microsoft
description: "Descrive come Reliable Actors si sovrappone a Reliable Services e usa le funzionalità della piattaforma Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: ba07b1bf1b49ebb24a7d5cfbaad71f5f17c49192
ms.lasthandoff: 03/28/2017


---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Modalità d'uso della piattaforma Service Fabric da parte di Reliable Actors
Questo articolo descrive il funzionamento di Reliable Actors sulla piattaforma Service Fabric. Reliable Actors viene eseguito in un framework ospitato in un'implementazione di un servizio Reliable Services con stato denominato *servizio Actor*. Il servizio Actor contiene tutti i componenti necessari per gestire il ciclo di vita e l'invio di messaggi per gli attori:

* Il runtime di Actors gestisce il ciclo di vita e la Garbage Collection e applica l'accesso a thread singolo.
* Un listener di comunicazione remota del servizio Actor accetta chiamate di accesso remoto per gli attori e le invia a un dispatcher per l'indirizzamento all'istanza d attore appropriata.
* Il provider di stato degli attori esegue il wrapping dei provider di stato (come il provider di stato Reliable Collections) e fornisce un adattatore per la gestione dello stato degli attori.

Questi componenti insieme costituiscono il framework Reliable Actors. 

## <a name="service-layering"></a>Livelli del servizio
Dato che il servizio Actor stesso è un servizio Reliable Services, tutti i concetti di [modello applicativo](service-fabric-application-model.md), ciclo di vita, [creazione pacchetti](service-fabric-package-apps.md), [distribuzione](service-fabric-deploy-remove-applications.md), aggiornamento e ridimensionamento validi per Reliable Services si applicano anche ai servizi Actor. 

![Livelli del servizio Actor][1]

Il diagramma precedente mostra la relazione tra i framework applicativi di Service Fabric e il codice utente. Gli elementi blu rappresentano il framework applicativo di Reliable Services, quelli arancioni il framework Reliable Actors e quelli verdi il codice utente. 

In Reliable Services il servizio eredita la classe `StatefulService`, che a sua volta deriva da `StatefulServiceBase`. (o `StatelessService` per i servizi senza stato). In Reliable Actors si usa il servizio Actor che è un'implementazione diversa della classe `StatefulServiceBase` che implementa il modello dell'attore in cui vengono eseguiti gli attori. Dato che il servizio Actor è semplicemente un'implementazione di `StatefulServiceBase`, si può scrivere il proprio servizio che deriva da `ActorService` e implementare le funzionalità a livello di servizio così come si farebbe quando si eredita `StatefulService`, ad esempio:

* Backup e ripristino del servizio.
* Funzionalità condivisa per tutti gli attori, ad esempio un interruttore.
* Chiamate di procedura di comunicazione remota sul servizio Actor stesso, nonché su ogni attore. 

### <a name="using-the-actor-service"></a>Uso del servizio Actor
Le istanze degli attori hanno accesso al servizio Actor in cui sono in esecuzione. Tramite il servizio Actor, le istanze degli attori possono ottenere il contesto del servizio che include l'ID partizione, il nome del servizio, il nome dell'applicazione e altre informazioni specifiche sulla piattaforma Service Fabric a livello di codice:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```

Come tutti i servizi Reliable Services, il servizio Actor deve essere registrato con un tipo di servizio nel runtime di Service Fabric. Perché il servizio Actor possa eseguire le istanze degli attori, è necessario che anche il proprio tipo di attore sia registrato con il servizio Actor. Il metodo di registrazione `ActorRuntime` esegue questa attività per gli attori. Nel caso più semplice, è sufficiente registrare il tipo di attore e verrà usato implicitamente il servizio Actor con le impostazioni predefinite:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```  

In alternativa, è possibile usare un'espressione lambda fornita dal metodo di registrazione per creare manualmente il servizio Actor. Questo consente di configurare il servizio Actor e costruire esplicitamente le istanze degli attori, in cui è possibile inserire le dipendenze per l'attore mediante il relativo costruttore:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

### <a name="actor-service-methods"></a>Metodi del servizio Actor
Il servizio Actor implementa `IActorService`, che a sua volta implementa `IService`. Questo è l'interfaccia usata dalla comunicazione remota di Reliable Services, che consente le chiamate RPC sui metodi del servizio. Contiene i metodi a livello di servizio che possono essere chiamati in remoto mediante la comunicazione remota del servizio.

#### <a name="enumerating-actors"></a>Enumerazione degli attori
Il servizio Actor consente al client di enumerare i metadati relativi agli attori ospitati dal servizio. Dato che il servizio Actor è un servizio con stato partizionato, l'enumerazione viene eseguita per partizione. Poiché ogni partizione può contenere un numero elevato di attori, l'enumerazione viene restituita come set di risultati a pagine. Le pagine vengono esaminate in ciclo fino a quando non vengono lette tutte. L'esempio seguente illustra come creare un elenco di tutti gli attori attivi in una partizione di un servizio Actor:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>Eliminazione di attori
Il servizio Actor fornisce anche una funzione per l'eliminazione di attori:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```

Per altre informazioni sull'eliminazione di attori e il relativo stato, vedere la [documentazione sul ciclo di vita degli attori](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Servizio Actor personalizzato
Usando l'espressione lambda della registrazione attore si può registrare anche il proprio servizio Actor personalizzato che deriva da `ActorService` , in cui è possibile implementare la propria funzionalità a livello di servizio. Questa operazione si esegue scrivendo una classe di servizio che eredita `ActorService`. Un servizio Actor personalizzato eredita tutte le funzionalità di runtime dell'attore da `ActorService` e può essere usato per implementare i propri metodi del servizio.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```


#### <a name="implementing-actor-back-up-and-restore"></a>Implementazione del backup e ripristino dell'attore
 Nell'esempio seguente il servizio Actor personalizzato espone un metodo per il backup dei dati dell'attore sfruttando il listener di comunicazione remota già presente in `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```

In questo esempio `IMyActorService` è un contratto di comunicazione remota che implementa `IService` e viene successivamente implementato da `MyActorService`. Aggiungendo questo contratto di comunicazione remota, i metodi su `IMyActorService` ora sono disponibili anche per un client mediante la creazione di un proxy di comunicazione remota usando `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```


## <a name="application-model"></a>Modello di applicazione
I servizi Actor sono servizi Reliable Services, per cui il modello applicativo è lo stesso. Tuttavia, gli strumenti di compilazione del framework attore generano automaticamente gran parte dei file del modello applicativo.

### <a name="service-manifest"></a>Manifesto del servizio
Il contenuto di ServiceManifest.xml del servizio Actor viene generato automaticamente dagli strumenti di compilazione del framework attore. Sono inclusi:

* Il tipo del servizio Actor. Il nome del tipo viene generato in base al nome del progetto attore. In base all'attributo di persistenza nell'attore, viene impostato anche il flag HasPersistedState.
* Pacchetto di codice.
* Pacchetto di configurazione.
* Risorse ed endpoint

### <a name="application-manifest"></a>Manifesto dell'applicazione
Gli strumenti di compilazione del framework attore creano automaticamente una definizione del servizio predefinito per il servizio Actor. Le proprietà del servizio predefinito vengono popolate dagli strumenti di compilazione:

* Il numero di set di repliche è determinato dall'attributo di persistenza sull'attore. Ogni volta che viene modificato l'attributo di persistenza nell'attore, viene reimpostato di conseguenza il numero di set di repliche nella definizione del servizio predefinito.
* Lo schema e l'intervallo della partizione vengono impostati su Uniform Int64 con l'intervallo di chiavi Int64 completo.

## <a name="service-fabric-partition-concepts-for-actors"></a>Concetti relativi alla partizione di Service Fabric per gli attori
I servizi Actor sono servizi con stato partizionati. Ogni partizione di un servizio Actor contiene un set di attori. Le partizioni del servizio vengono distribuite automaticamente su più nodi in Service Fabric. Quindi, le istanze degli attori vengono distribuite di conseguenza.

![Partizionamento e distribuzione degli attori][5]

È possibile creare servizi Reliable Services con schemi di partizione e intervalli di chiavi di partizione diversi. Il servizio Actor usa lo schema di partizionamento Int64 con l'intervallo di chiavi Int64 completo per mappare gli attori alle partizioni. 

### <a name="actor-id"></a>ID attore
A ogni attore creato nel servizio è associato un ID univoco, rappresentato dalla classe `ActorId` . `ActorId` è un valore ID opaco che può essere usato per la distribuzione uniforme degli attori nelle partizioni del servizio mediante la generazione di ID casuali:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```

Di ogni `ActorId` viene eseguito l'hashing in un Int64 ed è per questo motivo che il servizio Actor deve usare uno schema di partizionamento Int64 con l'intervallo di chiavi Int64 completo. È comunque possibile usare valori ID personalizzati per un `ActorID`, tra cui GUID, stringhe e Int64. 

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```

Quando si usano GUID e stringhe, viene eseguito l'hashing dei valori in un Int64. Quando invece si fornisce esplicitamente un Int64 a un `ActorId`, l'Int64 verrà mappato direttamente a una partizione senza ulteriore hashing. Questo consente di controllare in quale la partizione vengono inseriti gli attori.

## <a name="next-steps"></a>Passaggi successivi
* [Gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md)
* [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

