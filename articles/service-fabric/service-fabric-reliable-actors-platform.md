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
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 43b3f758fe7017c0ec949ba6e28b76438cf1bc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Modalità d'uso della piattaforma Service Fabric da parte di Reliable Actors
Questo articolo descrive il funzionamento di Reliable Actors sulla piattaforma Service Fabric di Azure. Reliable Actors viene eseguito in un framework ospitato in un'implementazione di un servizio Reliable Services con stato denominato *servizio attore*. Il servizio attore contiene tutti i componenti necessari per gestire il ciclo di vita e l'invio di messaggi per gli attori:

* Il runtime di Actors gestisce il ciclo di vita e la Garbage Collection e applica l'accesso a thread singolo.
* Un listener di comunicazione remota del servizio Actor accetta chiamate di accesso remoto per gli attori e le invia a un dispatcher per l'indirizzamento all'istanza d attore appropriata.
* Il provider di stato degli attori esegue il wrapping dei provider di stato (come il provider di stato Reliable Collections) e fornisce un adattatore per la gestione dello stato degli attori.

Questi componenti insieme costituiscono il framework Reliable Actors.

## <a name="service-layering"></a>Livelli del servizio
Dato che il servizio attore stesso è un servizio Reliable Services, tutti i concetti di [modello applicativo](service-fabric-application-model.md), ciclo di vita, [creazione pacchetti](service-fabric-package-apps.md), [distribuzione](service-fabric-deploy-remove-applications.md), aggiornamento e ridimensionamento validi per Reliable Services si applicano anche ai servizi attore.

![Livelli del servizio attore][1]

Il diagramma precedente mostra la relazione tra i framework applicativi di Service Fabric e il codice utente. Gli elementi blu rappresentano il framework applicativo di Reliable Services, quelli arancioni il framework Reliable Actors e quelli verdi il codice utente.

In Reliable Services il servizio eredita la classe `StatefulService`. Questa classe è a sua volta derivata da `StatefulServiceBase` (o `StatelessService` per i servizi senza stato). In Reliable Actors usare il servizio attore. Il servizio attore è un'implementazione diversa della classe `StatefulServiceBase` che implementa lo schema dell'attore in cui vengono eseguiti gli attori. Dato che il servizio attore è semplicemente un'implementazione di `StatefulServiceBase`, si può scrivere il proprio servizio che deriva da `ActorService` e implementare le funzionalità a livello di servizio così come si farebbe quando si eredita `StatefulService`, ad esempio:

* Backup e ripristino del servizio.
* Funzionalità condivisa per tutti gli attori, ad esempio un interruttore.
* Chiamate di routine remote sul servizio attore stesso e su ogni singolo attore.

> [!NOTE]
> I servizi con stato non sono attualmente supportati in Java/Linux.

### <a name="using-the-actor-service"></a>Uso del servizio attore
Le istanze degli attori hanno accesso al servizio attore in cui sono in esecuzione. Tramite il servizio attore, le istanze degli attori possono ottenere il contesto del servizio a livello di codice. Il contesto del servizio include l'ID partizione, il nome del servizio, il nome dell'applicazione e altre informazioni specifiche sulla piattaforma Service Fabric:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```


Come tutti i servizi Reliable Services, il servizio attore deve essere registrato con un tipo di servizio nel runtime di Service Fabric. Perché il servizio attore possa eseguire le istanze degli attori, è necessario che anche il proprio tipo di attore sia registrato con il servizio attore. Il metodo di registrazione `ActorRuntime` esegue questa attività per gli attori. Nel caso più semplice, è sufficiente registrare il tipo di attore e verrà usato implicitamente il servizio attore con le impostazioni predefinite:

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

In alternativa, è possibile usare un'espressione lambda fornita dal metodo di registrazione per creare manualmente il servizio attore. È possibile perciò configurare il servizio attore e costruire esplicitamente le istanze degli attori, in cui possono essere inserite le dipendenze per l'attore mediante il relativo costruttore:

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
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

### <a name="actor-service-methods"></a>Metodi del servizio attore
Il servizio attore implementa `IActorService` (C#) o `ActorService` (Java), che a sua volta implementa `IService` (C#) o `Service` (Java). Questo è l'interfaccia usata dalla comunicazione remota di Reliable Services, che consente le chiamate RPC sui metodi del servizio. Contiene i metodi a livello di servizio che possono essere chiamati in remoto mediante la comunicazione remota del servizio.

#### <a name="enumerating-actors"></a>Enumerazione degli attori
Il servizio attore consente al client di enumerare i metadati relativi agli attori ospitati dal servizio. Dato che il servizio attore è un servizio con stato partizionato, l'enumerazione viene eseguita per partizione. Poiché ogni partizione può contenere molti attori, l'enumerazione viene restituita come set di risultati a pagine. Le pagine vengono esaminate in ciclo fino a quando non vengono lette tutte. L'esempio seguente illustra come creare un elenco di tutti gli attori attivi in una partizione di un servizio Actor:

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

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>Eliminazione di attori
Il servizio attore fornisce anche una funzione per l'eliminazione degli attori:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Per altre informazioni sull'eliminazione degli attori e il relativo stato, vedere la [documentazione sul ciclo di vita degli attori](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Servizio attore personalizzato
Usando l'espressione lambda di registrazione dell'attore è possibile registrare il proprio servizio attore personalizzato che deriva da `ActorService` (C#) e `FabricActorService` (Java). In questo servizio attore personalizzato è possibile implementare funzionalità di livello di servizio scrivendo una classe di servizio che eredita `ActorService` (C#) o `FabricActorService` (Java). Un servizio attore personalizzato eredita tutte le funzionalità di runtime dell'attore da `ActorService` (C#) o `FabricActorService` (Java) e può essere usato per implementare i propri metodi del servizio.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
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
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

#### <a name="implementing-actor-backup-and-restore"></a>Implementazione del backup e ripristino dell'attore
 Nell'esempio seguente il servizio attore personalizzato espone un metodo per il backup dei dati dell'attore sfruttando il listener di comunicazione remota già presente in `ActorService`:

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
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


In questo esempio `IMyActorService` è un contratto di comunicazione remota che implementa `IService` (C#) and `Service` (Java) e viene successivamente implementato da `MyActorService`. Aggiungendo questo contratto di comunicazione remota, i metodi su `IMyActorService` ora sono disponibili anche per un client attraverso la creazione di un proxy di comunicazione remota mediante `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>Modello di applicazione
I servizi Actor sono servizi Reliable Services, per cui il modello applicativo è lo stesso. Tuttavia, gli strumenti di compilazione del framework attore generano automaticamente alcuni dei file del modello applicativo.

