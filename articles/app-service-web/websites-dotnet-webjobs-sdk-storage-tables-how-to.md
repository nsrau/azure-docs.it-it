<properties 
	pageTitle="Come usare il servizio di archiviazione tabelle di Azure con WebJobs SDK" 
	description="Informazioni su come usare il servizio di archiviazione tabelle di Azure con WebJobs SDK. Creare tabelle, aggiungere entità alle tabelle e leggere le tabelle esistenti." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="tdykstra"/>

# Come usare il servizio di archiviazione tabelle di Azure con WebJobs SDK

## Panoramica

In questa guida sono forniti esempi di codice C# che illustrano come leggere e scrivere le tabelle di archiviazione di Azure usando [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versione 1.x.

Nella guida si presuppone che si sappia come [creare un progetto WebJob in Visual Studio con stringhe di connessione che puntano all'account di archiviazione](websites-dotnet-webjobs-sdk-get-started.md) o [più account di archiviazione](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).
		
Alcuni dei frammenti di codice illustrano l'attributo `Table` usato nelle funzioni [chiamate manualmente](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) e non mediante uno degli attributi del trigger.

## <a id="ingress"></a> Come aggiungere entità a una tabella

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

## <a id="monitor"></a> Monitoraggio in tempo reale

Poiché spesso le funzioni di ingresso ai dati (Ingress) elaborano volumi elevati di informazioni, il dashboard di WebJobs SDK fornisce dati di monitoraggio in tempo reale. La sezione **Invocation Log** indica se la funzione è ancora in esecuzione.

![Funzione Ingress in esecuzione](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

La pagina **Invocation Details** restituisce lo stato di avanzamento della funzione (numero di entità scritte) mentre è in esecuzione e offre la possibilità di interromperla.

![Funzione Ingress in esecuzione](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Al termine della funzione, la pagina **Invocation Details** indica il numero di righe scritte.

![Funzione Ingress completata](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> Come leggere più entità da una tabella

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

### <a id="readone"></a> Come leggere una singola entità da una tabella

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

## <a id="storageapi"></a> Come usare l'API di archiviazione .NET direttamente con una tabella

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

Per altre informazioni su come usare l'oggetto `CloudTable`, vedere l'argomento relativo [all'uso dell'archiviazione tabelle da .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a id="queues"></a>Argomenti correlati trattati nell'articolo delle procedure sulle code

Per informazioni su come gestire l'elaborazione di tabelle attivata da un messaggio di coda o per scenari di WebJobs SDK non specifici dell'elaborazione di tabelle, vedere [Come usare il servizio di archiviazione code di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Gli argomenti trattati in questo articolo includono quanto segue:

* Funzioni asincrone
* Più istanze
* Arresto normale
* Usare gli attributi di WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice
* Impostare i valori per i parametri del costruttore WebJobs SDK nel codice
* Attivare manualmente una funzione
* Scrivere i log

## <a id="nextsteps"></a> Passaggi successivi

Questa guida ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso di tabelle di Azure. Per altre informazioni su come usare i processi Web di Azure e su WebJobs SDK, vedere le [risorse consigliate per i processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=AcomDC_0302_2016-->