<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Creazione del ruolo di lavoro B (mittente posta elettronica) per l'applicazione Azure Email Service - 5 di 5

Questa è la quinta di una serie di cinque esercitazioni in cui viene illustrato come creare e distribuire l'applicazione di esempio Azure Email Service. Per informazioni sull'applicazione e sulla serie di esercitazioni, vedere la [prima esercitazione della serie][].

In questa esercitazione si apprenderà come:

-   Aggiungere un ruolo di lavoro a un progetto di servizio cloud.
-   Eseguire il polling di una coda ed elaborare gli elementi di lavoro dalla coda.
-   Inviare messaggi di posta elettronica utilizzando SendGrid.
-   Gestire arresti pianificati tramite override del metodo `OnStop`.
-   Gestire arresti non pianificati verificando che non vengano inviati messaggi di posta elettronica duplicati.

## Segmenti dell'esercitazione

-   [Aggiunta del progetto del ruolo di lavoro B alla soluzione][]
-   [Aggiunta di un riferimento al progetto Web][]
-   [Aggiunta del pacchetto NuGet di SendGrid al progetto][]
-   [Aggiunta delle impostazioni del progetto][]
-   [Aggiunta del codice da eseguire all'avvio del ruolo di lavoro][]
-   [Test del ruolo di lavoro B][]
-   [Passaggi successivi][]

## <a name="addworkerrole"></a><span class="short-header">Aggiunta del ruolo di lavoro B</span>Aggiunta del progetto del ruolo di lavoro B alla soluzione

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di servizio cloud, quindi scegliere **Nuovo progetto di ruolo di lavoro**.

    ![Menu Nuovo progetto di ruolo di lavoro][]

2.  Nella finestra di dialogo **Aggiungi nuovo progetto di ruolo** selezionare **C#**, quindi selezionare **Ruolo di lavoro**, denominare il progetto WorkerRoleB e fare clic su **Aggiungi**.

    ![Finestra di dialogo Nuovo progetto di ruolo][]

## <a name="addreference"></a>Aggiunta di un riferimento al progetto Web

Poiché le classi di entità vengono definite nel progetto Web, è necessario aggiungere un apposito riferimento. Nel ruolo di lavoro B verranno utilizzate le classi di entità per leggere e scrivere dati nelle tabelle di Azure utilizzate dall'applicazione.

1.  Fare clic con il pulsante destro del mouse sul progetto WorkerRoleB e scegliere **Aggiungi - Riferimento**.

2.  In **Gestione riferimenti** aggiungere un riferimento al progetto MvcWebRole.

    ![Aggiunta di un riferimento a MvcWebRole][]

## <a name="addsendgrid"></a>Aggiunta del pacchetto NuGet di SendGrid al progetto

Per inviare la posta elettronica tramite SendGrid è necessario installare il pacchetto NuGet di SendGrid.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto WorkerRoleB e scegliere **Manage NuGet Packages**.

    ![Manage NuGet Packages][]

2.  Nella finestra di dialogo **Manage NuGet Packages** selezionare la scheda **Online**, immettere "sendgrid" nella casella di ricerca e quindi premere Invio.

3.  Fare clic su **Installa** sul pacchetto **SendGrid**.

    ![Installazione del pacchetto SendGrid][]

4.  Chiudere la finestra di dialogo.

## <a name="addsettings"></a>Aggiunta delle impostazioni del progetto

Come per il ruolo di lavoro A, affinché sia possibile utilizzare il ruolo di lavoro B con tabelle, code e blob sono necessarie le credenziali dell'account di archiviazione. Inoltre, per poter inviare la posta elettronica, è necessario che al ruolo di lavoro siano associate le credenziali da incorporare nelle chiamate al servizio SendGrid. Infine, allo scopo di creare un collegamento di annullamento della sottoscrizione da includere nei messaggi di posta elettronica inviati, è necessario che il ruolo di lavoro conosca l'URL dell'applicazione. Questi valori sono archiviati nelle impostazioni del progetto.

Per ottenere le credenziali dell'account di archiviazione la procedura è analoga a quella appresa nella [terza esercitazione][].

1.  In **Esplora soluzioni**, nella sezione **Ruoli** del progetto cloud, fare clic con il pulsante destro del mouse su **WorkerRoleB** e scegliere **Proprietà**.

2.  Fare clic sulla scheda **Impostazioni**.

3.  Assicurarsi che nell'elenco a discesa **Configurazione del servizio** sia selezionata l'opzione **Tutte le configurazioni**.

4.  Selezionare la scheda **Impostazioni** e quindi fare clic su **Aggiungi impostazione**.

5.  Immettere "StorageConnectionString" nella colonna **Nome**.

6.  Scegliere **Stringa di connessione** nell'elenco a discesa **Tipo**.

7.  Fare clic sui puntini di sospensione (**...**) all'estrema destra della riga per aprire la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

8.  Nella finestra di dialogo **Crea stringa di connessione di archiviazione** fare clic sul pulsante di opzione **Emulatore di archiviazione di Azure** e quindi su **OK**.

Quindi, creare e configurare le tre nuove impostazioni utilizzate solo dal ruolo di lavoro B.

1.  Nella scheda **Settings** della finestra **Properties** fare clic su **Add Setting**, quindi aggiungere tre nuove impostazioni di tipo **String**:

    -   **Name**: SendGridUserName, **Value**: il nome utente di SendGrid stabilito nella [seconda esercitazione][].

    -   **Name**: SendGridPassword, **Value**: la password di SendGrid.

    -   **Name**: AzureMailServiceURL, **Value**: l'URL di base dell'applicazione al momento della distribuzione; ad esempio: <http://sampleurl.cloudapp.net>.

    ![Nuove impostazioni nel progetto WorkerRoleB][]

## <a name="addcode"></a>Aggiunta del codice da eseguire all'avvio del ruolo di lavoro

1.  Nel progetto WorkerRoleB eliminare WorkerRole.cs.

2.  Nel progetto WorkerRoleB aggiungere il file WorkerRoleB.cs al progetto scaricato.

3.  Compilare la soluzione.

    Se viene restituito un errore di compilazione, è possibile che siano installate più versioni del pacchetto NuGet di Archiviazione di Azure. In questo caso, per risolvere il problema, accedere a Gestione pacchetti NuGet per la soluzione, selezionare Pacchetti installati e scorrere verso il basso per verificare se sono presenti istanze del pacchetto di Archiviazione di Azure. Selezionare la voce Azure Storage 4.0.0 ed eliminarla dai progetti in cui è installata. Selezionare quindi la voce Azure Storage 3.0.x e installarla nei progetti in cui non è presente. Chiudere e riavviare Visual Studio, quindi compilare nuovamente la soluzione.

4.  Assicurarsi che il progetto del servizio cloud sia ancora il progetto di avvio per la soluzione.

### Metodo Onstart

Come già osservato nel ruolo di lavoro A, il metodo `OnStart` inizializza le classi di contesto necessarie per usare le entità di Archiviazione di Azure. Verifica inoltre l'esistenza di tutte le tabelle, le code e i contenitori BLOB necessari nel metodo `Run`.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

La differenza confrontata con il ruolo di lavoro A è l'aggiunta del contenitore blob e della coda di sottoscrizione tra le risorse da creare qualora non esistessero già. Il contenitore BLOB verrà utilizzato per ottenere i file che contengono il testo HTML e normale per il corpo del messaggio di posta elettronica. La coda di sottoscrizione viene utilizzata per inviare messaggi di posta elettronica di conferma della sottoscrizione.

### Metodo Run

Il metodo `Run` elabora gli elementi di lavoro da due code: la coda utilizzata per i messaggi inviati agli elenchi di posta elettronica (elementi di lavoro creati dal ruolo di lavoro A) e la coda utilizzata per i messaggi di posta elettronica di conferma della sottoscrizione (elementi di lavoro creati dal metodo API di sottoscrizione nel progetto MvcWebRole).

        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Questo codice viene eseguito in un ciclo infinito fino all'arresto del ruolo di lavoro. Un eventuale elemento di lavoro rilevato nella coda principale verrà elaborato e quindi verrà verificata la coda di sottoscrizione.

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

Se non vi sono elementi in attesa in nessuna coda, il codice resta inattivo per 60 secondi prima di continuare con il ciclo.

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

Lo scopo della sospensione è quello di ridurre al minimo i costi delle transazioni di archiviazione Azure, come illustrato nell'[esercitazione precedente][].

Quando un elemento della coda viene estratto tramite il metodo [GetMessage][] diverrà visibile per 30 secondi a tutti gli altri ruoli lavoro e Web che accedono alla coda. È in questo modo che è possibile assicurarsi che solo un'istanza del ruolo di lavoro selezioni eventuali messaggi in coda per elaborarli. È possibile impostare esplicitamente questo intervallo di *exclusive lease* (la durata per cui l'elemento della coda è invisibile) passando un parametro di [timeout di visibilità][] al metodo `GetMessage`. Se il ruolo di lavoro impiega oltre 30 secondi per elaborare un messaggio della coda, sarà opportuno aumentare l'intervallo di lease esclusivo allo scopo di impedire l'elaborazione dello stesso messaggio da parte di altre istanze del ruolo.

D'altro canto, non è consigliabile impostare l'intervallo di lease esclusivo su un valore eccessivamente lungo. Ad esempio, se si imposta questo intervallo su 48 ore e il ruolo di lavoro si arresta in maniera imprevista dopo aver rimosso un messaggio dalla coda, un altro ruolo di lavoro non sarà in grado di elaborare il messaggio per 48 ore. La durata massima dell'intervallo di lease esclusivo è di 7 giorni.

Il metodo [GetMessages][] (notare la "s" alla fine del nome) è utile per estrarre fino a 32 messaggi dalla coda con una sola chiamata. Ogni accesso alla coda implica un costo della transazione contenuto, che rimane invariato qualora vengano restituiti 32 messaggi o zero messaggi. Il codice seguente recupera fino a 32 messaggi con una sola chiamata e quindi li elabora.

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

Quando si usa `GetMessages` per rimuovere più messaggi, assicurarsi che il timeout di visibilità offra all'applicazione tempo sufficiente a elaborare tutti i messaggi. Alla scadenza del periodo di visibilità altre istanze del ruolo potranno accedere al messaggio e, in tal caso, la prima istanza non sarà in grado di eliminarlo al termine dell'elaborazione dell'elemento di lavoro.

### Metodo ProcessQueueMessage

Il metodo `Run` chiama `ProcessQueueMessage` quando rileva un elemento di lavoro nella coda principale:

        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

