<properties 
	pageTitle="Come partizionare i dati in DocumentDB con .NET SDK" 
	description="Informazioni su come usare Azure DocumentDB .NET SDK per partizionare (condividere) i dati e instradare le richieste in più raccolte" 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="arramac"/>

# Come partizionare i dati in DocumentDB con .NET SDK

Azure DocumentDB è un servizio di database di documenti che consente di scalare facilmente l'account attraverso il provisioning di raccolte usando gli [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) e le [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) (detto anche **partizionamento orizzontale**). Per facilitare lo sviluppo di applicazioni partizionate e ridurre la quantità di codice boilerplate per il partizionamento delle attività, in .NET SDK è stata aggiunta una funzionalità che semplifica la compilazione di applicazioni scalate orizzontalmente in più partizioni.

In questo articolo, verranno esaminate le classi e le interfacce in .NET SDK e verrà spiegato come usarle per sviluppare le applicazioni partizionate.

## Partizionamento con l'SDK di DocumentDB

Prima di esaminare più in dettaglio il partizionamento, è opportuno riepilogare alcuni concetti di base di DocumentDB correlati al partizionamento. Ogni account di database di DocumentDB è costituito da un insieme di database, ognuno dei quali include più raccolte, che possono contenere stored procedure, trigger, UDF, documenti e allegati correlati. Le raccolte possono essere considerate come partizioni in DocumentDB e avere le proprietà seguenti:

- Le raccolte sono partizioni fisiche e non solo contenitori logici. La query o l'elaborazione di documenti che si trovano nella stessa raccolta comporta quindi un miglioramento delle prestazioni.
- Le raccolte sono il limite per le transazioni ACID, ovvero stored procedure e trigger.
- Le raccolte non applicano uno schema e quindi possono essere usate per documenti JSON dello stesso tipo o di tipi diversi.

A partire dalla versione [1.1.0 di Azure DocumentDB .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), è possibile eseguire operazioni sui documenti direttamente in un database. Internamente [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) usa la classe PartitionResolver specificata per il database per instradare le richieste alla raccolta appropriata.

