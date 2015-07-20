<properties
   pageTitle="Timer e promemoria di Azure Service Fabric Actors"
   description="Introduzione ai timer e ai promemoria di Azure Service Fabric Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/17/2015"
   ms.author="amanbha"/>


# Timer degli attori
I timer degli attori forniscono un wrapper semplice attorno al timer .NET per fare in modo che i metodi di callback rispettino le garanzie di concorrenza basata su turni offerte dal runtime di Actors.

Per eseguire e annullare la registrazione dei timer, gli attori possono usare i metodi `RegisterTimer` e `UnregisterTimer` nella propria classe base. L'esempio seguente illustra l'uso delle API di timer, che sono molto simili al timer .NET. Nell'esempio, alla scadenza del timer, il runtime di Actors chiama il metodo `MoveObject` che rispetta la concorrenza basata su turni. Questo significa che nessun altro metodo dell'attore o callback di timer/promemoria viene eseguito fino al completamento dell'esecuzione di questo callback.

```csharp
class VisualObjectActor : Actor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    public override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // callback method
            state,                          // state to pass to callback method
            TimeSpan.FromMilliseconds(15),  // amount of time to delay before callback is invoked
            TimeSpan.FromMilliseconds(15)); // time interval between invocation of the callback method

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
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
        return TaskDone.Done;
    }
}
```

Il periodo successivo del timer inizia dopo il completamento del callback. Pertanto, il timer viene arrestato mentre il callback è in esecuzione e viene avviato quando il callback è completato.

Se l'attore è con stato, come nell'esempio precedente, il runtime di Actors salva lo stato dell'attore al completamento del callback. Se si verifica un errore durante il salvataggio dello stato, viene disattivato l'oggetto attore e viene attivata una nuova istanza. Un metodo di callback che non modifica lo stato dell'attore può essere registrato come callback di timer di sola lettura specificando l'attributo Readonly nel callback, come descritto nella sezione [Metodi di sola lettura](service-fabric-reliable-actors-introduction.md#readonly-methods).

Tutti i timer vengono arrestati quando l'attore viene disattivato nell'ambito dell'operazione di Garbage Collection e nessun callback di timer viene richiamato dopo tale operazione. Inoltre, il runtime di Actors non mantiene alcuna informazione sui timer in esecuzione prima della disattivazione. È responsabilità dell'attore registrare gli eventuali timer che saranno necessari quando verrà riattivato in futuro. Per altre informazioni, vedere la sezione [Garbage Collection degli attori](service-fabric-reliable-actors-lifecycle.md).

## Promemoria degli attori
I promemoria offrono un meccanismo per attivare i callback persistenti su un attore in base a orari specificati. Questa funzionalità è simile ai timer, ma i promemoria, diversamente dai timer, vengono attivati in qualsiasi circostanza finché l'attore non ne annulla esplicitamente la registrazione. In particolare, i promemoria vengono attivati anche in caso di failover e disattivazione dell'attore perché il runtime di Actors rende persistenti le informazioni sui promemoria dell'attore.

I promemoria sono supportati solo per gli attori con stato. Non possono essere usati dagli attori senza stato. I provider di stato degli attori sono responsabili dell'archiviazione delle informazioni sui promemoria registrate dagli attori.

Per registrare un promemoria, un attore chiama il metodo `RegisterReminder` fornito nella classe base, come illustrato nell'esempio seguente.

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

In questo esempio, `"Pay cell phone bill"` è il nome del promemoria, ossia la stringa usata dall'attore per identificare in modo univoco il promemoria, mentre `BitConverter.GetBytes(amountInDollars)` è il contesto associato al promemoria. Questo contesto verrà passato all'attore come argomento per il callback di promemoria, ad esempio `IRemindable.ReceiveReminderAsync`.

Gli attori che usano i promemoria devono implementare l'interfaccia `IRemindable`, come illustrato nell'esempio seguente.

```csharp
public class ToDoListActor : Actor<ToDoList>, IToDoListActor, IRemindable
{
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

Quando viene attivato un promemoria, il runtime di Fabric Actors richiama il metodo `ReceiveReminderAsync` sull'attore. Un attore può registrare più promemoria e il metodo `ReceiveReminderAsync` viene richiamato ogni volta che tali promemoria vengono attivati. L'attore può usare il nome del promemoria che viene passato al metodo `ReceiveReminderAsync` per identificare il promemoria attivato.

Il runtime di Actors salva lo stato dell'attore quando la chiamata a `ReceiveReminderAsync` viene completata. Se si verifica un errore durante il salvataggio dello stato, viene disattivato l'oggetto attore e viene attivata una nuova istanza. Per specificare che lo stato non deve essere salvato al completamento del callback di promemoria, è possibile impostare il flag `ActorReminderAttributes.ReadOnly` nel parametro `attributes` quando viene chiamato il metodo `RegisterReminder` per creare il promemoria.

Per annullare la registrazione di un promemoria, è necessario chiamare il metodo `UnregisterReminder`, come illustrato nell'esempio seguente.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Come indicato nell'esempio, il metodo `UnregisterReminder` accetta un'interfaccia `IActorReminder`. La classe base dell'attore supporta un metodo `GetReminder` che può essere usato per recuperare l'interfaccia `IActorReminder` passando il nome del promemoria. Questo metodo è utile perché l'attore non deve rendere persistente l'interfaccia `IActorReminder` restituita dalla chiamata al metodo `RegisterReminder`.
 

<!---HONumber=July15_HO2-->