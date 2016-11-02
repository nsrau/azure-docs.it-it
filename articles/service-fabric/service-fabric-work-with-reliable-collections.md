<properties
    pageTitle="Lavorare con le raccolte Reliable Collections | Microsoft Azure"
    description="Procedure consigliate per lavorare con le raccolte Reliable Collections."
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# Lavorare con le raccolte Reliable Collections

Service Fabric offre un modello di programmazione con stato disponibile per gli sviluppatori .NET tramite Reliable Collections. In particolare, Service Fabric offre classi ReliableDictionary e ReliableQueue. Quando si usano queste classi, lo stato è partizionato (per la scalabilità), replicato (per la disponibilità) e le transazioni vengono eseguite all'interno di una partizione (per la semantica ACID). Di seguito viene descritto l'uso tipico di un oggetto ReliableDictionary per osservarne le azioni.

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

Tutte le operazioni sugli oggetti ReliableDictionary (ad eccezione di ClearAsync che non è annullabile) richiedono un oggetto ITransaction. Questo oggetto è associato a tutte le modifiche che si tenta di apportare a qualsiasi oggetto ReliableDictionary e/o ReliableQueue all'interno di una singola partizione. Acquisire un oggetto ITransaction chiamando il metodo CreateTransaction della partizione "StateManager".
 
Nel codice precedente, l'oggetto ITransaction viene passato al metodo AddAsync dell'oggetto ReliableDictionary. Internamente, i metodi di dizionario che accettano una chiave acquisiscono un blocco di lettura/scrittura associato alla chiave. Se il metodo modifica il valore della chiave, il metodo acquisisce un blocco di scrittura sulla chiave; se il metodo legge solo dal valore della chiave, allora acquisisce un blocco di lettura sulla chiave. Poiché AddAsync modifica il valore della chiave sul nuovo valore ottenuto, viene acquisito il blocco di scrittura della chiave. Pertanto, se 2 (o più) thread tentano di aggiungere i valori alla stessa chiave nello stesso momento, un thread acquisirà il blocco di scrittura e gli altri verranno bloccati. Per impostazione predefinita, i metodi si interrompono fino a 4 secondi per acquisire il blocco; dopo 4 secondi, i metodi generano un'eccezione TimeoutException. Se si preferisce, esistono overload del metodo che consentono di superare un valore di timeout esplicito.
 
In genere, si scrive il codice per reagire a un'eccezione TimeoutException rilevandola e tentando di effettuare nuovamente l'intera operazione (come illustrato nel codice precedente). In questo codice semplice, si sta chiamando ogni volta Task.Delay oltre i 100 millisecondi. In realtà, potrebbe essere più opportuno usare un tipo di ritardo backoff esponenziale.
 
Una volta acquisito il blocco, AddAsync aggiunge i riferimenti dell'oggetto valore e chiave a un dizionario interno temporaneo associato all'oggetto ITransaction. Questa operazione viene eseguita per fornire la semantica di autolettura delle proprie scritture. Vale a dire che, dopo aver chiamato AddAsync, una chiamata successiva a TryGetValueAsync (usando lo stesso oggetto ITransaction) restituirà il valore anche se non si è eseguito il commit della transazione. Successivamente, AddAsync serializza gli oggetti di chiave e valore in array di byte e aggiunge gli array a un file di log sul nodo locale. Infine, AddAsync invia gli array di byte di tutte le repliche secondarie in modo che abbiano le stesse informazioni chiave/valore. Anche se le informazioni chiave/valore sono stato scritte in un file di log, le informazioni non vengono considerate parte del dizionario fino a quando non è stato eseguito il commit della transazione a cui sono associate.

Nel codice precedente, la chiamata a CommitAsync esegue il commit di tutte le operazioni della transazione. In particolare, aggiunge informazioni di commit al file di log sul nodo locale e invia anche il record di commit a tutte le repliche secondarie. Una volta ricevuta la risposta da un quorum (maggioranza) delle repliche, tutte le modifiche ai dati vengono considerate permanenti e i blocchi associati alle chiavi modificate tramite l'oggetto ITransaction vengono rilasciati in modo che altri thread/transazioni possano modificare le stesse chiavi e i relativi valori.

