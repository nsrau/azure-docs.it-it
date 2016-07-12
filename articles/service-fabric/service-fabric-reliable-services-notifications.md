<properties
   pageTitle="Notifiche di Reliable Services | Microsoft Azure"
   description="Documentazione concettuale per le notifiche di Reliable Services di Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/24/2016"
   ms.author="mcoskun"/>

# Notifiche di Reliable Services

Le notifiche consentono ai client di tenere traccia delle modifiche apportate all'oggetto di loro interesse. Esistono due tipi di oggetto che supportano la notifica: **Reliable State Manager** e **ReliableDictionary**.

Motivi frequenti per l'uso delle notifiche sono

- Compilazione di viste materializzate, ad esempio indici secondari o viste filtrate aggregate dello stato della replica. Ad esempio un indice ordinato di tutte le chiavi in un oggetto ReliableDictionary.
- Invio di dati di monitoraggio, ad esempio numero di utenti aggiunti nell'ultima ora.

Le notifiche vengono attivate come parte dell'applicazione dell'operazione. Dal momento che fanno parte di tale applicazione, è importante notare che la gestione delle notifiche deve essere completata quanto più velocemente possibile e che non deve essere eseguita alcuna operazione costosa in eventi sincroni.

## Notifiche di Reliable State Manager

Reliable State Manager prevede notifiche per gli eventi seguenti

- Transazione
    - Commit
- State Manager
    - Ricompilazione
    - Aggiunta di un oggetto Reliable State
    - Rimozione di un oggetto Reliable State

Reliable State Manager tiene traccia delle transazioni correnti in fase di elaborazione. L'unica modifica dello stato della transazione che causa l'attivazione di una notifica è il commit.

Reliable State Manager gestisce una raccolta di oggetti ReliableDictionary e Reliable Queue come Reliable State. Reliable State Manager attiva le notifiche quando viene modificata la raccolta con l'aggiunta o rimozione di un oggetto Reliable State o quando l'intera raccolta viene ricompilata. La raccolta di Reliable State Manager viene ricompilata in tre casi

- Recupero: al suo avvio, una replica recupera il suo stato precedente dal disco. Al termine del recupero, attiva un evento con **NotifyStateManagerChangedEventArgs** che contiene il set di **IReliableState** ripristinato.
- Copia completa: prima che una replica possa essere aggiunta al set di configurazione, deve essere compilata. In alcuni casi, potrebbe essere necessario applicare una copia completa dello stato di Reliable State Manager dalla replica primaria alla replica secondaria inattiva. Reliable State Manager sulla replica secondaria genera un evento con **NotifyStateManagerChangedEventArgs** che contiene il set di **IReliableState** acquisito dalla replica primaria.
- Ripristino: negli scenari di ripristino di emergenza lo stato della replica può essere ripristinato da un backup tramite **RestoreAsync**. In questi casi Reliable State Manager sulla replica primaria genera un evento con **NotifyStateManagerChangedEventArgs** che contiene il set di **IReliableState** ripristinato dal backup.

### Come usare le notifiche di Reliable State Manager
Per abilitare le notifiche delle transazioni e/o le notifiche di gestione dello stato, l'utente deve registrarsi con gli eventi **TransactionChanged** o **StateManagerChanged** sull'oggetto Reliable State Manager rispettivamente. La posizione frequente per la registrazione con questi gestori di eventi è il costruttore di StatefulService. Questo perché la registrazione sul costruttore garantisce al client di non perdere alcuna notifica derivante da una modifica durante il ciclo di vita di **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

EventHandler **TransactionChanged** usa **NotifyTransactionChangedEventArgs** per fornire dettagli sull'evento. Contiene la proprietà Action (ad esempio **NotifyTransactionChangedAction.Commit**) che specifica il tipo di modifica e la proprietà Transaction che fornisce un riferimento alla transazione modificata.

>[AZURE.NOTE] Oggi gli eventi TransactionChanged vengono generati solo in caso di commit della transazione, per cui Action sarà uguale a NotifyTransactionChangedAction.Commit. Tuttavia, sarà possibile aggiungere altre azioni negli aggiornamenti futuri. Pertanto, è consigliabile controllare la proprietà Action ed elaborare l'evento solo se previsto.

Di seguito è riportato un esempio di gestore di eventi **TransactionChanged**

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

EventHandler **StateManagerChanged** usa **NotifyStateManagerChangedEventArgs** per fornire dettagli sull'evento. **NotifyStateManagerChangedEventArgs** ha due sottoclassi: NotifyStateManagerRebuildEventArgs e NotifyStateManagerSingleEntityChangedEventArgs. La proprietà Action in **NotifyStateManagerChangedEventArgs** deve essere usata per eseguire il cast di **NotifyStateManagerChangedEventArgs** nella sottoclasse corretta

- NotifyStateManagerChangedAction.Rebuild: NotifyStateManagerRebuildEventArgs
- NotifyStateManagerChangedAction.Add e Remove: NotifyStateManagerSingleEntityChangedEventArgs

