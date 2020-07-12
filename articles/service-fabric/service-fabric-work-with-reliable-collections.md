---
title: Lavorare con le raccolte Reliable Collections
description: Informazioni sulle procedure consigliate per l'uso di Reliable Collections all'interno di un'applicazione Service Fabric di Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 7df48bc0dfbef6fc85335801e64484914a218eb7
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255796"
---
# <a name="working-with-reliable-collections"></a>Lavorare con le raccolte Reliable Collections
Service Fabric offre un modello di programmazione con stato disponibile per gli sviluppatori .NET tramite Reliable Collections. In particolare, Service Fabric offre classi ReliableDictionary e ReliableQueue. Quando si usano queste classi, lo stato è partizionato (per la scalabilità), replicato (per la disponibilità) e le transazioni vengono eseguite all'interno di una partizione (per la semantica ACID). Verrà ora esaminato un utilizzo tipico di un oggetto Reliable Dictionary e ne verrà verificata la realtà.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

Tutte le operazioni sugli oggetti ReliableDictionary (ad eccezione di ClearAsync che non è annullabile) richiedono un oggetto ITransaction. Questo oggetto è associato a tutte le modifiche che si sta tentando di apportare a un dizionario Reliable e/o a oggetti coda affidabili all'interno di una singola partizione. Si acquisisce un oggetto ITransaction chiamando il metodo StateManager's CreateTransaction della partizione.

Nel codice precedente, l'oggetto ITransaction viene passato al metodo AddAsync di un dizionario reliable. Internamente, i metodi di dizionario che accettano una chiave acquisiscono un blocco di lettura/scrittura associato alla chiave. Se il metodo modifica il valore della chiave, il metodo accetta un blocco di scrittura sulla chiave e se il metodo legge solo dal valore della chiave, viene preso un blocco di lettura sulla chiave. Poiché AddAsync modifica il valore della chiave nel nuovo valore passato, viene assunto il blocco di scrittura della chiave. Pertanto, se due (o più) thread tentano di aggiungere i valori alla stessa chiave nello stesso momento, un thread acquisirà il blocco di scrittura e gli altri verranno bloccati. Per impostazione predefinita, i metodi si interrompono fino a 4 secondi per acquisire il blocco; dopo 4 secondi, i metodi generano un'eccezione TimeoutException. Gli overload dei metodi sono disponibili per consentire il passaggio di un valore di timeout esplicito, se si preferisce.

In genere, si scrive il codice per reagire a un'eccezione TimeoutException rilevandola e tentando di effettuare nuovamente l'intera operazione (come illustrato nel codice precedente). In questo semplice codice, si sta semplicemente chiamando Task. delay che passa 100 millisecondi ogni volta. In realtà, potrebbe essere più opportuno usare un tipo di ritardo backoff esponenziale.

Una volta acquisito il blocco, AddAsync aggiunge i riferimenti dell'oggetto valore e chiave a un dizionario interno temporaneo associato all'oggetto ITransaction. Questa operazione viene eseguita per fornire la semantica di autolettura delle proprie scritture. Ovvero, dopo avere chiamato AddAsync, una chiamata successiva a TryGetValueAsync usando lo stesso oggetto ITransaction restituirà il valore anche se non è stata ancora eseguito il commit della transazione.

> [!NOTE]
> Se si chiama TryGetValueAsync con una nuova transazione, viene restituito un riferimento all'ultimo valore di cui è stato eseguito il commit. Non modificare direttamente il riferimento, in quanto ignora il meccanismo per salvare in modo permanente e replicare le modifiche. Si consiglia di rendere i valori di sola lettura in modo che l'unico modo per modificare il valore di una chiave sia tramite le API Reliable Dictionary.

Successivamente, AddAsync serializza gli oggetti di chiave e valore in array di byte e aggiunge gli array a un file di log sul nodo locale. Infine, AddAsync invia gli array di byte di tutte le repliche secondarie in modo che abbiano le stesse informazioni chiave/valore. Anche se le informazioni chiave/valore sono stato scritte in un file di log, le informazioni non vengono considerate parte del dizionario fino a quando non è stato eseguito il commit della transazione a cui sono associate.

Nel codice precedente, la chiamata a CommitAsync consente di eseguire il commit di tutte le operazioni della transazione. In particolare, aggiunge informazioni di commit al file di log sul nodo locale e invia anche il record di commit a tutte le repliche secondarie. Una volta ricevuta la risposta da un quorum (maggioranza) delle repliche, tutte le modifiche ai dati vengono considerate permanenti e i blocchi associati alle chiavi modificate tramite l'oggetto ITransaction vengono rilasciati in modo che altri thread/transazioni possano modificare le stesse chiavi e i relativi valori.

