<properties 
	pageTitle="Come usare l'archiviazione di accodamento da .NET | Microsoft Azure" 
	description="Informazioni su come usare il servizio di archiviazione di accodamento di Microsoft Azure per creare ed eliminare code e per inserire, visualizzare, ottenere ed eliminare messaggi della coda." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>

# Come usare l'archiviazione di accodamento da .NET

In questa guida verranno illustrati diversi scenari comuni di uso del servizio di archiviazione di accodamento di Azure. Negli esempi, scritti in codice C\#, viene usata la libreria client di archiviazione di Azure per .NET. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il **recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed eliminazione di code**. Per altre informazioni sulle code, fare riferimento alla sezione [Passaggi successivi][].

> [AZURE.NOTE] Questa guida fa riferimento alla libreria client di archiviazione di Azure per .NET 2.x e versioni successive. La versione consigliata è la libreria client di archiviazione 4.x, disponibile tramite [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o come parte di [Azure SDK per .NET](/it-it/downloads/). Vedere [Procedura: Accedere all'archiviazione di accodamento a livello di codice][] per informazioni su come ottenere la libreria client di archiviazione.

<h2>Sommario</h2>

-   [Informazioni sull'archiviazione di accodamento][]
-   [Concetti][]
-   [Creare un account di archiviazione di Azure][]
-   [Configurare una stringa di connessione di archiviazione di Azure][]
-   [Procedura: Accedere all'archiviazione di accodamento a livello di codice][]
-   [Procedura: Creare una coda][]
-   [Procedura: Inserire un messaggio in una coda][]
-   [Procedura: Visualizzare il messaggio successivo][]
-   [Procedura: Cambiare il contenuto di un messaggio in coda][]
-   [Procedura: Rimuovere il messaggio successivo dalla coda][]
-   [Procedura: Usare opzioni aggiuntive per rimuovere i messaggi dalla coda][]
-   [Procedura: Recuperare la lunghezza della coda][]
-   [Procedura: Eliminare una coda][]
-   [Passaggi successivi][]

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Procedura: Accedere all'archiviazione di accodamento a livello di codice

<h3>Recupero dell'assembly</h3>
Per ottenere l'assembly `Microsoft.WindowsAzure.Storage.dll`, è possibile usare NuGet. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

Il file `Microsoft.WindowsAzure.Storage.dll` è inoltre incluso in Azure SDK per .NET, che può essere scaricato dal <a href="http://www.windowsazure.com/it-it/develop/net/#">Centro per sviluppatori .NET</a>. L'assembly viene installato nella directory `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Dichiarazioni dello spazio dei nomi</h3>
Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Assicurarsi di fare riferimento all'assembly `Microsoft.WindowsAzure.Storage.dll`.

<h3>Recupero della stringa di connessione</h3>
Per rappresentare le informazioni dell'account di archiviazione, è possibile usare il tipo **CloudStorageAccount**. Se si intende usare un modello di progetto Azure e/o si dispone di un riferimento a Microsoft.WindowsAzure.CloudConfigurationManager, è possibile usare il tipo **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se si intende creare un'applicazione senza riferimenti a Microsoft.WindowsAzure.CloudConfigurationManager e la stringa di connessione si trova nel file `web.config` o `app.config` come illustrato in precedenza, per recuperarla è possibile usare **ConfigurationManager**.  Sarà necessario aggiungere un riferimento a System.Configuration.dll al progetto e aggiungere un'altra dichiarazione dello spazio dei nomi:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dipendenze ODataLib</h3>
Le dipendenze ODataLib nella libreria client di archiviazione per .NET vengono risolte con i pacchetti ODataLib (versione 5.0.2) disponibili tramite NuGet e non WCF Data Services.  È possibile scaricare le librerie ODataLib direttamente oppure farvi riferimento nel progetto del codice tramite NuGet.  I pacchetti ODataLib specifici sono [OData], [Edm] e [Spatial].

<h2><a name="create-queue"></a>Procedura: Creare una coda</h2>

Per ottenere oggetti di riferimento per le code, è possibile usare un oggetto **CloudQueueClient**. Il codice seguente consente di creare un oggetto **CloudQueueClient**. In tutto il codice di questa guida viene usata una stringa di connessione di archiviazione archiviata nel servizio di configurazione dell'applicazione Azure. Sono inoltre disponibili altri modi per creare un oggetto **CloudStorageAccount**. Per informazioni dettagliate, vedere la documentazione relativa a [CloudStorageAccount][].

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Usare l'oggetto **queueClient** per ottenere un riferimento alla coda
da usare. È possibile creare la coda se non esiste già.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a>Procedura: Inserire un messaggio in una coda</h2>

Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto **CloudQueueMessage**. Quindi, chiamare il metodo **AddMessage**. È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di **byte**. Di seguito è riportato il codice che consente di creare una coda (se non esiste già) e di inserire il messaggio 'Hello, World':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a>Procedura: Visualizzare il messaggio successivo</h2>

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **PeekMessage**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display message.
	Console.WriteLine(peekedMessage.AsString);

<h2><a name="change-contents"></a>Procedura: Cambiare il contenuto di un messaggio in coda</h2>

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile usare questa funzionalità per aggiornarne lo stato. Il codice seguente consente di aggiornare il messaggio in coda con nuovo contenuto e di impostarne il timeout di visibilità per prolungarlo di altri 60 secondi. In questo modo lo stato del lavoro associato al messaggio viene salvato e il client ha a disposizione un altro minuto per continuare l'elaborazione del messaggio. È possibile usare questa tecnica per tenere traccia di flussi di lavoro composti da più passaggi nei messaggi in coda, senza la necessità di ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware o software. In genere, è consigliabile mantenere anche un conteggio dei tentativi, in modo da eliminare i messaggi per cui vengono effettuati più di *n* tentativi. In questo modo è possibile evitare che un messaggio attivi un errore dell'applicazione ogni volta che viene elaborato.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a>Procedura: Rimuovere il messaggio successivo dalla coda</h2>

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Chiamando **GetMessage**, si ottiene il messaggio successivo in una coda. Un messaggio restituito da **GetMessage** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **DeleteMessage**. Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **DeleteMessage** immediatamente dopo l'elaborazione del messaggio.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a>Procedura: Usufruire di opzioni aggiuntive per rimuovere i messaggi dalla coda</h2>

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene usato il metodo **GetMessages** per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo **foreach**. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a>Procedura: Recuperare la lunghezza della coda</h2>

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **FetchAttributes** chiede al servizio di accodamento di recuperare gli attributi della coda, incluso il numero di messaggi. La proprietà **ApproximateMethodCount** restituisce l'ultimo valore recuperato dal metodo **FetchAttributes**, senza chiamare il servizio di accodamento.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Fetch the queue attributes.
	queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

<h2><a name="delete-queue"></a>Procedura: Eliminare una coda</h2>

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **Delete** sull'oggetto coda.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

<h2><a name="next-steps"></a>Passaggi successivi</h2>

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

<ul>
<li>Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio di accodamento:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Informazioni di riferimento sulla libreria client di archiviazione per .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/it-it/library/windowsazure/dd179355">Informazioni di riferimento sulle API REST dei servizi di archiviazione</a></li>
  </ul>
</li>
<li>Per altre informazioni sulle attività avanzate che è possibile eseguire con Archiviazione di Azure, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx">Archiviazione</a>.</li>
<li>Per altre informazioni su come semplificare il codice scritto per usare Archiviazione di Azure, vedere <a href="../websites-dotnet-webjobs-sdk/">Introduzione a Azure WebJobs SDK.</li>
<li>Per altre opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
  <ul>
    <li>Per archiviare dati strutturati, usare <a href="/it-it/documentation/articles/storage-dotnet-how-to-use-tables/">Archiviazione tabelle</a>.</li>
    <li>Per archiviare dati non strutturati, usare <a href="/it-it/documentation/articles/storage-dotnet-how-to-use-blobs/">Archiviazione BLOB</a>.</li>
    <li>Per archiviare dati relazionali, usare <a href="/it-it/documentation/articles/sql-database-dotnet-how-to-use/">Database SQL</a>.</li>
  </ul>
</li>
</ul>



  [Passaggi successivi]: #next-steps
  [Informazioni sull'archiviazione di accodamento]: #what-is
  [Concetti]: #concepts
  [Creare un account di archiviazione di Azure]: #create-account
  [Configurare una stringa di connessione di archiviazione di Azure]: #setup-connection-string
  [Procedura: Accedere all'archiviazione di accodamento a livello di codice]: #configure-access
  [Procedura: Creare una coda]: #create-queue
  [Procedura: Inserire un messaggio in una coda]: #insert-message
  [Procedura: Visualizzare il messaggio successivo]: #peek-message
  [Procedura: Cambiare il contenuto di un messaggio in coda]: #change-contents
  [Procedura: Rimuovere il messaggio successivo dalla coda]: #get-message
  [Procedura: Usare opzioni aggiuntive per rimuovere i messaggi dalla coda]: #advanced-get
  [Procedura: Recuperare la lunghezza della coda]: #get-queue-length
  [Procedura: Eliminare una coda]: #delete-queue
  [Scaricare e installare Azure SDK per .NET]: /it-it/develop/net/
  [Informazioni di riferimento sulla libreria client .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creazione di un progetto Azure in Visual Studio]: http://msdn.microsoft.com/it-it/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/it-it/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Archiviazione]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurazione delle stringhe di connessione]: http://msdn.microsoft.com/it-it/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
<!--HONumber=42-->
