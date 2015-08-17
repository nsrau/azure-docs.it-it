<properties
	pageTitle="Introduzione all'archiviazione delle code di Azure e ai servizi relativi a Visual Studio"
	description="Informazioni su come iniziare a usare il servizio di archiviazione di accodamento di Azure in un progetto ASP.NET in Visual Studio."
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
	ms.date="08/04/2015"
	ms.author="patshea123"/>

# Introduzione all'archiviazione delle code di Azure e ai servizi relativi a Visual Studio

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

##Panoramica

In questo articolo viene descritto come iniziare a utilizzare l'archiviazione delle code di Azure in Visual Studio dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un progetto ASP.NET usando la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio.

Verrà mostrato come creare e accedere a una coda nell'account di archiviazione di Azure. Infine verrà mostrato come eseguire operazioni relative alle code di base, come l'aggiunta, la modifica, la lettura e la rimozione di messaggi delle code. Negli esempi, scritti in codice C#, viene utilizzata la [libreria del client di archiviazione di Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Per ulteriori informazioni su ASP.NET, vedere [ASP.NET](http://www.asp.net).

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione.

##Code di accesso nel codice

Per accedere alle code nei progetti ASP.NET, è necessario includere gli elementi seguenti ai file di origine C# che consentono di accedere all'archiviazione delle code di Azure.

1. Assicurarsi che le dichiarazioni dello spazio dei nomi all'inizio del file C# includano queste istruzioni `using`.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. Ottenere un oggetto `CloudStorageAccount` che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto `CloudQueueClient` per fare riferimento agli oggetti delle code nell'account di archiviazione.

	    // Create the queue client.
    	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Ottenere un oggetto `CloudQueue` per fare riferimento a una coda specifica.

    	// Get a reference to a queue named "messageQueue"
	    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**NOTA:** utilizzare tutto il codice riportato in precedenza prima del codice indicato negli esempi seguenti.

##Creare una coda in codice

Per creare una coda di Azure nel codice, è sufficiente aggiungere una chiamata `CreateIfNotExists` al codice riportato sopra.

	// Create the CloudQuecClient  if it does not exist
	messageQueue.CreateIfNotExists();

##Aggiungere un messaggio a una coda

Per inserire un messaggio in una coda esistente, creare un nuovo oggetto `CloudQueueMessage`, quindi chiamare il metodo `AddMessage`.

Un oggetto `CloudQueueMessage` può essere creato da una stringa (in formato UTF-8) o da una matrice di byte.

Di seguito è riportato un esempio che inserisce il messaggio "Hello, World".

	// Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

##Leggere un messaggio in una coda

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo PeekMessage().

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

##Leggere e rimuovere un messaggio in una coda

Il codice può rimuovere un messaggio da una coda in due passaggi. 1. Chiamare GetMessage() per ottenere il messaggio successivo in una coda. Un messaggio restituito da GetMessage() diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. 2. Per completare la rimozione del messaggio dalla coda, chiamare `DeleteMessage`.

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il seguente codice chiama immediatamente `DeleteMessage` dopo l'elaborazione del messaggio.

	// Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

  	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## Usufruire di opzioni aggiuntive per rimuovere i messaggi dalla coda

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Il seguente esempio di codice usa il metodo `GetMessages` per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo `foreach`. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i 5 minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi 5 minuti dalla chiamata a `GetMessages`, tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## Recuperare la lunghezza della coda

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo `FetchAttributes` chiede al servizio di accodamento di recuperare gli attributi della coda, incluso il numero di messaggi. La proprietà `ApproximateMethodCount` restituisce l'ultimo valore recuperato per il metodo `FetchAttributes`, senza chiamare il servizio di Accodamento.

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Utilizzare il modello Async-Await modello con API delle code comuni

In questo esempio viene illustrato come utilizzare il modello Async-Await con API delle code comuni. Nell'esempio viene chiamata la versione asincrona di ogni metodo specificato. Ciò può essere osservato dal suffisso Async di ciascun metodo. Quando un metodo asincrono viene utilizzato, il modello async-await sospende l'esecuzione locale fino al completamento della chiamata. Questo comportamento consente al thread corrente di eseguire altre attività per evitare colli di bottiglia delle prestazioni e migliora la velocità di risposta complessiva dell'applicazione. Per ulteriori informazioni sull'utilizzo del modello Async-Await in .NET, vedere [Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo `Delete` sull'oggetto coda.

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Delete the queue.
    messageQueue.Delete();



##Passaggi successivi

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
			

<!---HONumber=August15_HO6-->