Se CommitAsync non viene chiamato (in genere a causa di un'eccezione generata), l'oggetto ITransaction viene eliminato. Quando si elimina un oggetto ITransaction di cui non è stato eseguito il commit, Service Fabric aggiunge le informazioni di interruzione al file di log del nodo locale e non è necessario inviare nulla a nessuna delle repliche secondarie. A quel punto, i blocchi associati alle chiavi modificate tramite la transazione vengono rilasciati.

## <a name="volatile-reliable-collections"></a>Raccolte affidabili volatili 
In alcuni carichi di lavoro, come una cache replicata, ad esempio, può essere tollerata una perdita di dati occasionale. Evitare la persistenza dei dati su disco può consentire una migliore latenza e velocità effettiva durante la scrittura in dizionari affidabili. Il compromesso per la mancanza di persistenza è che se si verifica una perdita del quorum, si verificherà una perdita di dati completa. Poiché la perdita del quorum è un evento raro, le prestazioni più elevate potrebbero rivelarsi la rara possibilità di perdita di dati per tali carichi di lavoro.

Attualmente, il supporto volatile è disponibile solo per i dizionari affidabili e le code affidabili e non per ReliableConcurrentQueues. Vedere l'elenco delle [avvertenze](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) per indicare se usare le raccolte volatili.

Per abilitare il supporto volatile nel servizio, impostare il ```HasPersistedState``` flag nella dichiarazione del tipo di servizio su ```false``` , come segue:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>I servizi mantenuti esistenti non possono essere resi volatili e viceversa. Se si desidera eseguire questa operazione, sarà necessario eliminare il servizio esistente e quindi distribuire il servizio con il flag aggiornato. Ciò significa che è necessario essere disposti a comportare una perdita di dati completa se si desidera modificare il ```HasPersistedState``` flag. 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Inconvenienti comuni e come evitarli
Ora che si è appreso come funzionano internamente le raccolte Reliable Collections, verranno esaminate alcune applicazioni comuni. Osserviamo il seguente codice:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Quando si usa un normale dizionario .NET, è possibile aggiungere una coppia chiave/valore al dizionario e quindi modificare il valore di una proprietà (ad esempio LastLogin). Tuttavia, questo codice non funziona correttamente con un ReliableDictionary. Come visto in precedenza, la chiamata ad AddAsync serializza gli oggetti chiave/valore agli array di byte, salva gli array in un file locale e invia gli array anche alle repliche secondarie. Se successivamente si modifica una proprietà, il valore della proprietà verrà modificato solo in memoria. non ha alcun effetto sul file locale o sui dati inviati alle repliche. Se il processo si arresta in modo anomalo, la memoria viene eliminata. Quando viene avviato un nuovo processo o un'altra replica diventa primaria, è disponibile il valore della proprietà.

Non è possibile sottolineare a sufficienza quanto sia semplice effettuare l'errore descritto in alto. Sarà possibile apprendere l'errore solo se/quando il processo si interrompe. Il modo corretto per scrivere il codice è semplicemente invertire le due righe:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Ecco un altro esempio che mostra un errore comune:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Anche qui, con i dizionari regolari .NET, il codice indicato in alto funziona correttamente ed è un modello comune: lo sviluppatore usa una chiave per cercare un valore. Se il valore esiste, lo sviluppatore modifica il valore di una proprietà. Con le raccolte Reliable Collections, tuttavia, questo codice presenta lo stesso problema già illustrato: **non SI DEVE modificare un oggetto dopo averlo assegnato a una raccolta Reliable Collections.**

Il modo corretto per aggiornare un valore in una raccolta Reliable Collections è fare riferimento al valore esistente e tenere in considerazione l'oggetto a cui fa riferimento tramite il riferimento non modificabile. Creare quindi un nuovo oggetto come copia esatta dell'oggetto originale. A questo punto, è possibile modificare lo stato di questo nuovo oggetto e scrivere il nuovo oggetto nella raccolta in modo che venga serializzato in array di byte, aggiunto al file locale e inviato alle repliche. Dopo aver eseguito il commit delle modifiche, gli oggetti interni alla memoria, il file locale e tutte le repliche hanno lo stesso stato. Tutto è in posizione.

Il codice seguente illustra il modo corretto per aggiornare un valore in una raccolta Reliable Collections:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definire tipi di dati non modificabili per evitare errori del programmatore
Idealmente, si vuole che il compilatore segnali gli errori quando si genera accidentalmente codice che modifica lo stato di un oggetto che si dovrebbe considerare non modificabile. Tuttavia, il compilatore C# non è in grado di farlo. Pertanto, per evitare potenziali errori del programmatore, si consiglia di definire i tipi da usare con le raccolte Reliable Collections come tipi non modificabili. In particolare, questo significa che è opportuno fermarsi ai principali tipi di valore (ad esempio numeri [Int32, UInt64, etc.], DateTime, Guid, TimeSpan e simili). È anche possibile usare le stringhe. È preferibile evitare proprietà della raccolta poiché la serializzazione e la deserializzazione possono spesso influire negativamente sulle prestazioni. Tuttavia, se si desidera utilizzare le proprietà della raccolta, è consigliabile utilizzare. Libreria di raccolte non modificabili di NET ([System. Collections. Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Questa libreria è disponibile per il download da https://nuget.org . È inoltre consigliabile bloccare le classi e rendere i campi di sola lettura quando possibile.

Il tipo UserInfo riportato di seguito mostra come definire un tipo non modificabile sfruttando i consigli indicati in precedenza.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Anche ItemId è un tipo non modificabile, come illustrato di seguito:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Controllo delle versioni dello schema (aggiornamenti)
Internamente, le raccolte Reliable Collections serializzano gli oggetti usando. DataContractSerializer di NET. Gli oggetti serializzati vengono salvati in modo permanente nel disco locale della replica primaria e vengono trasmessi anche alle repliche secondarie. Con la maturità del servizio, è probabile che si desideri modificare il tipo di dati (schema) richiesto dal servizio. Eseguire il controllo delle versioni dei dati con estrema attenzione. Innanzitutto, si deve essere sempre in grado di deserializzare i dati precedenti. In particolare, ciò significa che il codice di deserializzazione deve essere infinitamente compatibile con le versioni precedenti: la versione 333 del codice del servizio deve essere in grado di operare sui dati inseriti in una raccolta Reliable Collections dalla versione 1 del codice del servizio 5 anni fa.

Il codice del servizio viene aggiornato un dominio di aggiornamento alla volta. Pertanto, durante un aggiornamento, vengono eseguite contemporaneamente due diverse versioni del codice del servizio. È necessario evitare che la nuova versione del codice del servizio usi il nuovo schema dal momento che le versioni precedenti del codice del servizio potrebbero non essere in grado di gestire il nuovo schema. Quando possibile, progettare ogni versione del servizio in modo che sia compatibile con la versione immediatamente successiva. In particolare, questo significa che la V1 del codice del servizio deve essere in grado di ignorare tutti gli elementi dello schema che non gestisce in modo esplicito. Tuttavia, deve essere in grado di salvare tutti i dati che non sono in grado di conoscere in modo esplicito e di scriverli di nuovo durante l'aggiornamento di una chiave o di un valore del dizionario.

> [!WARNING]
> Sebbene sia possibile modificare lo schema di una chiave, è necessario assicurarsi che il codice hash e gli algoritmi di uguaglianza della chiave siano stabili. Se si modifica il funzionamento di questi algoritmi, non sarà più possibile cercare la chiave all'interno del ReliableDictionary.
> È possibile usare le stringhe .NET come chiave, ma usare la stringa stessa come chiave. non usare il risultato di String. GetHashCode come chiave.

In alternativa, è possibile eseguire un aggiornamento noto come aggiornamento a due fasi. Con un aggiornamento in due fasi, si aggiorna il servizio da V1 a V2: V2 contiene il codice che sa come gestire la nuova modifica dello schema, ma questo codice non viene eseguito. Quando il codice V2 legge i dati V1, opera su di esso e scrive i dati V1. Quindi, al termine dell'aggiornamento di tutti i domini di aggiornamento, è possibile in qualche modo segnalare alle istanze V2 in esecuzione che l'aggiornamento è stato completato. Un modo per segnalare questo problema consiste nell'implementazione di un aggiornamento della configurazione. questo è ciò che rende un aggiornamento in due fasi. A questo punto, le istanze V2 possono leggere i dati V1, convertirli in dati V2, operare su di esso e scriverli come dati V2. Quando altre istanze leggono i dati V2, non è necessario convertirli: possono operano su di essi e scrivere dati V2.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla creazione di contratti di dati compatibili con versioni successive, vedere [Contratti di dati compatibili con versioni successive](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts)

Per informazioni sulle procedure consigliate per il controllo delle versioni dei contratti di dati, vedere [Controllo delle versioni dei contratti di dati](/dotnet/framework/wcf/feature-details/data-contract-versioning)

Per informazioni su come implementare contratti di dati a tolleranza di versione, vedere [Callback di serializzazione a tolleranza di versione](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks)

Per informazioni su come fornire una struttura di dati che possa interagire con più versioni, vedere [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject?view=netcore-3.1)