I messaggi non elaborabili sono quelli che, quando elaborati dall'applicazione, causano la generazione di un'eccezione. Se un messaggio è stato estratto dalla coda più di cinque volte, si presume che non sia possibile elaborarlo e verrà rimosso dalla coda, in modo che non si continui a tentarne l'elaborazione. È consigliabile che, piuttosto che eliminarlo, le applicazioni di produzione prendano in considerazione lo spostamento del messaggio non elaborabile a una coda di messaggi non recapitabili.

Il codice analizza il messaggio in coda nelle chiavi di partizione e di riga necessarie per recuperare la riga SendEmail e un contrassegno di riavvio.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

Se l'elaborazione di questo messaggio è stata riavviata dopo un arresto imprevisto, il codice controlla la tabella `messagearchive` per determinare se il messaggio è stato già inviato. In caso affermativo, il codice elimina la riga `SendEmail`, se esistente, quindi elimina il messaggio in coda.

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

Dopo questa operazione, si recupera la riga `SendEmail` dalla tabella `message`. La riga include tutte le informazioni necessarie per inviare il messaggio di posta elettronica, tranne i BLOB che contengono il corpo del messaggio in formato HTML e testo semplice.

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

Il codice invia quindi il messaggio di posta elettronica e archivia la riga `SendEmail`.

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

Non è possibile eseguire lo spostamento della riga alla tabella messagearchive in una transazione in quanto incide su più tabelle.

Infine, se tutte le altre operazioni hanno avuto esito positivo, il messaggio in coda verrà eliminato.

            sendEmailQueue.DeleteMessage(msg);

### Metodo SendEmailToList

L'operazione effettiva di invio del messaggio di posta elettronica tramite SendGrid viene eseguita dal metodo `SendEmailToList`. Se si desidera utilizzare un servizio diverso da SendGrid, sarà sufficiente modificare il codice in questo metodo.

**Nota:** Se nelle impostazioni del progetto sono presenti credenziali non valide, la chiamata a SendGrid non riuscirà, ma l'applicazione non riceverà alcuna indicazione dell'esito negativo. Se si utilizza SendGrid in un'applicazione di produzione, si consideri la configurazione di credenziali separate per l'API Web in modo da evitare la produzione di errori non segnalati quando un amministratore cambia la password del proprio account SendGrid. Per ulteriori informazioni, vedere l'articolo relativo alle [credenziali per più account di SendGrid MultiAuth][]. È possibile configurare le credenziali all'indirizzo [][]<https://sendgrid.com/credentials></a>.

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

