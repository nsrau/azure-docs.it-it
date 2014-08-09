<properties linkid="dev-net-how-to-use-queue-storage-service-java" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Java) | Microsoft Azure" metaKeywords="Azure Queue Service, Azure Queue storage service, queues peeking, queues insert messages, queues get messages, queues delete messages, create queues, delete queues, Queue service Java" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Queue storage service from Java" authors="" solutions="" manager="" editor="" />

Come utilizzare il servizio di archiviazione di accodamento di Java
===================================================================

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione di accodamento di Azure. Gli esempi sono scritti in Java e utilizzano [Azure SDK for Java](http://www.windowsazure.com/it-it/develop/java/). Gli scenari presentati includono l'inserimento, la visualizzazione, il recupero e l'eliminazione dei messaggi in coda, oltre alle procedure di creazione ed eliminazione di code. Per ulteriori informazioni sulle code, fare riferimento alla sezione [Passaggi successivi](#NextSteps).

Sommario
--------

-   [Informazioni sull'archiviazione di accodamento](#what-is)
-   [Concetti](#Concepts)
-   [Creazione di un account di archiviazione di Azure](#CreateAccount)
-   [Creazione di un'applicazione Java](#CreateApplication)
-   [Configurazione dell'applicazione per l'accesso all'archiviazione di accodamento](#ConfigureStorage)
-   [Configurazione di una stringa di connessione di archiviazione di Azure](#ConnectionString)
-   [Procedura: Creare una coda](#create-queue)
-   [Procedura: Aggiungere un messaggio a una coda](#add-message)
-   [Procedura: Visualizzare il messaggio successivo](#peek-message)
-   [Procedura: Rimuovere il messaggio successivo dalla coda](#dequeue-message)
-   [Procedura: Cambiare il contenuto di un messaggio in coda](#change-message)
-   [Opzioni aggiuntive per rimuovere i messaggi dalla coda](#additional-options)
-   [Procedura: Recuperare la lunghezza della coda](#get-queue-length)
-   [Procedura: Eliminare una coda](#delete-queue)
-   [Passaggi successivi](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Creazione di un account di archiviazione di Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creazione di un'applicazione Java
---------------------------------

In questa guida si utilizzeranno le funzionalità di archiviazione che possono essere eseguite in un'applicazione Java in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in Azure. Si presuppone che sia stato scaricato e installato Java Development Kit (JDK), che siano stati installati Azure SDK e le librerie di Azure per Java in base alle istruzioni riportate in [Download di Azure SDK for Java](http://www.windowsazure.com/it-it/develop/java/) e che sia stato creato un account di archiviazione di Azure nella sottoscrizione di Azure. Per creare l'applicazione, è possibile utilizzare qualsiasi strumento di sviluppo, incluso il Blocco note. È sufficiente essere in grado di compilare un progetto Java e di fare riferimento alle librerie di Azure per Java.

Configurazione dell'applicazione per l'accesso all'archiviazione di accodamento
-------------------------------------------------------------------------------

Aggiungere le istruzioni import seguenti all'inizio del file Java in cui si desidera utilizzare le API di archiviazione di Azure per accedere alle code:

    // Include the following imports to use queue APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.queue.client.*;

Configurazione di una stringa di connessione di archiviazione di Azure
----------------------------------------------------------------------

I client di archiviazione di Azure utilizzano le stringhe di connessione di archiviazione per archiviare endpoint e credenziali per l'accesso ai servizi di gestione dati. Quando si esegue un'applicazione client, è necessario specificare la stringa di connessione di archiviazione nel formato seguente, utilizzando il nome dell'account di archiviazione e la chiave di accesso primaria relativa all'account di archiviazione riportata nel portale di gestione per i valori di *AccountName* e *AccountKey*. In questo esempio viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In un'applicazione in esecuzione in un ruolo di Azure, questa stringa può essere archiviata nel file di configurazione del servizio ServiceConfiguration.cscfg ed è accessibile con una chiamata al metodo RoleEnvironment.getConfigurationSettings. Nell'esempio seguente viene recuperata la stringa di connessione da un elemento **Setting** denominato *StorageConnectionString* nel file di configurazione del servizio:

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Procedura: Creare una coda
--------------------------

Per ottenere oggetti di riferimento per le code, è possibile utilizzare un oggetto CloudQueueClient. Il codice seguente consente di creare un oggetto CloudQueueClient.

In questa guida tutta la coda utilizza una stringa di connessione di archiviazione dichiarata in uno dei due modi illustrati sopra. Sono inoltre disponibili altri modi per creare gli oggetti CloudStorageAccount. Per informazioni dettagliate, vedere la documentazione relativa a CloudStorageAccount.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

Utilizzare l'oggetto CloudQueueClient per ottenere un riferimento alla coda da utilizzare. È possibile creare la coda se non esiste già.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

Procedura: Aggiungere un messaggio a una coda
---------------------------------------------

Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto CloudQueueMessage. Quindi, chiamare il metodo addMessage. È possibile creare un oggetto CloudQueueMessage da una stringa in formato UTF-8 o da una matrice di byte. Di seguito è riportato il codice che consente di creare una coda (se non esiste già) e di inserire il messaggio "Hello, World".

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);

Procedura: Visualizzare il messaggio successivo
-----------------------------------------------

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando peekMessage.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.peekMessage();

Procedura: Rimuovere il messaggio successivo dalla coda
-------------------------------------------------------

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Chiamando retrieveMessage, si ottiene il messaggio successivo in una coda. Un messaggio restituito da retrieveMessage diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche deleteMessage. Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama deleteMessage immediatamente dopo l'elaborazione del messaggio.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    // Process the message in less than 30 seconds, and then delete the message.
    queue.deleteMessage(retrievedMessage);

Procedura: Cambiare il contenuto di un messaggio in coda
--------------------------------------------------------

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. Il codice seguente consente di aggiornare il messaggio in coda con nuovo contenuto e di impostarne il timeout di visibilità per prolungarlo di altri 60 secondi. In questo modo lo stato del lavoro associato al messaggio viene salvato e il client ha a disposizione un altro minuto per continuare l'elaborazione del messaggio. È possibile utilizzare questa tecnica per tenere traccia di flussi di lavoro composti da più passaggi nei messaggi in coda, senza la necessità di ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware o software. In genere, è consigliabile mantenere anche un conteggio dei tentativi, in modo da eliminare i messaggi per cui vengono effettuati più di n tentativi. In questo modo è possibile evitare che un messaggio attivi un errore dell'applicazione ogni volta che viene elaborato.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage message = queue.retrieveMessage();

    // Modify the message content and set it to be visible in 60 seconds
    message.setMessageContent("Updated contents.");
    EnumSet<MessageUpdateFields> updateFields = 
        EnumSet.of(MessageUpdateFields.CONTENT, MessageUpdateFields.VISIBILITY);
    queue.updateMessage(message, 60, updateFields, null, null);

Opzioni aggiuntive per rimuovere i messaggi dalla coda
------------------------------------------------------

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio.

Nell'esempio di codice seguente viene utilizzato il metodo retrieveMessages per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo **for**. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti (300 secondi). Si noti che i cinque minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla chiamata a retrieveMessages, tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes, 
        // deleting each message after processing.
        queue.deleteMessage(message);
        }

Procedura: Recuperare la lunghezza della coda
---------------------------------------------

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo downloadAttributes richiede al servizio di accodamento diversi valori correnti, incluso un conteggio dei messaggi presenti in una coda. Il conteggio è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento. Il metodo getApproximateMethodCount restituisce l'ultimo valore recuperato dalla chiamata a downloadAttributes, senza chiamare il servizio di accodamento.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Download the approximate message count from the server
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count
    long cachedMessageCount = queue.getApproximateMessageCount();

Procedura: Eliminare una coda
-----------------------------

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo delete sull'oggetto coda.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue
    queue.delete();

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure]
-   Blog del team di Archiviazione di Azure: <http://blogs.msdn.com/b/windowsazurestorage/>

