<properties 
	pageTitle="Introduzione all'Archiviazione di Azure" 
	description="Informazioni su come iniziare a usare l'archiviazione di accodamento di Azure in un progetto ASP.NET 5 in Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="kempb"/>

# Introduzione all'Archiviazione di Azure (progetti ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. Per altre informazioni, vedere [Come utilizzare l'archiviazione di accodamento da .NET](storage-dotnet-how-to-use-queues.md/ "Come usare l'archiviazione di accodamento da .NET").

Per accedere alle code a livello di codice in progetti ASP.NET 5, è necessario aggiungere gli elementi seguenti, se non sono già presenti.

1. Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;

2. Usare il codice seguente per ottenere l'impostazione di configurazione.

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Ottenere la stringa di connessione di archiviazione
Prima di poter eseguire qualsiasi operazione con una coda, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui si troveranno le code. Per rappresentare le informazioni dell'account di archiviazione, è possibile usare il tipo **CloudStorageAccount**. Se si usa un progetto ASP.NET 5, sarà possibile chiamare il metodo Get dell'oggetto Configuration per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

**Nota:** le API che eseguono chiamate ad Archiviazione di Azure in ASP.NET 5 sono asincrone. Per ulteriori informazioni, vedere [Programmazione asincrona con Async e Await](http://msdn.microsoft.com/library/hh191443.aspx). Nel codice riportato di seguito si presuppone vengano utilizzati i metodi di programmazione asincrona.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Creare una coda
Per ottenere oggetti di riferimento per le code, è possibile usare un oggetto **CloudQueueClient**. Il codice seguente consente di creare un oggetto **CloudQueueClient**. In tutto il codice di questo argomento viene usata una stringa di connessione di archiviazione archiviata nel servizio di configurazione dell'applicazione Azure. Sono inoltre disponibili altri modi per creare un oggetto **CloudStorageAccount**. Per informazioni dettagliate, vedere la documentazione di [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount").

**Nota:** le API che eseguono chiamate ad Archiviazione di Azure in ASP.NET 5 sono asincrone. Per ulteriori informazioni, vedere [Programmazione asincrona con Async e Await](http://msdn.microsoft.com/library/hh191443.aspx). Nel codice riportato di seguito si presuppone vengano utilizzati i metodi di programmazione asincrona.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilizzare l'oggetto **queueClient** per ottenere un riferimento alla coda da utilizzare. Il codice tenta di fare riferimento a una coda denominata “myqueue”. Se non è in grado di trovare una coda con tale nome, ne crea una.

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	await queue.CreateIfNotExistsAsync();

**NOTA:** usare tutto questo codice prima del codice nelle sezioni seguenti.

#####Inserire un messaggio in una coda
Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto **CloudQueueMessage**. Chiamare quindi il metodo AddMessageAsync(). È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di byte. Di seguito è riportato il codice che consente di creare una coda (se non esiste già) e di inserire il messaggio 'Hello, World'.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####Visualizzare il messaggio successivo
È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo PeekMessageAsync().

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####Rimuovere il messaggio successivo
Il codice può rimuovere un messaggio da una coda in due passaggi.


1. Chiamare GetMessageAsync() per ottenere il messaggio successivo in una coda. Un messaggio restituito da GetMessageAsync() diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. 
2.	Per completare la rimozione del messaggio dalla coda, chiamare DeleteMessageAsync(). 

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice seguente chiama DeleteMessageAsync() immediatamente dopo l'elaborazione del messaggio.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[Ulteriori informazioni sull'Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/) Vedere anche [Esplorazione delle risorse di archiviazione con Esplora server](http://msdn.microsoft.com/library/azure/ff683677.aspx) e [ASP.NET 5](http://www.asp.net/vnext).

<!---HONumber=58-->