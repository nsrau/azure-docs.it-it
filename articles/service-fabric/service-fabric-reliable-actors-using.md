---
title: Implementare le funzionalità negli attori di Azure Service Fabric | Microsoft Docs
description: Illustra come scrivere il proprio servizio Actor, implementando così funzionalità a livello di servizio nello stesso modo usato per ereditare StatefulService.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 57894770ad9d27430d5803c9a93ce6973355878a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123247"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementare le funzionalità a livello di servizio nel servizio Actor

Come descritto in [Livelli del servizio](service-fabric-reliable-actors-platform.md#service-layering), il servizio Actor stesso è un servizio affidabile. È possibile scrivere il proprio servizio che deriva da `ActorService`. È anche possibile implementare le funzionalità a livello di servizio nello stesso modo usato per ereditare un servizio con stato, ad esempio:

- Backup e ripristino del servizio.
- Funzionalità condivisa per tutti gli attori, ad esempio un interruttore.
- Chiamate di routine remote sul servizio attore stesso e su ogni singolo attore.

## <a name="use-the-actor-service"></a>Uso del servizio Actor

Le istanze degli attori hanno accesso al servizio attore in cui sono in esecuzione. Tramite il servizio attore, le istanze degli attori possono ottenere il contesto del servizio a livello di codice. Il contesto del servizio include l'ID partizione, il nome del servizio, il nome dell'applicazione e altre informazioni specifiche sulla piattaforma Service Fabric.

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

Come tutti i servizi Reliable Services, il servizio attore deve essere registrato con un tipo di servizio nel runtime di Service Fabric. Perché il servizio Actor possa eseguire le istanze degli attori, è necessario che anche il proprio tipo di attore sia registrato con il servizio Actor. Il metodo di registrazione `ActorRuntime` esegue questa attività per gli attori. Nel caso più semplice, è sufficiente registrare il tipo di attore e il servizio Actor userà le impostazioni predefinite.

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

In alternativa, è possibile usare un'espressione lambda fornita dal metodo di registrazione per creare manualmente il servizio attore. È quindi possibile configurare il servizio Actor e costruire esplicitamente le istanze degli attori. È possibile inserire le dipendenze per l'attore mediante il relativo costruttore.

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

Il servizio Actor implementa `IActorService` (C#) o `ActorService` (Java), che a sua volta implementa `IService` (C#) o `Service` (Java). Questa è l'interfaccia usata dalla comunicazione remota di Reliable Services, che consente di effettuare RPC (Remote Procedure Call) sui metodi di servizio. Contiene i metodi a livello di servizio che possono essere chiamati in remoto mediante la comunicazione remota del servizio. È possibile usarlo per [enumerare](service-fabric-reliable-actors-enumerate.md) ed [eliminare](service-fabric-reliable-actors-delete-actors.md) gli attori.


## <a name="custom-actor-service"></a>Servizio attore personalizzato

Usando l'espressione lambda di registrazione dell'attore è possibile registrare il proprio servizio attore personalizzato che deriva da `ActorService` (C#) e `FabricActorService` (Java). È quindi possibile implementare le proprie funzionalità a livello di servizio scrivendo una classe di servizio che erediti `ActorService` (C#) o `FabricActorService` (Java). Un servizio Actor personalizzato eredita tutte le funzionalità di runtime dell'attore da `ActorService` (C#) o `FabricActorService` (Java). Può essere usato per implementare i propri metodi di servizio.

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

## <a name="implement-actor-backup-and-restore"></a>Implementare il backup e ripristino dell'attore

Un servizio Actor personalizzato può esporre un metodo per il backup dei dati dell'attore sfruttando il listener di comunicazione remota già presente in `ActorService`. Per un esempio, vedere [Backup e ripristino di attori](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Attore che usa uno stack di comunicazione remota V2 (compatibile con l'interfaccia)

Lo stack di comunicazione remota V2 (compatibile con l'interfaccia, noto come V2_1) include tutte le funzionalità dello stack di comunicazione remota V2. La sua interfaccia è compatibile con lo stack di comunicazione remota V1, ma non è compatibile con V1 e V2. Per passare da V1 a V2_1 senza compromettere la disponibilità del servizio, seguire i passaggi della sezione successiva.

Per usare lo stack di comunicazione remota V2_1 sono necessarie le seguenti modifiche:

1. Aggiungere il seguente attributo assembly nelle interfacce dell'attore.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Compilare e aggiornare il servizio Actor e i progetti client dell'attore per iniziare a usare lo stack V2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Aggiornare il servizio Actor allo stack di comunicazione remota V2 (compatibile con l'interfaccia) senza compromettere la disponibilità del servizio

Questa modifica consiste in un aggiornamento in due passaggi. Seguire i passaggi descritti in questa sequenza.

1. Aggiungere il seguente attributo assembly nelle interfacce dell'attore. Questo attributo avvia due listener per il servizio Actor, il listener V1 (esistente) e il listener V2_1. Eseguire l'aggiornamento del servizio Actor con questa modifica.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Dopo aver completato l'aggiornamento precedente, aggiornare i client degli attori.
   Questo passaggio garantisce che il proxy Actor usi lo stack di comunicazione remota V2_1.

3. Questo passaggio è facoltativo. Modificare l'attributo precedente per rimuovere il listener V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Attore che usa lo stack di comunicazione remota V2

Con il pacchetto NuGet 2.8, gli utenti possono usare lo stack di comunicazione remota V2: è più efficiente e fornisce funzioni quali la serializzazione personalizzata. La comunicazione remota V2 non è compatibile lo stack di comunicazione remota esistente (ora definito stack di comunicazione remota V1).

Per usare lo stack di comunicazione remota V2, sono necessarie le seguenti modifiche.

1. Aggiungere il seguente attributo assembly nelle interfacce dell'attore.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Compilare e aggiornare il servizio Actor e i progetti client dell'attore per iniziare a usare lo stack V2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Aggiornare il servizio Actor allo stack di comunicazione remota V2 senza compromettere la disponibilità del servizio

Questa modifica consiste in un aggiornamento in due passaggi. Seguire i passaggi descritti in questa sequenza.

1. Aggiungere il seguente attributo assembly nelle interfacce dell'attore. Questo attributo avvia due listener per il servizio Actor, il listener V1 (esistente) e il listener V2. Eseguire l'aggiornamento del servizio Actor con questa modifica.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Dopo aver completato l'aggiornamento precedente, aggiornare i client degli attori.
   Questo passaggio garantisce che l'utilizzo dello stack di comunicazione remota V2 da parte del proxy Actor.

3. Questo passaggio è facoltativo. Modificare l'attributo precedente per rimuovere il listener V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md)
* [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Codice di esempio Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
