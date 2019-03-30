---
title: Reliable Actors in Service Fabric | Documentazione Microsoft
description: Descrive come Reliable Actors si sovrappone a Reliable Services e usa le funzionalità della piattaforma Service Fabric.
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
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: bc7569c9f230abb7677a8df9fc0cc0268e57296f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661174"
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

Per altre informazioni, vedere [Implementazione di funzionalità a livello di servizio nel servizio Actor](service-fabric-reliable-actors-using.md).

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


## <a name="next-steps"></a>Passaggi successivi
* [Gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md)
* [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Documentazione di riferimento delle API di Actors](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Codice di esempio .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Codice di esempio Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
