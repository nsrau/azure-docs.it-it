<properties linkid="dev-net-how-to-queue-service" urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Come utilizzare l'archiviazione di accodamento da .NET

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di
archiviazione di accodamento di Azure. Negli esempi, scritti in codice C#
, viene usata la libreria client di archiviazione di Azure per .NET. Gli scenari presentati includono l'**inserimento**, la
**visualizzazione**, il
**recupero** e l'
**eliminazione** dei messaggi in coda, oltre alle procedure di
**creazione ed eliminazione di code**. Per altre informazioni sulle code, fare riferimento
alla sezione [Passaggi successivi][].

> [WACOM.NOTE] Questa guida fa riferimento alla libreria client di archiviazione di Azure per .NET 2.x e versioni successive. La versione consigliata è la libreria client di archiviazione di Azure per .NET 4.x, disponibile tramite [NuGet][] o come parte di [Azure SDK per .NET][]. Vedere [Procedura: Accedere all'archiviazione di accodamento a livello di codice][], per informazioni su come ottenere la libreria client di archiviazione.

## Sommario

-   [Informazioni sull'archiviazione di accodamento][]
-   [Concetti][]
-   [Creare un account di archiviazione di Azure][]
-   [Configurazione di una stringa di connessione di archiviazione di Azure][]
-   [Procedura: Accedere all'archiviazione di accodamento a livello di codice][]
-   [Procedura: Creare una coda][]
-   [Procedura: Inserire un messaggio in una coda][]
-   [Procedura: Visualizzare il messaggio successivo][]
-   [Procedura: Cambiare il contenuto di un messaggio in coda][]
-   [Procedura: Rimuovere il messaggio successivo dalla coda][]
-   [Procedura: Utilizzare opzioni aggiuntive per rimuovere i messaggi dalla coda][]
-   [Procedura: Recuperare la lunghezza della coda][]
-   [Procedura: Eliminare una coda][]
-   [Passaggi successivi][]

[WACOM.INCLUDE [howto-queue-storage][]]

## 

## <a name="create-account"></a><span class="short-header">Creare un account</span>Creare un account di archiviazione di Azure

</h2>
[WACOM.INCLUDE [create-storage-account][]]

## 

## <a name="setup-connection-string"></a><span class="short-header">Configurazione di una stringa di connessione</span>Configurazione di una stringa di connessione di archiviazione di Azure

</h2>
[WACOM.INCLUDE [storage-configure-connection-string][]]

## <a name="configure-access"> </a><span class="short-header">Accedere a livello di programmazione</span>Procedura: Accedere all'archiviazione di accodamento a livello di codice

### Recupero dell'assembly

Per ottenere l'assembly `Microsoft.WindowsAzure.Storage.dll`, è possibile usare NuGet. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

Il file `Microsoft.WindowsAzure.Storage.dll` è inoltre incluso in Azure SDK per .NET, che può essere scaricato dal [Centro per sviluppatori .NET][]. L'assembly viene installato nella directory `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`

### Dichiarazioni dello spazio dei nomi

Aggiungere le dichiarazioni dello spazio dei nomi del codice seguenti all'inizio del file
C# in cui si intende accedere ad Archiviazione di Azure a livello di codice:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Assicurarsi di fare riferimento all'assembly `Microsoft.WindowsAzure.Storage.dll`.

### Recupero della stringa di connessione

Per rappresentare
le informazioni dell'account di archiviazione, è possibile utilizzare il tipo **CloudStorageAccount**. Se si intende usare un modello di progetto di Windows
Azure e/o si dispone di un riferimento a
Microsoft.WindowsAzure.CloudConfigurationManager, è
possibile usare il tipo **CloudConfigurationManager** per
recuperare la stringa di connessione di archiviazione e le informazioni
dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se si intende creare un'applicazione senza riferimenti a Microsoft.WindowsAzure.CloudConfigurationManager e la stringa di connessione si trova nel file `web.config` o `app.config` come illustrato in precedenza, per recuperarla è possibile usare **ConfigurationManager**. Sarà necessario aggiungere un riferimento a System.Configuration.dll al progetto e aggiungere un'altra dichiarazione dello spazio dei nomi:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Dipendenze ODataLib

Le dipendenze ODataLib nella libreria client di archiviazione per .NET vengono risolte con i pacchetti ODataLib (versione 5.0.2) disponibili tramite NuGet e non WCF Data Services. È possibile scaricare le librerie ODataLib direttamente oppure farvi riferimento nel progetto del codice tramite NuGet. I pacchetti ODataLib specifici sono [OData][], [Edm][] e [Spatial][].

## <a name="create-queue"></a><span class="short-header">Creare una coda</span>Procedura: Creare una coda

Per ottenere oggetti di riferimento per le code, è possibile usare un oggetto **CloudQueueClient**.
Il codice seguente consente di creare un oggetto **CloudQueueClient**. In tutto il codice di
questa guida viene usata una stringa di connessione di archiviazione archiviata nella configurazione del servizio dell'applicazione
Azure. Sono inoltre disponibili altri modi per creare un
oggetto **CloudStorageAccount**. Per informazioni dettagliate, vedere la documentazione relativa a [CloudStorageAccount][].

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Usare l'oggetto **queueClient** per ottenere un riferimento alla coda da
usare. È possibile creare la coda se non esiste già.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-message"> </a><span class="short-header">Inserire un messaggio</span>Procedura: Inserire un messaggio in una coda

Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto
**CloudQueueMessage**. Quindi, chiamare il metodo **AddMessage**. È possibile creare un oggetto
**CloudQueueMessage** da una stringa in formato
UTF-8 o da una matrice di **byte**. Di seguito è riportato il codice che consente di creare una coda (se
non esiste già) e di inserire il messaggio 'Hello, World'.

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

## <a name="peek-message"></a><span class="short-header">Visualizzare il messaggio successivo</span>Procedura: Visualizzare il messaggio successivo

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo
dalla coda chiamando il metodo **PeekMessage**.

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

## <a name="change-contents"></a><span class="short-header">Cambiare il contenuto di un messaggio</span>Procedura: Cambiare il contenuto di un messaggio in coda

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il
messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo
stato. Il codice seguente consente di aggiornare il messaggio
in coda con nuovo contenuto e di impostarne il timeout di visibilità per prolungarlo di altri 60
secondi. In questo modo lo stato del lavoro associato al messaggio viene salvato e il client ha a
disposizione un altro minuto per continuare l'elaborazione del messaggio. È
possibile utilizzare questa tecnica per tenere traccia di flussi di lavoro composti da più passaggi nei messaggi
in coda, senza la necessità di ricominciare dall'inizio se un passaggio di
elaborazione non riesce a causa di errori hardware o software. In genere,
è consigliabile mantenere anche un conteggio dei tentativi, in modo da eliminare i messaggi per cui vengono effettuati più
di *n* tentativi. In questo modo è possibile evitare che un messaggio
attivi un errore dell'applicazione ogni volta che viene elaborato.

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

## <a name="get-message"></a><span class="short-header">Rimuovere il messaggio successivo dalla coda</span>Procedura: Rimuovere il messaggio successivo dalla coda

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Chiamando
**GetMessage**, si ottiene il messaggio successivo in una coda. Un messaggio restituito
da **GetMessage** diventa invisibile a qualsiasi altro codice che legge i messaggi
dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30
secondi. Per completare la rimozione del messaggio dalla coda, è necessario
chiamare anche **DeleteMessage**. Questo processo in due passaggi di rimozione di un messaggio assicura
che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o
software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di
riprovare. Il codice chiama **DeleteMessage** immediatamente dopo l'elaborazione del
messaggio.

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

## <a name="advanced-get"></a><span class="short-header">Usufruire di opzioni aggiuntive per la rimozione di messaggi dalla coda</span>Procedura: Usufruire di opzioni aggiuntive per rimuovere i messaggi dalla coda

È possibile personalizzare il recupero di messaggi da una coda in due modi.
Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più
lungo o più breve assegnando al codice più o meno
tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo
**GetMessages** per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio
viene elaborato con un ciclo **foreach**. Per ogni messaggio, inoltre, il timeout
di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti iniziano
per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla
chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati diventano nuovamente
visibili.

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

## <a name="get-queue-length"></a><span class="short-header">Recuperare la lunghezza della coda</span>Procedura: Recuperare la lunghezza della coda

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo
**FetchAttributes** chiede al servizio di accodamento di
recuperare gli attributi della coda, incluso il numero di messaggi. La proprietà **ApproximateMethodCount**
 restituisce l'ultimo valore recuperato dal metodo
**FetchAttributes**
, senza chiamare il servizio di accodamento.

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

## <a name="delete-queue"></a><span class="short-header">Eliminare una coda</span>Procedura: Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo
**Delete** sull'oggetto coda.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre
informazioni sulle attività di archiviazione più complesse.

-   Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio di accodamento:
    -   [Informazioni di riferimento sulla libreria client di archiviazione per .NET][]
    -   [Informazioni di riferimento sulle API REST][]
-   Per ulteriori informazioni sulle attività avanzate che è possibile eseguire con Archiviazione di Azure, vedere la pagina relativa all'[archiviazione e all'accesso di dati in Azure][].
-   Per altre informazioni su come usare Archiviazione di Azure nei processi back-end per Siti Web di Azure, vedere [Introduzione all'uso dell'SDK di Processi Web di Azure][].
-   Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
    -   Per archiviare dati strutturati, utilizzare [Archiviazione tabelle][].
    -   Per archiviare dati non strutturati, utilizzare [Archiviazione BLOB][].
    -   Per archiviare dati relazionali, utilizzare [Database SQL][].

  [Passaggi successivi]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK per .NET]: /en-us/downloads/
  [Procedura: Accedere all'archiviazione di accodamento a livello di codice]: #configure-access
  [Informazioni sull'archiviazione di accodamento]: #what-is
  [Concetti]: #concepts
  [Creare un account di archiviazione di Azure]: #create-account
  [Configurazione di una stringa di connessione di archiviazione di Azure]: #setup-connection-string
  [Procedura: Creare una coda]: #create-queue
  [Procedura: Inserire un messaggio in una coda]: #insert-message
  [Procedura: Visualizzare il messaggio successivo]: #peek-message
  [Procedura: Cambiare il contenuto di un messaggio in coda]: #change-contents
  [Procedura: Rimuovere il messaggio successivo dalla coda]: #get-message
  [Procedura: Utilizzare opzioni aggiuntive per rimuovere i messaggi dalla coda]: #advanced-get
  [Procedura: Recuperare la lunghezza della coda]: #get-queue-length
  [Procedura: Eliminare una coda]: #delete-queue
  [howto-queue-storage]: ../includes/howto-queue-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [Centro per sviluppatori .NET]: http://www.windowsazure.com/en-us/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [CloudStorageAccount]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Informazioni di riferimento sulla libreria client di archiviazione per .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Informazioni di riferimento sulle API REST]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
  [archiviazione e all'accesso di dati in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Introduzione all'uso dell'SDK di Processi Web di Azure]: /it-it/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Archiviazione tabelle]: /it-it/documentation/articles/storage-dotnet-how-to-use-tables/
  [Archiviazione BLOB]: /it-it/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Database SQL]: /it-it/documentation/articles/sql-database-dotnet-how-to-use/
