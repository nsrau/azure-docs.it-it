<properties  linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use the Windows Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="tamram" />

# Come utilizzare l'archiviazione di accodamento da .NET

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione di accodamento di Azure. Negli esempi, scritti in codice C#, viene utilizzata la libreria client di archiviazione di Azure per .NET. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il **recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed eliminazione di code**. Per ulteriori informazioni sulle code, fare riferimento alla sezione [Passaggi successivi](#next-steps).

<h2>Sommario</h2>

* [Informazioni sull'archiviazione di accodamento](#what-is)
* [Concetti](#concepts)
* [Creazione di un account di archiviazione di Azure](#create-account)
* [Configurazione di una stringa di connessione di archiviazione di Azure](#setup-connection-string)
* [Procedura: Accedere alle code a livello di codice tramite .NET](#access)
* [Procedura: Creare una coda](#create-queue)
* [Procedura: Inserire un messaggio in una coda](#insert-message)
* [Procedura: Visualizzare il messaggio successivo](#peek-message)
* [Procedura: Cambiare il contenuto di un messaggio in coda](#change-contents)
* [Procedura: Rimuovere il messaggio successivo dalla coda](#get-message)
* [Procedura: Utilizzare opzioni aggiuntive per rimuovere i messaggi dalla coda](#advanced-get)
* [Procedura: Recuperare la lunghezza della coda](#get-queue-length)
* [Procedura: Eliminare una coda](#delete-queue)
* [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Creazione di un account</span>Creazione di un account di archiviazione di Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Configurazione di una stringa di connessione</span>Configurazione di una stringa di connessione di archiviazione di Azure</h2>

Nella libreria client di archiviazione di Azure per .NET è possibile utilizzare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. È possibile inserire la stringa di connessione di archiviazione in un file di configurazione, anziché impostarla come hardcoded nel codice:

* Quando si utilizza Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`).
* Quando si utilizza Siti Web di Azure, Macchine virtuali di Azure o si compilano applicazioni .NET destinate a essere eseguite all'esterno di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file `web.config` o `app.config`.

In entrambi i casi è possibile recuperare la stringa di connessione utilizzando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### Configurazione della stringa di connessione per l'utilizzo con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione.

Per configurare la stringa di connessione nella configurazione dei servizi di Azure:

1.  All'interno di Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse sul ruolo Web o sul ruolo di lavoro nella cartella **Ruoli**, quindi scegliere **Proprietà**.
    
    ![BLOB5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2.  Fare clic sulla scheda **Impostazioni** e quindi sul pulsante **Aggiungi impostazioni**.
    
    ![BLOB6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)
    
    Nella griglia delle impostazioni verrà visualizzata una nuova voce **Impostazione1**.

3.  Nell'elenco a discesa **Tipo** della nuova voce **Impostazione1** selezionare **Stringa di connessione**.
    
    ![BLOB7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4.  Fare clic sul pulsante **...** a destra della voce **Impostazione1**. Verrà visualizzata la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

5.  Scegliere se si desidera utilizzare come destinazione l'emulatore di archiviazione (servizio Archiviazione di Azure simulato nel  computer locale) oppure un effettivo account di archiviazione nel cloud. Il codice riportato in questa guida funziona con entrambe le opzioni. Se si desidera archiviare i dati di accodamento nell'account di archiviazione creato in precedenza in Azure, immettere il valore di **Chiave di accesso primaria** copiato dal passaggio precedente di questa esercitazione.
    
    ![BLOB8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6.  Modificare il valore **Impostazione1** della voce **Nome** specificando un nome più descrittivo, ad esempio **StorageConnectionString**. A questa stringa di connessione verrà fatto riferimento più avanti nel codice in questa guida.
    
    ![BLOB9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)

### Impostazione della stringa di connessione con la configurazione .NET

Se si intende scrivere un'applicazione diversa da un servizio cloud di Azure (vedere la sezione precedente), è consigliabile utilizzare il sistema di configurazione .NET, ad esempio `web.config` o `app.config`. Oltre alle applicazioni progettate per l'esecuzione all'esterno di Azure, questo sistema include Siti Web di Azure o Macchine virtuali di Azure. Per archiviare la stringa di connessione, utilizzare l'elemento `<appSettings>`, come indicato di seguito:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

Per ulteriori informazioni sulle stringhe di connessione di archiviazione, leggere [Configurazione delle stringhe di connessione][1].

È ora possibile eseguire le procedure di questa guida.

<h2><a name="access"></a><span  class="short-header">Accedere a livello di codice</span>Procedura: Accedere alle code a livello di codice tramite .NET</h2>

<h3>Recupero dell'assembly</h3>
Per recuperare l'assembly `Microsoft.WindowsAzure.Storage.dll`, è possibile utilizzare NuGet. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le
dipendenze.

Il file `Microsoft.WindowsAzure.Storage.dll` è inoltre incluso in Azure SDK per .NET, che può essere scaricato dal [Centro per sviluppatori .NET][2]. L'assembly viene installato nella directory `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\`.

<h3>Dichiarazioni dello spazio dei nomi</h3>
Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si desidera accedere ad Archiviazione di Azure a livello di codice:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Assicurarsi di fare riferimento all'assembly `Microsoft.WindowsAzure.Storage.dll`.

<h3>Recupero della stringa di connessione</h3>
Per rappresentare le informazioni dell'account di archiviazione, è possibile utilizzare il tipo **CloudStorageAccount**. Se si intende utilizzare un modello di progetto Azure e/o si dispone di un riferimento a Microsoft.WindowsAzure.CloudConfigurationManager, è possibile utilizzare il tipo **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se si intende creare un'applicazione senza riferimenti a Microsoft.WindowsAzure.CloudConfigurationManager e la stringa di connessione si trova nel file `web.config` o `app.config` come illustrato in precedenza, per recuperarla è possibile utilizzare **ConfigurationManager**. Sarà necessario aggiungere un riferimento a System.Configuration.dll al progetto e aggiungere un'altra dichiarazione dello spazio dei nomi:

    using System.Configuration;?...?CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dipendenze ODataLib</h3>
Le dipendenze ODataLib nella libreria client di archiviazione per .NET vengono risolte con i pacchetti ODataLib (versione 5.0.2) disponibili tramite NuGet e non WCF Data Services. È possibile scaricare le librerie ODataLib direttamente oppure farvi riferimento nel progetto del codice tramite NuGet. I pacchetti ODataLib specifici sono [OData][3], [Edm][4] e [Spatial][5].

<h2><a name="create-queue"></a><span  class="short-header">Creare una coda</span>Procedura: Creare una coda</h2>

Per ottenere oggetti di riferimento per le code, è possibile utilizzare un oggetto **CloudQueueClient**. Il codice seguente consente di creare un oggetto **CloudQueueClient**. In tutto il codice di questa guida viene utilizzata una stringa di connessione di archiviazione archiviata nel servizio di configurazione dell'applicazione Azure. Sono inoltre disponibili altri modi per creare un oggetto **CloudStorageAccount**. Per informazioni dettagliate, vedere la documentazione relativa a [CloudStorageAccount][6].

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilizzare l'oggetto **queueClient** per ottenere un riferimento alla coda da utilizzare. È possibile creare la coda se non esiste già.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">Inserire un messaggio</span>Procedura: Inserire un messaggio in una coda</h2>

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

<h2><a name="peek-message"></a><span class="short-header">Visualizzare il messaggio successivo</span>Procedura: Visualizzare il messaggio successivo</h2>

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

<h2><a name="change-contents"></a><span  class="short-header">Cambiare il contenuto di un messaggio</span>Procedura: Cambiare il contenuto di un messaggio in coda</h2>

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. Il codice seguente consente di aggiornare il messaggio in coda con nuovo contenuto e di impostarne il timeout di visibilità per prolungarlo di altri 60 secondi. In questo modo lo stato del lavoro associato al messaggio viene salvato e il client ha a disposizione un altro minuto per continuare l'elaborazione del messaggio. È possibile utilizzare questa tecnica per tenere traccia di flussi di lavoro composti da più passaggi nei messaggi in coda, senza la necessità di ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware o software. In genere, è consigliabile mantenere anche un conteggio dei tentativi, in modo da eliminare i messaggi per cui vengono effettuati più di *n* tentativi. In questo modo è possibile evitare che un messaggio attivi un errore dell'applicazione ogni volta che viene elaborato.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;?queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">Rimuovere il messaggio successivo dalla coda</span>Procedura: Rimuovere il messaggio successivo dalla coda</h2>

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

<h2><a name="advanced-get"></a><span  class="short-header">Usufruire di opzioni aggiuntive per la rimozione di messaggi dalla coda</span>Procedura: Usufruire di opzioni aggiuntive per rimuovere i messaggi dalla coda</h2>

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **GetMessages** per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo **foreach**. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))?{?    // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);?}?

<h2><a name="get-queue-length"></a><span  class="short-header">Recuperare la lunghezza della coda</span>Procedura: Recuperare la lunghezza della coda</h2>

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

<h2><a name="delete-queue"></a><span  class="short-header">Eliminare una coda</span>Procedura: Eliminare una coda</h2>

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

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

*  Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio di accodamento:
  *  [Informazioni di riferimento sulla libreria client di archiviazione per .NET][7]
  *  [Informazioni di riferimento sulle API REST][8]
*  Per ulteriori informazioni sulle attività avanzate che è possibile eseguire con Archiviazione di Azure, vedere la pagina relativa all'[archiviazione e all'accesso ai dati in Azure][9].
*  Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
  *  Per archiviare dati strutturati, utilizzare [Archiviazione tabelle](/en-us/develop/net/how-to-guides/table-services/).
  *  Per archiviare dati non strutturati, utilizzare [Archiviazione BLOB](/en-us/develop/net/how-to-guides/blob-storage/).
  *  Per archiviare dati relazionali, utilizzare [Database SQL](/en-us/develop/net/how-to-guides/sql-database/).



[1]: http://msdn.microsoft.com/it-it/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/it-it/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/it-it/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
[7]: http://msdn.microsoft.com/it-it/library/windowsazure/dn495001(v=azure.10).aspx
[8]: http://msdn.microsoft.com/it-it/library/windowsazure/dd179355
[9]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx