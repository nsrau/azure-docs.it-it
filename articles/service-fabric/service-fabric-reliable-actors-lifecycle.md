<properties
   pageTitle="Ciclo di vita di Reliable Actors | Microsoft Azure"
   description="Informazioni sul ciclo di vita e sull'operazione di Garbage Collection per Service Fabric Reliable Actors"
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
   ms.date="08/05/2015"
   ms.author="amanbha"/>


# Ciclo di vita degli attori e Garbage Collection
Un attore viene attivato alla prima chiamata effettuata. Un attore viene disattivato (tramite Garbage Collection del runtime di Actors) se rimane inutilizzato per un determinato periodo di tempo. Per configurare questo periodo, vedere più avanti la sezione Garbage Collection degli attori.

Cosa accade al momento dell'attivazione di un attore?

- Quando viene effettuata una chiamata per un attore che non è ancora attivo, viene creato un nuovo attore.
- Viene caricato lo stato dell'attore (se si tratta di un attore con stato).
- Viene chiamato il metodo `OnActivateAsync` (che può essere sottoposto a override nell'implementazione dell'attore).
- L'attore viene aggiunto a una tabella degli attori attivi.

Cosa accade al momento della disattivazione di un attore?

- Quando un attore rimane inutilizzato per un determinato periodo di tempo, viene rimosso dalla tabella degli attori attivi.
- Viene chiamato il metodo `OnDeactivateAsync` (che può essere sottoposto a override nell'implementazione dell'attore). Ciò elimina tutti i timer dell'attore.

> [AZURE.TIP]Il runtime di Fabric Actors emette alcuni [eventi relativi all'attivazione e alla disattivazione degli attori](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events), che sono utili per la diagnostica e il monitoraggio delle prestazioni.

## Garbage Collection degli attori
Il runtime di Actors esegue periodicamente un'analisi per identificare gli attori che sono rimasti inutilizzati per un determinato periodo di tempo e li disattiva. Una volta disattivati, gli attori possono essere sottoposti a garbage collection da Common Language Runtime (CLR).

Quali criteri determinano l'uso degli attori ai fini dell'operazione di Garbage Collection?

- Ricezione di una chiamata
- Chiamata al metodo `IRemindable.ReceiveReminderAsync` (applicabile solo se l'attore usa promemoria)

Si noti che, se l'attore usa dei timer e viene eseguito il callback dei timer, questa azione **non** viene considerata per determinare l'uso dell'attore.

Prima di approfondire i concetti relativi all'operazione di Garbage Collection, è importante definire i termini seguenti:

- *Intervallo di analisi*. Intervallo di tempo in cui il runtime di Actors esegue l'analisi della tabella degli attori attivi per identificare quelli che possono essere sottoposti a Garbage Collection. Il valore predefinito è 1 minuto.
- *Timeout di inattività*. Pperiodo di tempo in cui un attore deve rimanere inutilizzato (inattivo) prima di poter essere sottoposto a Garbage Collection. Il valore predefinito è 60 minuti.

Non è in genere necessario modificare questi valori predefiniti. Tuttavia, questi intervalli possono essere modificati a livello di assembly per tutti i tipi di attore in tale assembly o a livello di tipo di attore mediante l'attributo `ActorGarbageCollection`. L'esempio seguente illustra come modificare gli intervalli di Garbage Collection per HelloActor.

```csharp
[ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

Per modificare il valore predefinito dell'attributo `ActorGarbageCollection` a livello di assembly, aggiungere il frammento di codice seguente in `AssemblyInfo.cs`.

```csharp
[assembly: ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
```

Per ogni attore incluso nella tabella degli attori attivi, il runtime di Actors tiene traccia del periodo di inattività. Il runtime di Actors controlla ogni attore in base alla frequenza definita da `ScanIntervalInSeconds` per verificare se può essere sottoposto a Garbage Collection e determina se l'attore è rimasto inattivo per il numero di secondi definito da `IdleTimeoutInSeconds`.

Ogni volta che un attore viene usato, il periodo di inattività viene reimpostato su 0. A questo punto, l'attore può essere sottoposto a Garbage Collection solo se rimane nuovamente inattivo per il numero di secondi definito da `IdleTimeoutInSeconds`. È importante ricordare che l'uso di un attore è determinato in base all'esecuzione di un metodo di interfaccia o di un callback di promemoria dell'attore, ma **non** in base all'esecuzione di callback di timer.

Il diagramma seguente contiene un esempio per illustrare questi concetti.

![Esempio del tempo di inattività][1]

Nell'esempio si presuppone che esista un solo attore nella tabella degli attori attivi. Nell'esempio viene illustrato l'impatto delle chiamate ai metodi degli attori, dei promemoria e dei timer sulla durata dell'attore. Per questo esempio è opportuno evidenziare i punti seguenti:

- ScanInterval e IdleTimeout sono impostati rispettivamente su 5 e 10. (Unità non è rilevante in questo caso, poiché l’obiettivo è solo di illustrare il concetto.)
- L'analisi degli attori da sottoporre a Garbage Collection viene eseguita in T=0,5,10,15,20,25, come definito dall’intervallo di analisi di 5.
- Viene attivato un timer periodico in T=4,8,12,16,20,24 e ne viene eseguito il callback. Questo non influisce sul tempo di inattività dell'attore.
- Una chiamata a un metodo dell'attore in T=7 reimposta il tempo di inattività su 0 ritardando l'operazione di Garbage Collection dell'attore.
- Un callback di promemoria dell'attore eseguito in T=14 ritarda ulteriormente l'operazione di Garbage Collection dell'attore.
- Durante l'analisi di Garbage Collection in T=25, il tempo di inattività dell'attore supera infine il timeout di inattività di 10 e l'attore viene sottoposto a Garbage Collection.

Si noti che un attore non viene mai sottoposto a Garbage Collection mentre è in esecuzione uno dei relativi metodi, indipendentemente dal tempo impiegato per l'esecuzione di tale metodo. Come accennato in precedenza, l'esecuzione dei metodi dell'interfaccia dell'attore e dei callback di promemoria impedisce l'operazione di Garbage Collection, reimpostando su 0 il tempo di inattività dell'attore. L'esecuzione di callback di timer non ha invece l'effetto di reimpostare su 0 il tempo di inattività. Tuttavia, l'operazione di Garbage Collection dell'attore viene rinviata finché il callback di timer non ha completato l'esecuzione.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=AcomDC_1217_2015-->