Nel metodo `GetBlobText` il codice ottiene il valore delle dimensioni del BLOB e usa tale valore per inizializzare l'oggetto `MemoryStream` per motivi di prestazioni. Se le dimensioni non vengono fornite, l'oggetto `MemoryStream` allocherà 256 byte, per poi allocarne altri 512 quando il download supererà il primo valore impostato, e così via, raddoppiando la quantità allocata ogni volta. Per un blob di grandi dimensioni questo processo si rivelerebbe inefficiente se confrontato all'allocazione della quantità corretta all'inizio del download.

### Metodo ProcessSubscribeQueueMessage

Il metodo `Run` chiama `ProcessSubscribeQueueMessage` quando trova un elemento di lavoro nella coda di sottoscrizione:

        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

In questo metodo vengono eseguite le operazioni seguenti:

-   Se il messaggio non è elaborabile, viene registrato e quindi eliminato.
-   Il GUID del sottoscrittore viene ricavato dal messaggio in coda.
-   Il GUID viene quindi utilizzato per ottenere informazioni sul sottoscrittore dalla tabella MailingList.
-   Viene inviato un messaggio di posta elettronica di conferma al nuovo sottoscrittore.
-   Il messaggio in coda viene eliminato.

Proprio come per l'invio di messaggi di posta elettronica a elenchi, l'invio effettivo dei messaggi avviene in un metodo separato, che semplifica l'eventuale passaggio a un servizio di posta diverso.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>Test del ruolo di lavoro B

1.  Premere F5 per eseguire l'applicazione.

2.  Andare alla pagina **Messages** per visualizzare il messaggio creato per eseguire il test del ruolo di lavoro A. Dopo qualche minuto, aggiornare la pagina: la riga non sarà più visualizzata nell'elenco in quanto sarà stata archiviata.

3.  Controllare la cartella Posta in arrivo dove si prevede di ricevere il messaggio di posta elettronica. Si noti che potrebbero verificarsi dei ritardi nell'invio dei messaggi di posta elettronica da parte di SendGrid o nel recapito al proprio client di posta elettronica, pertanto potrebbe essere necessario attendere brevemente per visualizzare il messaggio. Potrebbe essere inoltre necessario controllare la cartella della posta indesiderata.

## <a name="nextsteps"></a>Passaggi successivi

È stata creata l'applicazione Azure Email Service da zero e il risultato ottenuto è identico al progetto completato che è stato scaricato. Per eseguire la distribuzione sul cloud, il test nel cloud e promuoverla alla produzione, è possibile utilizzare le stesse procedure illustrate nella [seconda esercitazione][].

Per un'applicazione di esempio che mostra come usare LINQ nelle query del servizio Tabella di Archiviazione di Azure, vedere [PhluffyFotos][].

Per ulteriori informazioni sull'archiviazione Azure, vedere la risorsa seguente:

-   [Articolo sulle][]nozioni fondamentali dell'Archiviazione di Microsoft Azure sul blog di Bruno Terkaly

Per ulteriori informazioni sul servizio tabelle di Azure, vedere le risorse seguenti:

