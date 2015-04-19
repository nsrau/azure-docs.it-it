<properties 
	pageTitle="Come usare il servizio di archiviazione tabelle di Azure con WebJobs SDK" 
	description="Informazioni su come usare il servizio di archiviazione tabelle di Azure con WebJobs SDK. Creare tabelle, aggiungere entità alle tabelle e leggere le tabelle esistenti." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Come usare il servizio di archiviazione tabelle di Azure con WebJobs SDK

Questa guida fornisce esempi di codice C# che illustrano come leggere e scrivere le tabelle di archiviazione di Azure usando [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versione 1.x.

La guida presuppone che si sappia [come creare un progetto di processo Web in Visual Studio con stringhe di connessione che puntano all'account di archiviazione](websites-dotnet-webjobs-sdk-get-started.md).
		
Alcuni dei frammenti di codice illustrano l'attributo  `Table` usato nelle funzioni [chiamate manualmente](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual), ovvero non mediante uno degli attributi di trigger. 

## Sommario

-   [Come aggiungere entità a una tabella](#ingress)
-   [Monitoraggio in tempo reale](#monitor)
-   [Come leggere più entità da una tabella](#multiple)
-   [Come leggere una singola entità da una tabella](#readone)
-   [Come usare l'API .NET di archiviazione direttamente con una tabella](#readone)
-   [Argomenti correlati trattati nell'articolo delle procedure sulle code](#queues)
-   [Passaggi successivi](#nextsteps)

## <a id="ingress"></a>Come aggiungere entità a una tabella

Per aggiungere entità a una tabella, usare l'attributo  `Table` con un parametro  `ICollector<T>` o  `IAsyncCollector<T>` dove  `T` specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table. 

The following code sample adds `Person` le entità per una tabella denominata  *Ingress*.

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

In genere il tipo usato con  `ICollector` deriva da  `TableEntity` o implementa  `ITableEntity`, anche se non necessariamente. Una delle seguenti classi  `Person` usa il codice illustrato nel precedente metodo  `Ingress`.

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

Se si desidera usare direttamente l'API di archiviazione di Azure, è possibile aggiungere un parametro  `CloudStorageAccount` alla firma del metodo.

## <a id="monitor"></a>Monitoraggio in tempo reale

Poiché spesso le funzioni Ingress di dati elaborano volumi elevati di informazioni, il dashboard WebJobs SDK fornisce dati di monitoraggio in tempo reale. La sezione **Invocation Log** indica se la funzione è ancora in esecuzione.

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

La pagina **Invocation Details** indica lo stato di avanzamento della funzione (numero di entità scritte) mentre è in esecuzione e offre la possibilità di interromperla. 

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Al termine della funzione, la pagina **Invocation Details** indica il numero di righe scritte.

![Ingress function finished](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a>Come leggere più entità da una tabella

Per leggere una tabella, usare l'attributo  `Table` con un parametro  `IQueryable<T>` dove il tipo  `T` derives from `TableEntity` o implementa  `ITableEntity`.

Il seguente esempio di codice legge e registra tutte le righe dalla tabella  `Ingress`:
 
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

### <a id="readone"></a>Come leggere una singola entità da una tabella

È disponibile un costruttore dell'attributo  `Table` con due parametri aggiuntivi che consentono di specificare la chiave di partizione e la chiave di riga quando si desidera l'associazione a un'entità di tabella singola.

Il seguente esempio di codice legge una riga della tabella per un'entità  `Person` basata sui valori della chiave di partizione e della chiave di riga ricevuti in un messaggio di coda:  

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


La classe  `Person` in questo esempio non deve implementare  `ITableEntity`.

## <a id="storageapi"></a> Come usare l'API .NET di archiviazione direttamente con una tabella

È inoltre possibile usare l'attributo  `Table` con un oggetto  `CloudTable` per una maggiore flessibilità di uso di una tabella.

Il seguente esempio di codice usa un oggetto  `CloudTable` per aggiungere una singola entità alla tabella  *Ingress*. 
 
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

Per altre informazioni su come usare l'oggetto  `CloudTable`, vedere [Come usare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md). 

## <a id="queues"></a>Argomenti correlati trattati nell'articolo delle procedure sulle code

Per informazioni su come gestire l'elaborazione delle tabelle attivata da un messaggio di coda o per gli scenari di WebJobs SDK non specifici dell'elaborazione delle tabelle, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Tra gli argomenti trattati nell'articolo sono inclusi i seguenti:

* Funzioni asincrone
* Istanze multiple
* Arresto normale
* Usare gli attributi WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice
* Impostare i valori per i parametri del costruttore WebJobs SDK nel codice
* Attivare manualmente una funzione
* Scrivere log

## <a id="nextsteps"></a> Passaggi successivi

Questa guida ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso di code di Azure. Per altre informazioni su come usare i processi Web Azure e su WebJobs SDK, vedere le [risorse consigliate per i processi Web Azure](http://go.microsoft.com/fwlink/?linkid=390226).


<!--HONumber=42-->
