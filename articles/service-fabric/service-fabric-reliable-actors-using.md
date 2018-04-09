---
title: Implementazione di funzionalità in Azure Service Fabric Actors | Microsoft Docs
description: Questo articolo illustra come scrivere il proprio servizio Actor che implementa funzionalità a livello di servizio così come si farebbe quando si eredita StatefulService.
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
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 60989825ecdefa853d0e2df99619e3cb350cb6bc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementazione di funzionalità a livello di servizio nel servizio Actor
Come descritto in [Livelli del servizio](service-fabric-reliable-actors-platform.md#service-layering), il servizio Actor stesso è un servizio affidabile.  È possibile scrivere il proprio servizio che deriva da `ActorService` e implementare funzionalità a livello di servizio così come si farebbe quando si eredita StatefulService, ad esempio:

- Backup e ripristino del servizio.
- Funzionalità condivisa per tutti gli attori, ad esempio un interruttore.
- Chiamate di routine remote sul servizio attore stesso e su ogni singolo attore.

## <a name="using-the-actor-service"></a>Uso del servizio attore
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

## <a name="actor-service-methods"></a>Metodi del servizio attore
Il servizio attore implementa `IActorService` (C#) o `ActorService` (Java), che a sua volta implementa `IService` (C#) o `Service` (Java). Questo è l'interfaccia usata dalla comunicazione remota di Reliable Services, che consente le chiamate RPC sui metodi del servizio. Contiene i metodi a livello di servizio che possono essere chiamati in remoto mediante la comunicazione remota con il servizio e consente di [enumerare](service-fabric-reliable-actors-enumerate.md) ed [eliminare](service-fabric-reliable-actors-delete-actors.md) gli attori.


## <a name="custom-actor-service"></a>Servizio attore personalizzato
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

## <a name="implementing-actor-backup-and-restore"></a>Implementazione del backup e ripristino dell'attore
Un servizio Actor personalizzato può esporre un metodo per il backup dei dati dell'attore sfruttando il listener di comunicazione remota già presente in `ActorService`.  Per un esempio, vedere [Backup e ripristino di attori](service-fabric-reliable-actors-backup-and-restore.md).

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