-   [Articolo sulle][1]nozioni fondamentali sull'archiviazione tabelle di Azure nel blog di Bruno Terkaly
-   [Articolo su][]come ottenere il massimo dalle tabelle di Microsoft Azure nel blog del team di Servizio di archiviazione di Azure
-   [Articolo su come utilizzare il servizio di archiviazione tabelle in .NET][]
-   [Articolo di][]approfondimento sulle tabelle della libreria client di archiviazione 2.0 di Microsoft Azure sul blog del team di Servizio di archiviazione di Azure
-   [Articolo sulla progettazione di una strategia di partizionamento scalabile per l'archiviazione tabelle di Azure][]

Per ulteriori informazioni sul servizio di accodamento di Azure e sulle code del bus di servizio Azure, vedere le risorse seguenti:

-   [Articolo sullo schema di lavoro incentrato sulle code (creazione di applicazioni per cloud funzionanti con Microsoft Azure)][]
-   [Code di Azure e Azure Service Bus: confronto e contrapposizioni][]
-   [Articolo su come utilizzare il servizio di archiviazione di accodamento in .NET][]

Per ulteriori informazioni sul servizio Blob di Azure, vedere le risorse seguenti:

-   [Articolo sull'archiviazione blob non strutturata (creazione di applicazioni per cloud funzionanti con Microsoft Azure)][]
-   [Come utilizzare il servizio di archiviazione BLOB di Azure in .NET][]

Per ulteriori informazioni sul ridimensionamento automatico dei ruoli del servizio cloud di Azure, vedere le risorse seguenti:

-   [Come utilizzare il blocco applicazione per la scalabilità automatica][]
-   [Scalabilità automatica e Azure][]
-   [Articolo sulla][]creazione di soluzioni elastiche e scalabili automaticamente con Azure (video del Channel 9 di MSDN)

## <a name="Acknowledgments"></a><span class="short-header">Ringraziamenti</span>Ringraziamenti

Queste esercitazioni e l'applicazione di esempio sono state scritte da [Rick Anderson][] e da Tom Dykstra. Cogliamo l'occasione per ringraziare le seguenti persone per la loro assistenza:

-   Barry Dorrans (Twitter [@blowdart][])
-   [Cory Fowler][] (Twitter [@SyntaxC4][] )
-   [Joe Giardino][]
-   Don Glover
-   Jai Haridas
-   [Scott Hunter][] (Twitter: [@coolcsh][])
-   [Brian Swan][]
-   [Daniel Wang][]
-   I membri del Developer Advisory Council che hanno fornito il loro feedback:
-   Damir Arh
-   Jean-Luc Boucho
-   Carlos dos Santos
-   Mike Douglas
-   Robert Fite
-   Gianni Rosa Gallina
-   Fabien Lavocat
-   Karl Ots
-   Carlos-Alejandro Perez
-   Sunao Tomita
-   Perez Jones Tsisah
-   Michiel van Otegem

  [prima esercitazione della serie]: /it-it/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Aggiunta del progetto del ruolo di lavoro B alla soluzione]: #addworkerrole
  [Aggiunta di un riferimento al progetto Web]: #addreference
  [Aggiunta del pacchetto NuGet di SendGrid al progetto]: #addsendgrid
  [Aggiunta delle impostazioni del progetto]: #addsettings
  [Aggiunta del codice da eseguire all'avvio del ruolo di lavoro]: #addcode
  [Test del ruolo di lavoro B]: #testing
  [Passaggi successivi]: #nextsteps
  [Menu Nuovo progetto di ruolo di lavoro]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [Finestra di dialogo Nuovo progetto di ruolo]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [Aggiunta di un riferimento a MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [Manage NuGet Packages]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Installazione del pacchetto SendGrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [terza esercitazione]: /it-it/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [seconda esercitazione]: /it-it/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Nuove impostazioni nel progetto WorkerRoleB]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [esercitazione precedente]: /it-it/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/it-it/library/windowsazure/ee741827.aspx
  [timeout di visibilità]: http://msdn.microsoft.com/it-it/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [credenziali per più account di SendGrid MultiAuth]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Articolo sulle]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [1]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [Articolo su]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Articolo su come utilizzare il servizio di archiviazione tabelle in .NET]: http://www.windowsazure.com/it-it/develop/net/how-to-guides/table-services/
  [Articolo di]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Articolo sulla progettazione di una strategia di partizionamento scalabile per l'archiviazione tabelle di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/hh508997.aspx
  [Articolo sullo schema di lavoro incentrato sulle code (creazione di applicazioni per cloud funzionanti con Microsoft Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Code di Azure e Azure Service Bus: confronto e contrapposizioni]: http://msdn.microsoft.com/it-it/library/windowsazure/hh767287.aspx
  [Articolo su come utilizzare il servizio di archiviazione di accodamento in .NET]: /it-it/develop/net/how-to-guides/queue-service/
  [Articolo sull'archiviazione blob non strutturata (creazione di applicazioni per cloud funzionanti con Microsoft Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [Come utilizzare il servizio di archiviazione BLOB di Azure in .NET]: /it-it/develop/net/how-to-guides/blob-storage/
  [Come utilizzare il blocco applicazione per la scalabilità automatica]: /it-it/develop/net/how-to-guides/autoscaling/
  [Scalabilità automatica e Azure]: http://msdn.microsoft.com/it-it/library/hh680945(v=PandP.50).aspx
  [Articolo sulla]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Cory Fowler]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Joe Giardino]: http://blogs.msdn.com/b/windowsazurestorage/
  [Scott Hunter]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Brian Swan]: http://blogs.msdn.com/b/brian_swan/
  [Daniel Wang]: http://blogs.msdn.com/b/daniwang/
