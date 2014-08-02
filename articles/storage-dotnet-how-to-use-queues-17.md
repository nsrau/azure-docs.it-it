<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service (2.0)" pageTitle="How to use the queue storage service | Microsoft Azure" metaKeywords="Get started Azure queue, Azure asynchronous processing, Azure queue, Azure queue storage, Azure queue .NET, Azure queue storage .NET, Azure queue C#, Azure queue storage C#" description="Learn how to use the Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Come utilizzare il servizio di archiviazione di accodamento
===========================================================

[versione 1.7](/en-us/develop/net/how-to-guides/queue-service-v17/ "versione 1.7") [versione 2.0](/en-us/develop/net/how-to-guides/queue-service/ "versione 2.0")

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione di accodamento di Azure. Negli esempi, scritti in C\#, viene utilizzata l'API .NET. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il **recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed eliminazione di code**. Per ulteriori informazioni sulle code, fare riferimento alla sezione [Passaggi successivi](#next-steps).

Sommario
--------

-   [Informazioni sull'archiviazione di accodamento](#what-is)
-   [Concetti](#concepts)
-   [Creazione di un account di archiviazione di Azure](#create-account)
-   [Configurazione di una stringa di connessione di archiviazione di Azure](#setup-connection-string)
-   [Procedura: Accedere alle code a livello di codice tramite .NET](#access)
-   [Procedura: Creare una coda](#create-queue)
-   [Procedura: Inserire un messaggio in una coda](#insert-message)
-   [Procedura: Visualizzare il messaggio successivo](#peek-message)
-   [Procedura: Cambiare il contenuto di un messaggio in coda](#change-contents)
-   [Procedura: Rimuovere il messaggio successivo dalla coda](#get-message)
-   [Procedura: Utilizzare opzioni aggiuntive per rimuovere i messaggi dalla coda](#advanced-get)
-   [Procedura: Recuperare la lunghezza della coda](#get-queue-length)
-   [Procedura: Eliminare una coda](#delete-queue)
-   [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Creazione di un accountCreazione di un account di archiviazione di Azure
------------------------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configurazione di una stringa di connessioneConfigurazione di una stringa di connessione di archiviazione di Azure
------------------------------------------------------------------------------------------------------------------

Nell'API di archiviazione di Azure per .NET è possibile utilizzare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. È possibile inserire la stringa di connessione di archiviazione in un file di configurazione, anziché impostarla come hardcoded nel codice:

-   Quando si utilizza Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`).
-   Quando si utilizza Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ovvero il file `web.config`.

In entrambi i casi è possibile recuperare la stringa di connessione utilizzando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### Configurazione della stringa di connessione per l'utilizzo con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione.

Per configurare la stringa di connessione nella configurazione dei servizi di Azure:

1.  All'interno di Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul ruolo Web o sul ruolo di lavoro nella cartella **Ruoli** del progetto di distribuzione di Azure, quindi scegliere **Proprietà**.  
     ![BLOB5](./media/storage-dotnet-how-to-use-queues-17/blob5.png)

2.  Fare clic sulla scheda **Impostazioni** e quindi sul pulsante **Aggiungi impostazione**.  
     ![BLOB6](./media/storage-dotnet-how-to-use-queues-17/blob6.png)

    Nella griglia delle impostazioni verrà visualizzata una nuova voce **Impostazione1**.

3.  Nell'elenco a discesa **Tipo** della nuova voce **Impostazione1** selezionare **Stringa di connessione**.  
     ![BLOB7](./media/storage-dotnet-how-to-use-queues-17/blob7.png)

4.  Fare clic sul pulsante **...** a destra della voce **Impostazione1**. Verrà visualizzata la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

5.  Scegliere se si desidera utilizzare come destinazione l'emulatore di archiviazione (servizio Archiviazione di Azure simulato nel computer locale) oppure un effettivo account di archiviazione nel cloud. Il codice riportato in questa guida funziona con entrambe le opzioni. Se si desidera archiviare i dati BLOB nell'account di archiviazione creato in precedenza in Azure, immettere il valore di **Chiave di accesso primaria** copiato dal passaggio precedente di questa esercitazione.

    ![BLOB8](./media/storage-dotnet-how-to-use-queues-17/blob8.png)

6.  Modificare il valore **Impostazione1** della voce **Nome** specificando un nome più descrittivo, ad esempio **StorageConnectionString**. A questa stringa di connessione verrà fatto riferimento più avanti nel codice in questa guida.

    ![BLOB9](./media/storage-dotnet-how-to-use-queues-17/blob9.png)

### Configurazione della stringa di connessione quando si utilizza Siti Web o Macchine virtuali

Con Siti Web o Macchine virtuali è consigliabile utilizzare il sistema di configurazione .NET, ovvero il file `web.config`. Per archiviare la stringa di connessione, utilizzare l'elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Per ulteriori informazioni sulle stringhe di connessione di archiviazione, leggere [Configurazione delle stringhe di connessione](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx).

È ora possibile eseguire le procedure di questa guida.

Accedere a livello di codiceProcedura: Accedere alle code a livello di codice tramite .NET
------------------------------------------------------------------------------------------

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C\# in cui si desidera accedere ad Archiviazione di Azure a livello di codice:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

È possibile utilizzare i tipi **CloudStorageAccount** e **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Creare una codaProcedura: Creare una coda
-----------------------------------------

Per ottenere oggetti di riferimento per le code, è possibile utilizzare un oggetto **CloudQueueClient**. Il codice seguente consente di creare un oggetto **CloudQueueClient**. In tutto il codice di questa guida viene utilizzata una stringa di connessione di archiviazione archiviata nel servizio di configurazione dell'applicazione Azure. Sono inoltre disponibili altri modi per creare un oggetto **CloudStorageAccount**. Per informazioni dettagliate, vedere la documentazione relativa a [CloudStorageAccount](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx).

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilizzare l'oggetto **queueClient** per ottenere un riferimento alla coda da utilizzare. È possibile creare la coda se non esiste già.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

Inserire un messaggioProcedura: Inserire un messaggio in una coda
-----------------------------------------------------------------

Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto **CloudQueueMessage**. Quindi, chiamare il metodo **AddMessage**. È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di **byte**. Di seguito è riportato il codice che consente di creare una coda (se non esiste già) e di inserire il messaggio 'Hello, World'.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

Visualizzare il messaggio successivoProcedura: Visualizzare il messaggio successivo
-----------------------------------------------------------------------------------

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

Cambiare il contenuto di un messaggioProcedura: Cambiare il contenuto di un messaggio in coda
---------------------------------------------------------------------------------------------

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. Il codice seguente consente di aggiornare il messaggio in coda con nuovo contenuto e di impostarne il timeout di visibilità per prolungarlo di altri 60 secondi. In questo modo lo stato del lavoro associato al messaggio viene salvato e il client ha a disposizione un altro minuto per continuare l'elaborazione del messaggio. È possibile utilizzare questa tecnica per tenere traccia di flussi di lavoro composti da più passaggi nei messaggi in coda, senza la necessità di ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware o software. In genere, è consigliabile mantenere anche un conteggio dei tentativi, in modo da eliminare i messaggi per cui vengono effettuati più di *n* tentativi. In questo modo è possibile evitare che un messaggio attivi un errore dell'applicazione ogni volta che viene elaborato.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
        queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // visible immediately
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

Rimuovere il messaggio successivo dalla codaProcedura: Rimuovere il messaggio successivo dalla coda
---------------------------------------------------------------------------------------------------

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

Usufruire di opzioni aggiuntive per la rimozione di messaggi dalla codaProcedura: Utilizzare opzioni aggiuntive per rimuovere i messaggi dalla coda
---------------------------------------------------------------------------------------------------------------------------------------------------

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **GetMessages** per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo **foreach**. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
        {
            // Do processing for all messages in less than 5 minutes, deleting each message after processing
        queue.DeleteMessage(message);
        }

Recuperare la lunghezza della codaProcedura: Recuperare la lunghezza della coda
-------------------------------------------------------------------------------

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **RetrieveApproximateMessageCount** richiede al servizio di accodamento di conteggiare quanti messaggi sono presenti in una coda. Il conteggio è solo un'approssimazione, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento. La proprietà **ApproximateMethodCount** restituisce l'ultimo valore recuperato da **RetrieveApproximateMessageCount**, senza chiamare il servizio di accodamento.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Retrieve the approximate message count
    int freshMessageCount = queue.RetrieveApproximateMessageCount();

    // Retrieve the cached approximate message count
    int? cachedMessageCount = queue.ApproximateMessageCount;

Eliminare una codaProcedura: Eliminare una coda
-----------------------------------------------

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **Delete** sull'oggetto coda.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue
    queue.Delete();

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio di accodamento:
    -   [Informazioni di riferimento sulla libreria client .NET](http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [Informazioni di riferimento sulle API REST](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Per ulteriori informazioni sulle attività avanzate che è possibile eseguire con Archiviazione di Azure, vedere la pagina relativa all'[archiviazione e all'accesso ai dati in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
    -   Per archiviare dati strutturati, utilizzare [Archiviazione tabelle](/en-us/develop/net/how-to-guides/table-services/).
    -   Per archiviare dati non strutturati, utilizzare [Archiviazione BLOB](/en-us/develop/net/how-to-guides/blob-storage/).
    -   Per archiviare dati relazionali, utilizzare [Database SQL](/en-us/develop/net/how-to-guides/sql-database/).