Di seguito è riportato un esempio di gestore delle notifiche **StateManagerChanged**

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## Notifiche di ReliableDictionary

ReliableDictionary prevede notifiche per gli eventi seguenti

- Ricompilazione: chiamata quando l'oggetto **ReliableDictionary** ha recuperato il suo stato dal disco locale, dalla copia di una replica primaria o dal backup.
- Cancellazione: chiamata quando lo stato di **ReliableDictionary** è stato cancellato tramite il metodo **ClearAsync**.
- Aggiunta: chiamata quando è stato aggiunto un elemento a **ReliableDictionary**.
- Aggiornamento: chiamata quando è stato aggiornato un elemento in **IReliableDictionary**.
- Eliminazione: chiamata quando è stato eliminato un elemento in **IReliableDictionary**.

### Come usare le notifiche di ReliableDictionary
Per abilitare le notifiche di ReliableDictionary, l'utente deve registrarsi con il gestore di eventi **DictionaryChanged** in **IReliableDictionary**. La posizione frequente per la registrazione con questi gestori di eventi è nella notifica di aggiunta **ReliableStateManager.StateManagerChanged**. La registrazione al momento dell'aggiunta di **IReliableDictionary** a **IReliableStateManager** garantisce che non verrà persa alcuna notifica.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] ProcessStateManagerSingleEntityNotification è il metodo di esempio chiamato dall'esempio OnStateManagerChangedHandler precedente.

Si noti che il codice precedente imposta IReliableNotificationAsyncCallback e **DictionaryChanged**. Poiché **NotifyDictionaryRebuildEventArgs** contiene **IAsyncEnumerable**, che richiede un'enumerazione asincrona, le notifiche di ricompilazione vengono attivate tramite **RebuildNotificationAsyncCallback** anziché **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] Nel codice precedente, come parte dell'elaborazione della notifica di ricompilazione, viene cancellato prima lo stato aggregato mantenuto. Infatti, poiché la raccolta Reliable Collection viene ricompilata con un nuovo stato, tutte le notifiche precedenti sono irrilevanti.

EventHandler **DictionaryChanged** usa **NotifyDictionaryChangedEventArgs** per fornire dettagli sull'evento. **NotifyDictionaryChangedEventArgs** ha cinque sottoclassi. La proprietà Action in **NotifyDictionaryChangedEventArgs** deve essere usata per eseguire il cast di **NotifyDictionaryChangedEventArgs** nella sottoclasse corretta

- NotifyDictionaryChangedAction.Rebuild: NotifyDictionaryRebuildEventArgs
- NotifyDictionaryChangedAction.Clear: NotifyDictionaryClearEventArgs
- NotifyDictionaryChangedAction.Add e Remove: NotifyDictionaryItemAddedEventArgs
- NotifyDictionaryChangedAction.Update: NotifyDictionaryItemUpdatedEventArgs
- NotifyDictionaryChangedAction.Remove: NotifyDictionaryItemRemovedEventArgs

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## Consigli

- COMPLETARE gli eventi notifiche più velocemente possibile.
- NON ESEGUIRE operazioni costose (ad esempio operazioni di I/O) come parte di eventi sincroni.
- CONTROLLARE il tipo Action prima di elaborare l'evento. In futuro sarà possibile aggiungere nuovi tipi di azione.

Occorre tenere presente i concetti seguenti:
- Le notifiche vengono attivate come parte dell'esecuzione di un'operazione. Ad esempio, come ultimo passaggio di un'operazione di ripristino verrà attivata la relativa notifica e il ripristino non sarà completato fino all'elaborazione dell'evento di notifica.
- Poiché le notifiche vengono attivate come parte dell'applicazione delle operazioni, i client visualizzeranno solo le notifiche per le operazioni con commit in locale. Si noti che poiché è garantito solo il commit in locale (in altri termini, la registrazione), potrebbe non essere possibile annullare le operazioni in futuro.
- Nel percorso di ripristino verrà attivata una singola notifica per ogni operazione applicata. Di conseguenza, se una transazione T1 include Create(X), Delete(X), Create(X), si riceverà una notifica per la creazione di X, una per l'eliminazione e una per una nuova creazione nell'ordine specificato.
- Per le transazioni che contengono più operazioni, queste verranno applicate nell'ordine in cui sono state ricevute nella replica primaria dall'utente.
- Come parte dell'elaborazione di un'incoerenza, alcune operazioni potrebbero essere annullate. Verranno generate notifiche relative a questo annullamento, con conseguente rollback dello stato della replica a un punto stabile. Una differenza importante delle notifiche di annullamento è che verranno aggregati eventi con chiavi duplicate. Ad esempio, se la transazione T1 precedente viene annullata, l'utente visualizzerà una singola notifica per Delete(X).

## Passaggi successivi

- [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
- [Eseguire il backup e il ripristino di Reliable Services (ripristino di emergenza)](service-fabric-reliable-services-backup-restore.md)
- [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0629_2016-->