### <a name="service-manifest"></a>Manifesto del servizio
Gli strumenti di compilazione del framework attore generano automaticamente il contenuto del file ServiceManifest.xml del servizio attore. Questo file include:

* Tipo del servizio attore. Il nome del tipo viene generato in base al nome del progetto attore. In base all'attributo di persistenza nell'attore, viene impostato anche il flag HasPersistedState.
* Pacchetto di codice.
* Pacchetto di configurazione.
* Risorse ed endpoint.

### <a name="application-manifest"></a>Manifesto dell'applicazione
Gli strumenti di compilazione del framework attore creano automaticamente una definizione del servizio predefinito per il servizio Actor. Gli strumenti di compilazione popolano le proprietà del servizio predefinito:

* Il numero di set di repliche è determinato dall'attributo di persistenza sull'attore. Ogni volta che viene modificato l'attributo di persistenza nell'attore, viene reimpostato di conseguenza il numero di set di repliche nella definizione del servizio predefinito.
* Lo schema e l'intervallo della partizione vengono impostati su Uniform Int64 con l'intervallo di chiavi Int64 completo.

## <a name="service-fabric-partition-concepts-for-actors"></a>Concetti relativi alla partizione di Service Fabric per gli attori
I servizi Actor sono servizi con stato partizionati. Ogni partizione di un servizio Actor contiene un set di attori. Le partizioni del servizio vengono distribuite automaticamente su più nodi in Service Fabric. Le istanze degli attori vengono distribuite di conseguenza.

![Partizionamento e distribuzione degli attori][5]

È possibile creare servizi Reliable Services con schemi di partizione e intervalli di chiavi di partizione diversi. Il servizio attore usa lo schema di partizionamento Int64 con l'intervallo di chiavi Int64 completo per mappare gli attori alle partizioni.

### <a name="actor-id"></a>ID attore
A ogni attore creato nel servizio è associato un ID univoco, rappresentato dalla classe `ActorId` . `ActorId` è un valore ID opaco che può essere usato per la distribuzione uniforme degli attori nelle partizioni del servizio mediante la generazione di ID casuali:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Di ogni `ActorId` viene eseguito l'hashing in un Int64. Per questo motivo il servizio attore deve usare uno schema di partizionamento Int64 con l'intervallo di chiavi Int64 completo. È comunque possibile usare valori ID personalizzati per un `ActorID`, tra cui GUID/UUID, stringhe e Int64.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Quando si usano GUID/UUID e stringhe, viene eseguito l'hashing dei valori in un Int64. Quando invece si fornisce esplicitamente un Int64 a un `ActorId`, l'Int64 verrà mappato direttamente a una partizione senza ulteriore hashing. È possibile usare questa tecnica per controllare in quale partizione vengono inseriti gli attori.

## <a name="actor-using-remoting-v2-stack"></a>Attore che usa lo stack di comunicazione remota V2
Con il pacchetto NuGet 2.8, gli utenti possono usare lo stack V2 per la comunicazione remota, che è più efficiente e fornisce funzioni quali la serializzazione personalizzata. La comunicazione remota V2 non è compatibile con le versioni precedenti dello stack di comunicazione remota esistente, che è stato definito stack V1 di comunicazione remota.

Le modifiche seguenti sono necessarie per usare lo stack V2 di comunicazione remota.
 1. Aggiungere l'attributo assembly seguente nell'interfaccia dell'attore.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Compilare e aggiornare ActorService e i progetti client dell'attore per iniziare a usare lo stack V2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Aggiornamento del servizio Actor allo stack V2 di comunicazione remota senza compromettere la disponibilità del servizio.
Questa modifica sarà un aggiornamento in due passaggi. Seguire i passaggi nella sequenza elencata.

1.  Aggiungere l'attributo assembly seguente nell'interfaccia dell'attore. Questo attributo avvierà due listener per ActorService, il listener V1 esistente e il listener V2. Eseguire l'aggiornamento di ActorService con questa modifica.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Eseguire l'aggiornamento di ActorClients dopo aver completato l'aggiornamento precedente.
Questo passaggio garantisce che il proxy Actor usi lo stack V2 per la comunicazione remota.

3. Questo passaggio è facoltativo. Modificare l'attributo precedente per rimuovere il listener V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md)
* [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Codice di esempio Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