Ogni classe PartitionResolver è un'implementazione concreta di un'interfaccia [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) che ha tre metodi: [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) e [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Le query LINQ e gli iteratori ReadFeed usano il metodo ResolveForRead internamente per scorrere tutte le raccolte che corrispondono alla chiave di partizione per la richiesta. Similmente le operazioni di creazione usano il metodo ResolveForCreate per instradare le creazioni alla partizione giusta. Non sono necessarie modifiche per Replace, Delete e Read perché usano documenti che contengono già il riferimento alla raccolta corrispondente.

L'SDK include anche due classi che supportano le due tecniche di partizionamento canoniche, l'hashing e le ricerche basate su intervalli, tramite [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) e [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). È possibile usare queste classi per aggiungere facilmente la logica di partizionamento all'applicazione.

## Aggiungere la logica di partizionamento e registrare PartitionResolver 

Ecco un frammento di codice che mostra come creare [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) e registrarlo con DocumentClient per un database.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## Creare documenti in una partizione  

Una volta registrato PartitionResolver, è possibile eseguire creazioni e query direttamente nel database, come illustrato di seguito. In questo esempio, l'SDK usa PartitionResolver per estrarre l'ID utente, generarne l'hash e quindi usare tale valore per instradare l'operazione di creazione alla raccolta corretta.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## Creare query per le partizioni  

È possibile eseguire una query con il metodo [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) passando il database e una chiave di partizione. La query restituisce un solo set di risultati per tutte le raccolte del database che eseguono il mapping alla chiave di partizione.

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## Creare query per tutte le raccolte del database 

È anche possibile eseguire una query di tutte le raccolte del database ed enumerare i risultati come illustrato di seguito, ignorando l'argomento della chiave di partizione.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## Resolver della partizione hash
Con il partizionamento hash le partizioni vengono assegnate in base al valore di una funzione hash, permettendo di distribuire uniformemente richieste e dati tra diverse partizioni. Questo approccio viene generalmente usato per il partizionamento dei dati prodotti o utilizzati da un numero elevato di client distinti e risulta utile per l'archiviazione di profili utente, elementi del catalogo e dati di telemetria IoT ("Internet of Things").

**Partizionamento hash:** ![Diagramma che illustra come il partizionamento hash distribuisca in modo uniforme le richieste tra le partizioni](media/documentdb-sharding/partition-hash.png "Partizionamento hash")

Un semplice schema di partizionamento hash in *N* raccolte consiste nel prendere un documento e nel calcolare *hash(d) mod N* per determinare in quale raccolta si trova. Questa semplice tecnica però non funziona bene quando si aggiungono nuove raccolte o si rimuovono le raccolte perché in questo caso sarebbe necessario riprodurre con sequenza casuale quasi tutti i dati. L'[hashing coerente](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) è un noto algoritmo che risolve questo problema implementando uno schema di hashing che riduce al minimo la quantità di dati da spostare durante l'aggiunta o la rimozione di raccolte.

La classe [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) implementa la logica per compilare una sequenza di hash coerenti sulla funzione hash specificata nell'interfaccia [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx). Per impostazione predefinita, HashPartitionResolver usa una funzione hash MD5, ma è possibile sostituirla con la propria implementazione di hashing. HashPartitionResolver crea internamente 16 hash o "nodi virtuali" nella sequenza di hash per ogni raccolta per ottenere una distribuzione più uniforme dei documenti nelle raccolte, ma è possibile variare questo numero in modo compensare l'asimmetria dei dati con la quantità di calcoli lato client.

**Hashing coerente con HashPartitionResolver:** ![Diagramma che illustra come HashPartitionResolver crei una sequenza di hash](media/documentdb-sharding/HashPartitionResolver.JPG "Hashing coerente")

## Resolver della partizione a intervalli

Nel partizionamento per intervalli le partizioni vengono assegnate in base alla presenza della chiave di partizione in un determinato intervallo. Questo approccio viene in genere usato per il partizionamento con proprietà di tipo timestamp, (ad esempio, eventTime tra 1 aprile 2015 e 14 aprile 2015). La classe [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) aiuta a mantenere un mapping tra Range<T> e il collegamento automatico della raccolta.

[Range<T>](https://msdn.microsoft.com/library/azure/mt126048.aspx) è una semplice classe che gestisce gli intervalli dei tipi che implementano stringhe o numeri di tipo IComparable<T> e IEquatable<T>. Per le operazioni di lettura e di creazione, è possibile passare qualsiasi intervallo arbitrario e il resolver identifica tutte le raccolte candidate identificando gli intervalli delle partizioni che intersecano l'intervallo richiesto. Questa funzionalità può essere utile quando si eseguono query di intervallo sui dati di una serie temporale.

**Partizionamento per intervalli:**

![Diagramma che illustra come il partizionamento per intervalli distribuisca in modo uniforme le richieste tra le partizioni](media/documentdb-sharding/partition-range.png "Partizionamento per intervalli")

Un caso speciale di partizionamento per intervalli si ha quando l'intervallo è un solo valore discreto, denominato a volte "partizionamento basato su ricerca". Questo approccio viene in genere usato per il partizionamento in base all'area (ad esempio, la partizione per la Scandinavia contiene Norvegia, Danimarca e Svezia) o per il partizionamento di tenant in un'applicazione multi-tenant.

## Esempi 

Esaminare il [progetto Github degli esempi di partizionamento di DocumentDB](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning) contenente frammenti di codice per usare le classi PartitionResolver ed estenderle per implementare i propri resolver per casi di utilizzo specifici, come i seguenti:

* Come specificare un'espressione lambda arbitraria per GetPartitionKey e usarla per implementare chiavi di partizionamento composte o per partizionare in modo diverso ogni tipo di oggetti.
* Come creare un semplice [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) che usa una tabella di ricerca manuale per eseguire il partizionamento. Questo modello di solito viene usato per il partizionamento basato su valori discreti, come area, ID tenant o nome applicazione.
* Come creare un oggetto [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) che crea automaticamente raccolte basate su un modello che definisce uno schema di denominazione, IndexingPolicy e le stored procedure da registrare nelle nuove raccolte.
* Come creare un oggetto [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) senza schema che crea semplicemente nuove raccolte quando le vecchie raccolte sono piene.
* Come serializzare e deserializzare lo stato di PartitionResolver come JSON, in modo da poter eseguire una condivisione tra processi e negli arresti. È possibile renderli persistenti nei file config o anche in una raccolta DocumentDB.
* Una classe [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) per l'aggiunta e la rimozione dinamiche di partizioni in un database partizionato in base all'hashing coerente. Usa internamente [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) per instradare le operazioni di lettura e di scrittura durante la migrazione usando una di queste quattro modalità: lettura dal vecchio schema di partizionamento (ReadCurrent), da quello nuovo (ReadNext), unione dei risultati da entrambi (ReadBoth) o mancata disponibilità durante la migrazione (None).

Gli esempi sono open source e si consiglia di inviare richieste pull con contributi che potrebbero essere utili ad altri sviluppatori DocumentDB. Per informazioni su come contribuire, fare riferimento alle [linee guida specifiche](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md).

>[AZURE.NOTE]Le creazioni di raccolte sono soggette a limitazioni di frequenza da parte di DocumentDB e quindi il completamento di alcuni dei metodi di esempio mostrati qui potrebbe richiedere alcuni minuti.

##Domande frequenti
**Perché DocumentDB supporta il partizionamento lato client invece del partizionamento lato server?**

DocumentDB supporta il partizionamento lato client per due motivi:

- È veramente difficile per gli sviluppatori prescindere dal concetto di raccolta senza compromettere l'indicizzazione/esecuzione di query coerente, la disponibilità elevata o le garanzie delle transazioni ACID. 
- I database di documenti spesso richiedono flessibilità in termini di definizione di strategie di partizionamento, cosa che un approccio lato server non potrebbe assicurare. 

**Perché il partizionamento non è supportato in altre piattaforme (Node.js, Java o Python)?**

Il supporto per il partizionamento verrà gradualmente implementato in altre piattaforme in base al feedback dei clienti di .NET SDK.

**Come aggiungere o rimuovere una raccolta nel proprio schema di partizionamento?**

Per un esempio di come implementare il ripartizionamento, esaminare l'implementazione di DocumentClientHashPartitioningManager nel progetto degli esempi.

**Come rendere persistente o condividere la propria configurazione di partizionamento con altri client?**

È possibile serializzare lo stato del partitioner come JSON e archiviarlo in file di configurazione o anche nelle raccolte DocumentDB. Per un esempio, esaminare il metodo RunSerializeDeserializeSample nel progetto degli esempi.

**Come concatenare diverse tecniche di partizionamento?**

È possibile concatenare classi PartitionResolver implementando la propria interfaccia IPartitionResolver che usa internamente uno o più sistemi resolver esistenti. Per un esempio, esaminare TransitionHashPartitionResolver nel progetto degli esempi.

##Riferimenti
* [Esempi di codici di partizionamento su Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning)
* [Partizionamento dei dati in DocumentDB](documentdb-partition-data.md)
* [Raccolte e livelli di prestazioni in DocumentDB](documentdb-performance-levels.md)
* [Documentazione di DocumentDB .NET SDK in MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Esempi di .NET in DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Limiti di DocumentDB](documentdb-limits.md)
* [Blog di DocumentDB sui suggerimenti per le prestazioni](http://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 

<!---HONumber=July15_HO4-->