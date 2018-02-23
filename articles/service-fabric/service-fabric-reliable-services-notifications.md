---
title: Notifiche di Reliable Services | Microsoft Docs
description: Documentazione concettuale per le notifiche di Reliable Services di Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: c080ee1f67b558988e35613f7357137c89c872fc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="reliable-services-notifications"></a>Notifiche di Reliable Services
Le notifiche consentono ai client di tenere traccia delle modifiche apportate a un oggetto a cui sono interessati. Le notifiche sono supportate da due tipi di oggetto: *Reliable State Manager* e *Reliable Dictionary*.

I motivi comuni per l'uso di notifiche sono i seguenti:

* Compilazione di viste materializzate, come indici secondari o visualizzazioni filtrate aggregate dello stato della replica. Un esempio è costituito da un indice ordinato di tutte le chiavi in un oggetto Reliable Dictionary.
* Invio di dati di monitoraggio, ad esempio il numero di utenti aggiunti nell'ultima ora.

Le notifiche vengono attivate come parte dell'applicazione dell'operazione. Per questo motivo, le notifiche devono essere gestite nel più breve tempo possibile e gli eventi sincroni non devono includere operazioni dispendiose.

## <a name="reliable-state-manager-notifications"></a>Notifiche di Reliable State Manager
Reliable State Manager prevede notifiche per gli eventi seguenti:

* Transazione
  * Commit
* State Manager
  * Ricompilazione
  * Aggiunta di uno stato affidabile
  * Rimozione di uno stato affidabile

Reliable State Manager tiene traccia delle transazioni correnti in fase di elaborazione. L'unica modifica dello stato della transazione che causa l'attivazione di una notifica è il commit.

Reliable State Manager gestisce una raccolta di stati affidabili come Reliable Dictionary e Reliable Queue. Reliable State Manager attiva le notifiche quando viene modificata la raccolta con l'aggiunta o rimozione di uno stato affidabile o la ricompilazione dell'intera raccolta.
La raccolta di Reliable State Manager viene ricompilata in tre casi.

* Recupero: quando viene avviata, una replica recupera il proprio stato precedente dal disco. Al termine del recupero, usa **NotifyStateManagerChangedEventArgs** per attivare un evento contenente il set di stati affidabili recuperati.
* Copia completa: prima che una replica possa essere aggiunta al set di configurazione, deve essere compilata. In alcuni casi, potrebbe essere necessario applicare una copia completa dello stato di Reliable State Manager dalla replica primaria alla replica secondaria inattiva. Reliable State Manager sulla replica secondaria usa **NotifyStateManagerChangedEventArgs** per attivare un evento contenente il set di stati affidabili acquisito dalla replica primaria.
* Ripristino: negli scenari di ripristino di emergenza, lo stato della replica può essere ripristinato da un backup tramite **RestoreAsync**. In questi casi, Reliable State Manager sulla replica primaria usa **NotifyStateManagerChangedEventArgs** per attivare un evento contenente il set di stati affidabili ripristinato dal backup.

Per abilitare le notifiche delle transazioni e/o le notifiche di gestione dello stato, è necessario registrarsi negli eventi **TransactionChanged** o **StateManagerChanged** in Reliable State Manager. Una posizione frequente per la registrazione in questi gestori eventi è il costruttore del servizio con stato. Con la registrazione sul costruttore, non si perde alcuna notifica causata da una modifica nel corso della durata di **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Il gestore eventi **TransactionChanged** usa **NotifyTransactionChangedEventArgs** per fornire dettagli sull'evento. Contiene la proprietà dell'azione (ad esempio, **NotifyTransactionChangedAction.Commit**) che specifica il tipo di modifica, nonché la proprietà della transazione che fornisce un riferimento alla transazione modificata.

> [!NOTE]
> Gli eventi **TransactionChanged** vengono attualmente generati solo in caso di commit della transazione. L'azione è quindi uguale a **NotifyTransactionChangedAction.Commit**. È tuttavia possibile che in futuro vengano generati eventi per altri tipi di modifica dello stato della transazione. È consigliabile controllare l'azione ed elaborare l'evento solo se previsto.
> 
> 

Di seguito è riportato un esempio del gestore eventi **TransactionChanged** .

```csharp
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

Il gestore eventi **StateManagerChanged** usa **NotifyStateManagerChangedEventArgs** per fornire dettagli sull'evento.
**NotifyStateManagerChangedEventArgs** ha due sottoclassi: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
La proprietà dell'azione in **NotifyStateManagerChangedEventArgs** viene usata per eseguire il cast di **NotifyStateManagerChangedEventArgs** nella sottoclasse corretta.

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** e **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Di seguito è riportato un esempio del gestore delle notifiche **StateManagerChanged** .

```csharp
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

