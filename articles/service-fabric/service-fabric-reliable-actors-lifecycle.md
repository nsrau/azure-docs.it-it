---
title: Panoramica del ciclo di vita dei microservizi Azure basati su attori | Documentazione Microsoft
description: Descrive il ciclo di vita di Service Fabric Reliable Actors, la Garbage Collection e l'eliminazione manuale di attori e del relativo stato
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/13/2017
ms.author: amanbha
ms.openlocfilehash: 75b7b77a0bef2051599a4f61183109cfb2ffff3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Ciclo di vita degli attori, Garbage Collection automatica ed eliminazione manuale
Un attore viene attivato la prima volta che viene effettuata una chiamata a uno dei suoi metodi. Un attore viene disattivato (tramite Garbage Collection del runtime di Actors) se rimane inutilizzato per un periodo di tempo configurabile. Un attore e il relativo stato possono essere eliminati manualmente in qualsiasi momento.

## <a name="actor-activation"></a>Attivazione di un attore
Quando un attore viene attivato, si verifica quanto segue:

* Quando viene effettuata una chiamata per un attore che non è ancora attivo, viene creato un nuovo attore.
* Viene caricato lo stato dell'attore, se viene mantenuto.
* Viene chiamato il metodo `OnActivateAsync` (C#) o `onActivateAsync` (Java), che può essere sottoposto a override nell'implementazione dell'attore.
* A questo punto l'attore è considerato attivo.

## <a name="actor-deactivation"></a>Disattivazione di un attore
Quando un attore viene disattivato, si verifica quanto segue:

* Quando un attore rimane inutilizzato per un determinato periodo di tempo, viene rimosso dalla tabella degli attori attivi.
* Viene chiamato il metodo `OnDeactivateAsync` (C#) o `onDeactivateAsync` (Java), che può essere sottoposto a override nell'implementazione dell'attore. Ciò elimina tutti i timer dell'attore. Le operazioni dell'attore come le modifiche di stato non devono essere chiamate da questo metodo.

> [!TIP]
> Il runtime di Fabric Actors emette alcuni [eventi relativi all'attivazione e alla disattivazione](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). che sono utili per la diagnostica e il monitoraggio delle prestazioni.
>
>

### <a name="actor-garbage-collection"></a>Garbage Collection degli attori
Quando un attore viene disattivato, i riferimenti all'oggetto corrispondente vengono rilasciati e l'attore può essere sottoposto a Garbage Collection dal garbage collector di Common Language Runtime (CLR) o di Java Virtual Machine (JVM). L'operazione di Garbage Collection pulisce solo l'oggetto attore, ma **non** rimuove lo stato archiviato nel gestore di stato dell'attore. Alla successiva attivazione dell'attore viene creato un nuovo oggetto attore e viene ripristinato il relativo stato.

Quali criteri determinano l'uso degli attori ai fini della disattivazione e dell'operazione di Garbage Collection?

* Ricezione di una chiamata
* `IRemindable.ReceiveReminderAsync` (applicabile solo se l'attore usa promemoria)

> [!NOTE]
> Se l'attore usa i timer e viene eseguito il callback di timer, questa azione **non** viene considerata per determinare l'uso dell'attore.
>
>

Prima di approfondire i concetti relativi alla disattivazione, è importante definire i termini seguenti:

* *Intervallo di analisi*. Intervallo di tempo in cui il runtime di Actors esegue l'analisi della tabella degli attori attivi per identificare quelli che possono essere disattivati e sottoposti a Garbage Collection. Il valore predefinito è 1 minuto.
* *Timeout di inattività*. Periodo di tempo in cui un attore deve rimanere inutilizzato (inattivo) prima di poter essere disattivato e sottoposto a Garbage Collection. Il valore predefinito è 60 minuti.

Non è in genere necessario modificare questi valori predefiniti. Se necessario, però, questi intervalli possono essere modificati tramite `ActorServiceSettings` durante la registrazione del [servizio Actor](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
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
    }
}
```
Per ogni attore attivo, il runtime di Actors tiene traccia del periodo di inattività. Il runtime di Actors controlla ogni attore in base alla frequenza definita da `ScanIntervalInSeconds` per verificare se può essere sottoposto a Garbage Collection e determina se l'attore è rimasto inattivo per il numero di secondi definito da `IdleTimeoutInSeconds`.

Ogni volta che un attore viene usato, il periodo di inattività viene reimpostato su 0. A questo punto, l'attore può essere sottoposto a Garbage Collection solo se rimane nuovamente inattivo per il numero di secondi definito da `IdleTimeoutInSeconds`. È importante ricordare che l'uso di un attore è determinato in base all'esecuzione di un metodo di interfaccia o di un callback di promemoria dell'attore. L'uso di un attore **non** è determinato in base all'esecuzione del callback dei timer.

Il diagramma seguente mostra il ciclo di vita di un unico attore per illustrare questi concetti.

![Esempio del tempo di inattività][1]

Nell'esempio viene illustrato l'impatto delle chiamate ai metodi degli attori, dei promemoria e dei timer sulla durata dell'attore. Per questo esempio è opportuno evidenziare i punti seguenti:

* ScanInterval e IdleTimeout sono impostati rispettivamente su 5 e 10. (Unità non è rilevante in questo caso, poiché l’obiettivo è solo di illustrare il concetto.)
* L'analisi degli attori da sottoporre a Garbage Collection viene eseguita in T=0,5,10,15,20,25, come definito dall’intervallo di analisi di 5.
* Viene attivato un timer periodico in T=4,8,12,16,20,24 e ne viene eseguito il callback. Questo non influisce sul tempo di inattività dell'attore.
* Una chiamata a un metodo dell'attore in T=7 reimposta il tempo di inattività su 0 ritardando l'operazione di Garbage Collection dell'attore.
* Un callback di promemoria dell'attore eseguito in T=14 ritarda ulteriormente l'operazione di Garbage Collection dell'attore.
* Durante l'analisi di Garbage Collection in T=25, il tempo di inattività dell'attore supera infine il timeout di inattività di 10 e l'attore viene sottoposto a Garbage Collection.

Un attore non viene mai sottoposto a Garbage Collection mentre è in esecuzione uno dei relativi metodi, indipendentemente dal tempo impiegato per l'esecuzione di tale metodo. Come accennato in precedenza, l'esecuzione dei metodi dell'interfaccia dell'attore e dei callback di promemoria impedisce l'operazione di Garbage Collection, reimpostando su 0 il tempo di inattività dell'attore. L'esecuzione di callback di timer non ha invece l'effetto di reimpostare su 0 il tempo di inattività. Tuttavia, l'operazione di Garbage Collection dell'attore viene rinviata finché il callback di timer non ha completato l'esecuzione.

## <a name="deleting-actors-and-their-state"></a>Eliminazione di attori e del relativo stato
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

Si noti che un attore non può chiamare un'operazione di eliminazione su se stesso da uno dei suoi metodi attore perché l'attore non può essere eliminato mentre è in esecuzione in un contesto di chiamata attore, in cui il runtime ha ottenuto un blocco per le chiamate dell'attore per applicare l'accesso a thread singolo.

## <a name="next-steps"></a>Passaggi successivi
* [Timer e promemoria degli attori](service-fabric-reliable-actors-timers-reminders.md)
* [Eventi relativi agli attori](service-fabric-reliable-actors-events.md)
* [Rientranza di Reliable Actors](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostica e monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio C#](https://github.com/Azure/servicefabric-samples)
* [Codice di esempio Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