Se CommitAsync non viene chiamato (in genere a causa di un'eccezione generata), l'oggetto ITransaction viene eliminato. Quando viene eliminato un oggetto ITransaction su cui non è stato eseguito il commit, Service Fabric aggiunge informazioni sull'interruzione al file di log del nodo locale e non è necessario inviare alcun elemento alle repliche secondarie. A quel punto, i blocchi associati alle chiavi modificate tramite la transazione vengono rilasciati.

## Inconvenienti comuni e come evitarli
Dopo averne appreso il funzionamento interno, ecco alcuni casi di uso improprio delle Reliable Collections. Osserviamo il seguente codice:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

Quando si usa un normale dizionario .NET, è possibile aggiungere una coppia chiave/valore al dizionario e quindi modificare il valore di una proprietà (ad esempio LastLogin). Tuttavia, questo codice non funziona correttamente con un ReliableDictionary. Come visto in precedenza, la chiamata ad AddAsync serializza gli oggetti chiave/valore agli array di byte, salva gli array in un file locale e invia gli array anche alle repliche secondarie. Se successivamente si modifica una proprietà, questa operazione modifica il valore della proprietà solo nella memoria, senza influire sul file locale o sui dati inviati alle repliche. Se il processo si arresta in modo anomalo, il contenuto della memoria viene eliminato. Quando viene avviato un nuovo processo o un'altra replica diventa primaria, è disponibile il valore della proprietà.

Non è possibile sottolineare a sufficienza quanto sia semplice effettuare l'errore descritto in alto. Sarà possibile apprendere l'errore solo se/quando il processo si interrompe. Il modo corretto per scrivere il codice è semplicemente invertire le due righe:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

Ecco un altro esempio che mostra un errore comune:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

Anche qui, con i dizionari regolari .NET, il codice indicato in alto funziona correttamente ed è un modello comune: lo sviluppatore usa una chiave per cercare un valore. Se il valore esiste, lo sviluppatore modifica un valore della proprietà. Con le raccolte Reliable Collections, tuttavia, questo codice presenta lo stesso problema già illustrato: __non SI DEVE modificare un oggetto dopo averlo assegnato a una raccolta Reliable Collections.__
 
Il modo corretto per aggiornare un valore in una raccolta Reliable Collections è fare riferimento al valore esistente e tenere in considerazione l'oggetto a cui fa riferimento tramite il riferimento non modificabile. Quindi, creare un nuovo oggetto come copia esatta dell'oggetto originale. A questo punto, è possibile modificare lo stato di questo nuovo oggetto e scrivere il nuovo oggetto nella raccolta in modo che venga serializzato in array di byte, aggiunto al file locale e inviato alle repliche. Dopo aver eseguito il commit delle modifiche, gli oggetti interni alla memoria, il file locale e tutte le repliche hanno lo stesso stato. Tutto è in posizione.

Il codice seguente illustra il modo corretto per aggiornare un valore in una raccolta Reliable Collections:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## Definire tipi di dati non modificabili per evitare errori del programmatore

Idealmente, il compilatore dovrebbe segnalare gli errori quando si crea inavvertitamente codice che modifica lo stato di un oggetto considerato non modificabile. Tuttavia, il compilatore C# non è in grado di farlo. Pertanto, per evitare potenziali errori del programmatore, si consiglia di definire i tipi da usare con le raccolte Reliable Collections come tipi non modificabili. In particolare, questo significa che è opportuno fermarsi ai principali tipi di valore (ad esempio numeri [Int32, UInt64, etc.], DateTime, Guid, TimeSpan e simili). E, ovviamente, è anche possibile usare le stringhe. È preferibile evitare proprietà della raccolta poiché la serializzazione e la deserializzazione può spesso influire negativamente sulle prestazioni. Tuttavia, se si desidera usare le proprietà della raccolta, si consiglia l'uso di libreria di raccolte .NET non modificabili (System.Collections.Immutable). Questa libreria è disponibile per il download all'indirizzo http://nuget.org. Si consiglia anche di sigillare le classi e rendere i campi di sola lettura quando possibile.

Il tipo UserInfo riportato di seguito mostra come definire un tipo non modificabile sfruttando i consigli indicati in precedenza.

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
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
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

Anche ItemId è un tipo non modificabile, come illustrato di seguito:

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## Controllo delle versioni dello schema (aggiornamenti)

Internamente, le raccolte Reliable Collections serializzano gli oggetti usando DataContractSerializer di .NET. Gli oggetti serializzati sono persistenti sul disco locale della replica primaria e vengono anche trasmessi alle repliche secondarie. Con l'evoluzione del servizio, è probabile che si desideri modificare il tipo di dati (schema) richiesti dal servizio. Il controllo delle versioni dei dati deve essere eseguito con estrema attenzione. Innanzitutto, si deve essere sempre in grado di deserializzare i dati precedenti. In particolare, ciò significa che il codice di deserializzazione deve essere infinitamente compatibile con le versioni precedenti: la versione 333 del codice del servizio deve essere in grado di operare sui dati inseriti in una raccolta Reliable Collections dalla versione 1 del codice del servizio 5 anni fa.

Il codice del servizio viene aggiornato un dominio di aggiornamento alla volta. Pertanto, durante un aggiornamento, vengono eseguite contemporaneamente due diverse versioni del codice del servizio. È necessario evitare che la nuova versione del codice del servizio usi il nuovo schema dal momento che le versioni precedenti del codice del servizio potrebbero non essere in grado di gestire il nuovo schema. Quando possibile, si consiglia di progettare ogni versione del servizio perché sia compatibile con tutte le versioni precedenti, a partire dalla versione 1. In particolare, questo significa che V1 del codice del servizio deve essere in grado di ignorare tutti gli elementi dello schema che non vengono gestiti in modo esplicito. Tuttavia, deve essere in grado di salvare i dati che non conosce in modo esplicito e riscriverli quando si aggiorna il valore o la chiave del dizionario.

>[AZURE.WARNING] Sebbene sia possibile modificare lo schema di una chiave, è necessario assicurarsi che il codice hash e l'algoritmo di uguaglianza della chiave siano stabili. Se si modifica il funzionamento di questi algoritmi, non sarà più possibile cercare la chiave all'interno del ReliableDictionary.
  
In alternativa, è possibile eseguire un aggiornamento noto come aggiornamento a 2 fasi. Con un aggiornamento a 2 fasi, viene effettuato l'aggiornamento del servizio da V1 a V2: V2 contiene il codice in grado di gestire la nuova modifica dello schema, che non può essere eseguito. Quando il codice V2 legge i dati V1, opera su di esso e scrive i dati V1. Quindi, al termine dell'aggiornamento di tutti i domini di aggiornamento, è possibile in qualche modo segnalare alle istanze V2 in esecuzione che l'aggiornamento è stato completato. (uno modo consiste nell'implementare un aggiornamento della configurazione; ovvero un aggiornamento a 2 fasi). A questo punto, le istanze V2 possono leggere i dati V1, convertirli in dati V2, operare su di essi e scriverli nella forma di dati V2. Quando altre istanze leggono i dati V2, non è necessario convertirli: possono operano su di essi e scrivere dati V2.

## Passaggi successivi
Per informazioni sulla creazione di contratti dati compatibili con versioni successive, vedere [Contratti dati compatibili con versioni successive](https://msdn.microsoft.com/library/ms731083.aspx).

Per informazioni sulle procedure consigliate per il controllo delle versioni dei contratti dati, vedere [Controllo delle versioni dei contratti dati](https://msdn.microsoft.com/library/ms731138.aspx).

Per informazioni su come implementare contratti dati a tolleranza di versione, vedere [Callback di serializzazione a tolleranza di versione](https://msdn.microsoft.com/library/ms733734.aspx).

Per informazioni su come fornire una struttura di dati che può interagire con più versioni, vedere [Interfaccia IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).

<!---HONumber=AcomDC_0406_2016-->