## <a name="reliable-dictionary-notifications"></a>Notifiche di Reliable Dictionary
Reliable Dictionary prevede notifiche per gli eventi seguenti.

* Ricompilazione: chiamata quando l'oggetto **ReliableDictionary** ha recuperato il proprio stato da un backup o uno stato locale copiato o ripristinato.
* Cancellazione: chiamata quando lo stato di **ReliableDictionary** è stato cancellato tramite il metodo **ClearAsync**.
* Aggiunta: chiamata quando è stato aggiunto un elemento a **ReliableDictionary**.
* Aggiornamento: chiamata quando è stato aggiornato un elemento in **IReliableDictionary** .
* Rimozione: chiamata quando è stato eliminato un elemento in **IReliableDictionary** .

Per ricevere le notifiche di Reliable Dictionary, è necessario registrarsi nel gestore eventi **DictionaryChanged** in **IReliableDictionary**. Una posizione frequente per la registrazione in questi gestori eventi è la notifica di aggiunta **ReliableStateManager.StateManagerChanged** .
La registrazione al momento dell'aggiunta di **IReliableDictionary** a **IReliableStateManager** garantisce che non verrà persa alcuna notifica.

```csharp
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
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** è il metodo di esempio chiamato dall'esempio **OnStateManagerChangedHandler** precedente.
> 
> 

Il codice precedente imposta l'interfaccia **IReliableNotificationAsyncCallback** e **DictionaryChanged**. Poiché **NotifyDictionaryRebuildEventArgs** contiene un'interfaccia **IAsyncEnumerable**, che richiede un'enumerazione asincrona, le notifiche di ricompilazione vengono attivate tramite **RebuildNotificationAsyncCallback** anziché **OnDictionaryChangedHandler**.

```csharp
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

> [!NOTE]
> Nel codice precedente, nell'ambito dell'elaborazione della notifica di ricompilazione, viene cancellato prima lo stato aggregato mantenuto. Poiché la raccolta Reliable Collections viene ricompilata con un nuovo stato, tutte le notifiche precedenti sono irrilevanti.
> 
> 

Il gestore eventi **DictionaryChanged** usa **NotifyDictionaryChangedEventArgs** per fornire dettagli sull'evento.
**NotifyDictionaryChangedEventArgs** ha cinque sottoclassi. Usare la proprietà dell'azione in **NotifyDictionaryChangedEventArgs** per eseguire il cast di **NotifyDictionaryChangedEventArgs** nella sottoclasse corretta.

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** e **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
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

## <a name="recommendations"></a>Raccomandazioni
* *Completare* gli eventi di notifica nel più breve tempo possibile.
* *Non eseguire* operazioni dispendiose (ad esempio, operazioni di I/O) nell'ambito di eventi sincroni.
* *Controllare* il tipo di azione prima di elaborare l'evento. In futuro potrebbero essere aggiunti nuovi tipi di azione.

Occorre tenere presente i concetti seguenti:

* Le notifiche vengono attivate come parte dell'esecuzione di un'operazione. Una notifica di ripristino, ad esempio, viene attivata come ultimo passaggio di un'operazione di ripristino. Un ripristino non viene completato finché non viene elaborato l'evento di notifica.
* Poiché le notifiche vengono attivate nell'ambito dell'applicazione di operazioni, i client visualizzano solo le notifiche per le operazioni con commit in locale. Poiché è garantito solo il commit in locale (in altri termini, la registrazione), inoltre, potrebbe non essere possibile annullare le operazioni in futuro.
* Nel percorso di ripristino viene attivata una singola notifica per ogni operazione applicata. Di conseguenza, se la transazione T1 include Create(X), Delete(X) e Create(X), si riceverà una notifica per la creazione di X, una per l'eliminazione e una per una nuova creazione, nell'ordine specificato.
* Per le transazioni che contengono più operazioni, queste verranno applicate nell'ordine in cui sono state ricevute nella replica primaria dall'utente.
* Come parte dell'elaborazione di un'incoerenza, alcune operazioni potrebbero essere annullate. Per queste operazioni di annullamento vengono generate notifiche, con rollback dello stato della replica a un punto stabile. Una differenza importante delle notifiche di annullamento è che gli eventi con chiavi duplicate vengono aggregati. Se la transazione T1 viene annullata, ad esempio, viene visualizzata una singola notifica per Delete(X).

## <a name="next-steps"></a>Passaggi successivi
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Eseguire il backup e il ripristino di Reliable Services (ripristino di emergenza)](service-fabric-reliable-services-backup-restore.md)
* [Guida di riferimento per gli sviluppatori per Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

