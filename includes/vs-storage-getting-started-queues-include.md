##### Creare una coda
Per ottenere oggetti di riferimento per le code, è possibile usare un oggetto **CloudQueueClient**. Il codice seguente consente di creare un oggetto **CloudQueueClient**. In tutto il codice di questo argomento viene usata una stringa di connessione di archiviazione archiviata nel servizio di configurazione dell'applicazione Azure. Sono inoltre disponibili altri modi per creare un oggetto **CloudStorageAccount**. Per informazioni dettagliate, vedere la documentazione di [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount").

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilizzare l'oggetto **queueClient** per ottenere un riferimento alla coda da utilizzare. Il codice tenta di fare riferimento a una coda denominata “myqueue”. Se non è in grado di trovare una coda con tale nome, ne crea una.

    // Get a reference to a queue named “myqueue”.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // If the queue isn’t already there, then create it.
    queue.CreateIfNotExists();

**NOTA:**anteporre questa parte ai codici riportati nelle seguenti sezioni.

##### Inserire un messaggio in una coda
Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto **CloudQueueMessage**. Quindi, chiamare il metodo AddMessage(). È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di byte. Di seguito è riportato il codice che consente di creare una coda (se non esiste già) e di inserire il messaggio 'Hello, World'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

##### Visualizzare il messaggio successivo
È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo PeekMessage().

    // Peek at the next message in the queue.
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display the message.
    Console.WriteLine(peekedMessage.AsString);

##### Rimuovere il messaggio successivo
Il codice può rimuovere un messaggio da una coda in due passaggi.

1. Chiamare GetMessage() per ottenere il messaggio successivo in una coda. Un messaggio restituito da GetMessage() diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. 
2. Per completare la rimozione del messaggio dalla coda, chiamare DeleteMessage(). 

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice seguente chiama DeleteMessage() immediatamente dopo l'elaborazione del messaggio.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    // Process the message in less than 30 seconds, and then delete the message.
    queue.DeleteMessage(retrievedMessage);

[Ulteriori informazioni sull'archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/) Vedere anche [Esplorazione delle risorse di archiviazione in Esplora server](http://msdn.microsoft.com/library/azure/ff683677.aspx).

<!---HONumber=62-->