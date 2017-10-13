---
title: Timer e promemoria di Reliable Actors | Microsoft Docs
description: Introduzione a timer e promemoria per Reliable Actors di Service Fabric
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 06b026ce06e0f16a77ac238de0af2263f272933c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="actor-timers-and-reminders"></a>Timer e promemoria degli attori
Gli attori possono pianificare il relativo lavoro periodico registrando timer o promemoria. Questo articolo illustra come usare timer e promemoria e ne spiega le differenze.

## <a name="actor-timers"></a>Timer degli attori
I timer degli attori forniscono un wrapper semplice intorno al timer .NET o Java per garantire che i metodi di callback rispettino le garanzie di concorrenza basata su turni offerte dal runtime di Actors.

Per eseguire e annullare la registrazione dei timer, gli attori possono usare i metodi `RegisterTimer`(C#) o `registerTimer`(Java) e `UnregisterTimer`(C#) o `unregisterTimer`(Java) nella propria classe base. L'esempio seguente illustra l'uso delle API di timer, che sono molto simili al timer .NET o al timer Java. In questo esempio, quando il timer è in scadenza, il runtime di Actors chiama il metodo `MoveObject`(C#) o `moveObject`(Java). Si garantisce che il metodo rispetti la concorrenza basata su turni. Ciò significa che nessun altro metodo di attori o callback di timer/promemoria sarà in azione fino al completamento dell'esecuzione del callback.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

Il periodo successivo del timer inizia dopo il completamento del callback. Pertanto, il timer viene arrestato mentre il callback è in esecuzione e viene avviato quando il callback è completato.

Il runtime di Actors salva le modifiche apportate alla gestione stati dell'attore al termine del callback. Se si verifica un errore durante il salvataggio dello stato, viene disattivato l'oggetto attore e viene attivata una nuova istanza.

Tutti i timer vengono arrestati quando l'attore viene disattivato come parte di garbage collection. In seguito, non viene richiamato nessun callback di timer. Inoltre, il runtime di Actors non mantiene alcuna informazione sui timer in esecuzione prima della disattivazione. È responsabilità dell'attore registrare gli eventuali timer che saranno necessari quando verrà riattivato in futuro. Per ulteriori informazioni, vedere la sezione sulla [garbage collection degli attori](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Promemoria degli attori
I promemoria sono un meccanismo per attivare i callback persistenti su un attore in base a orari specificati. La loro funzionalità è simile a quella dei timer. Tuttavia, a differenza dei timer, i promemoria vengono attivati in qualsiasi circostanza finché l'attore non ne annulla la registrazione in modo esplicito o finché l'attore non viene eliminato in modo esplicito. In particolare, i promemoria vengono attivati anche in caso di failover e disattivazione dell'attore perché il runtime di Actors rende persistenti le informazioni sui promemoria dell'attore.

Per registrare un promemoria, un attore chiama il metodo `RegisterReminderAsync` fornito nella classe base, come illustrato nell'esempio seguente.

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

In questo esempio `"Pay cell phone bill"` è il nome del promemoria. Questa è una stringa usata dall'attore per identificare in modo univoco un promemoria. `BitConverter.GetBytes(amountInDollars)`(C#) è il contesto associato al promemoria. Questo contesto verrà passato all'attore come argomento per il callback di promemoria, ad esempio `IRemindable.ReceiveReminderAsync`(C#) o `Remindable.receiveReminderAsync`(Java).

Gli attori che usano i promemoria devono implementare l'interfaccia `IRemindable` , come illustrato nell'esempio seguente.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Quando viene attivato un promemoria, il runtime di Reliable Actors richiama il metodo `ReceiveReminderAsync`(C#) o `receiveReminderAsync`(Java) sull'Actor. Un attore può registrare più promemoria e il metodo `ReceiveReminderAsync`(C#) o `receiveReminderAsync`(Java) viene richiamato ogni volta che tali promemoria vengono attivati. L'attore può usare il nome del promemoria che viene passato al metodo `ReceiveReminderAsync`(C#) o `receiveReminderAsync`(Java) per identificare il promemoria attivato.

Il runtime di Actors salva lo stato dell'attore al termine della chiamata a `ReceiveReminderAsync`(C#) o `receiveReminderAsync`(Java). Se si verifica un errore durante il salvataggio dello stato, viene disattivato l'oggetto attore e viene attivata una nuova istanza.

Per annullare la registrazione di un promemoria, un attore chiama il metodo `UnregisterReminderAsync`(C#) o `unregisterReminderAsync`(Java), come illustrato nell'esempio seguente.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Come mostrato in precedenza, il metodo `UnregisterReminderAsync`(C#) o `unregisterReminderAsync`(Java) accetta un'interfaccia `IActorReminder`(C#) o `ActorReminder`(Java). La classe base dell'attore supporta un metodo `GetReminder`(C#) o `getReminder`(Java) che può essere usato per recuperare l'interfaccia `IActorReminder`(C#) o `ActorReminder`(Java) passando il nome del promemoria. Questo metodo è utile perché l'attore non deve rendere persistente l'interfaccia `IActorReminder`(C#) o `ActorReminder`(Java) restituita dalla chiamata al metodo `RegisterReminder`(C#) o `registerReminder`(Java).

## <a name="next-steps"></a>Passaggi successivi
Acquisire informazioni sugli eventi e sulla rientranza di Reliable Actor:
* [Eventi relativi agli attori](service-fabric-reliable-actors-events.md)
* [Rientranza di Reliable Actors](service-fabric-reliable-actors-reentrancy.md)
