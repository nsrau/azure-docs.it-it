---
title: Elimina Azure Service Fabric Actors
description: Informazioni su come eliminare manualmente e completamente Reliable Actors e il relativo stato in un'applicazione Service Fabric di Azure.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.custom: devx-track-csharp
ms.openlocfilehash: 80192aef564317e36fba56025aa31c787676d974
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006857"
---
# <a name="delete-reliable-actors-and-their-state"></a>Eliminare Reliable Actors e il relativo stato
L'operazione di Garbage Collection degli attori disattivati pulisce solo l'oggetto attore, ma non rimuove i dati archiviati in State Manager dell'attore. Quando un attore viene riattivato, i suoi dati vengono nuovamente resi disponibili tramite il gestore di stato. Nei casi in cui gli attori archiviano dati nel gestore di stato e vengono disattivati ma mai riattivati, può essere necessario pulire i relativi dati.

Il [servizio Actor](service-fabric-reliable-actors-platform.md) fornisce anche una funzione per l'eliminazione di attori da un chiamante remoto:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

L'eliminazione di un attore produce gli effetti seguenti, a seconda del fatto che l'attore sia attualmente attivo o meno:

* **Attore attivo**
  * L'attore viene rimosso dall'elenco di attori attivi e viene disattivato.
  * Il suo stato viene eliminato definitivamente.
* **Attore inattivo**
  * Il suo stato viene eliminato definitivamente.

Un attore non può chiamare un'operazione di eliminazione su se stesso da uno dei suoi metodi attore perché l'attore non può essere eliminato mentre è in esecuzione in un contesto di chiamata attore, in cui il runtime ha ottenuto un blocco per le chiamate dell'attore per applicare l'accesso a thread singolo.

Per altre informazioni su Reliable Actors, vedere:
* [Timer e promemoria degli attori](service-fabric-reliable-actors-timers-reminders.md)
* [Eventi Actor](service-fabric-reliable-actors-events.md)
* [Rientranza attore](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Documentazione di riferimento delle API di Actors](/previous-versions/azure/dn971626(v=azure.100))
* [Codice di esempio C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Codice di esempio Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
