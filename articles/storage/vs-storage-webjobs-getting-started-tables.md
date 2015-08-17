<properties 
	pageTitle="Introduzione all'Archiviazione di Azure" 
	description="Informazioni su come iniziare a usare l'archiviazione tabelle di Azure in un progetto Azure WebJobs 5 in Visual Studio." 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# Introduzione all'Archiviazione di Azure (progetti Azure WebJob)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-tables.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-webjobs-getting-started-blobs.md)
> - [Queues](vs-storage-webjobs-getting-started-queues.md)
> - [Tables](vs-storage-webjobs-getting-started-tables.md)



## Panoramica

Il servizio di archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali. Per altre informazioni, vedere [Come usare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md/#create-a-table "Come usare l'archiviazione tabelle da .NET").

Questo articolo fornisce esempi di codice C# che illustrano come usare Azure WebJobs SDK versione 1.x con il servizio di archiviazione tabelle di Azure. Gli esempi di codice usano [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versione 1.x.

		
Alcuni dei frammenti di codice illustrano l'attributo `Table` usato nelle funzioni [chiamate manualmente](vs-storage-webjobs-getting-started-blobs.md#manual) e non mediante uno degli attributi del trigger.

##Come aggiungere entità a una tabella

Per aggiungere entità a una tabella, utilizzare l’attributo `Table` con un parametro `ICollector<T>` o `IAsyncCollector<T>` dove `T` specifica lo schema delle entità da aggiungere. Il costruttore dell'attributo accetta un parametro di stringa che specifica il nome della tabella.

L’esempio di codice seguente aggiunge le entità `Person` a una tabella denominata *Ingress*.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

In genere il tipo usato con `ICollector` deriva da `TableEntity` o implementa `ITableEntity`, anche se non necessariamente. Una delle seguenti classi `Person` usa il codice illustrato nel precedente metodo `Ingress`.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Se si desidera usare direttamente l'API di archiviazione di Azure, è possibile aggiungere un parametro `CloudStorageAccount` alla firma del metodo.

##Monitoraggio in tempo reale

Poiché spesso le funzioni di ingresso ai dati (Ingress) elaborano volumi elevati di informazioni, il dashboard di WebJobs SDK fornisce dati di monitoraggio in tempo reale. La sezione **Invocation Log** indica se la funzione è ancora in esecuzione.

![Funzione Ingress in esecuzione](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

La pagina **Invocation Details** restituisce lo stato di avanzamento della funzione (numero di entità scritte) mentre è in esecuzione e offre la possibilità di interromperla.

![Funzione Ingress in esecuzione](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Al termine della funzione, la pagina **Invocation Details** indica il numero di righe scritte.

![Funzione Ingress completata](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

##Come leggere più entità da una tabella

Per leggere una tabella, usare l’attributo `Table` con un parametro `IQueryable<T>` dove il tipo `T` deriva da `TableEntity` o implementa `ITableEntity`.

Il seguente esempio di codice legge e registra tutte le righe dalla tabella `Ingress`:
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

###Come leggere una singola entità da una tabella

È disponibile un costruttore dell'attributo `Table` con due parametri aggiuntivi che consentono di specificare la chiave di partizione e la chiave di riga quando si desidera l'associazione a un'entità di tabella singola.

Il seguente esempio di codice legge una riga della tabella per un'entità `Person` basata sui valori della chiave di partizione e della chiave di riga ricevuti in un messaggio di coda:

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


La classe `Person` in questo esempio non deve implementare `ITableEntity`.

##Come usare l'API di archiviazione .NET direttamente con una tabella

È possibile usare l'attributo `Table` anche con un oggetto `CloudTable` per una maggiore flessibilità nell'uso di una tabella.

Il seguente esempio di codice usa un oggetto `CloudTable` per aggiungere una singola entità alla tabella *Ingress*.
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

Per altre informazioni su come usare l'oggetto `CloudTable`, vedere l'argomento relativo [all'uso dell'archiviazione tabelle da .NET](../storage-dotnet-how-to-use-tables.md).

##Argomenti correlati trattati nell'articolo delle procedure sulle code

Per informazioni su come gestire l'elaborazione di tabelle attivata da un messaggio di coda o per scenari di WebJobs SDK non specifici dell'elaborazione di tabelle, vedere [Come usare il servizio di archiviazione code di Azure con WebJobs SDK](vs-storage-webjobs-getting-started-queues.md).



##Passaggi successivi

Questo articolo ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso di tabelle di Azure. Per altre informazioni su come usare i processi Web di Azure e su WebJobs SDK, vedere le [risorse consigliate per i processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=August15_HO6-->