<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" urlDisplayName="Step 5: Worker Role B" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 5: Worker role B" metaKeywords="Azure tutorial, adding working role cloud service, C# worker role" description="The fifth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role B (email sender) for the Azure Email Service application - 5 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Creazione del ruolo di lavoro B (mittente posta elettronica) per l'applicazione Azure Email Service - 5 di 5.
=============================================================================================================

Questa è la quinta di una serie di cinque esercitazioni in cui viene illustrato come creare e distribuire l'applicazione di esempio Azure Email Service. Per informazioni sull'applicazione e sulla serie di esercitazioni, vedere la [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

In questa esercitazione si apprenderà come:

-   Aggiungere un ruolo di lavoro a un progetto di servizio cloud.
-   Eseguire il polling di una coda ed elaborare gli elementi di lavoro dalla coda.
-   Inviare messaggi di posta elettronica utilizzando SendGrid.
-   Gestire arresti pianificati tramite override del metodo `OnStop`.
-   Gestire arresti non pianificati verificando che non vengano inviati messaggi di posta elettronica duplicati.

Aggiunta del ruolo di lavoro BAggiunta del progetto del ruolo di lavoro B alla soluzione
----------------------------------------------------------------------------------------

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di servizio cloud, quindi scegliere **Nuovo progetto di ruolo di lavoro**.

    ![Menu Nuovo progetto di ruolo di lavoro](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png)

2.  Nella finestra di dialogo **Aggiungi nuovo progetto di ruolo** selezionare **C\#**, quindi selezionare **Ruolo di lavoro**, denominare il progetto WorkerRoleB e fare clic su **Aggiungi**.

    ![Finestra di dialogo Nuovo progetto di ruolo](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png)

Aggiunta di un riferimentoAggiunta di un riferimento al progetto Web
--------------------------------------------------------------------

Poiché le classi di entità vengono definite nel progetto Web, è necessario aggiungere un apposito riferimento. Nel ruolo di lavoro B verranno utilizzate le classi di entità per leggere e scrivere dati nelle tabelle di Azure utilizzate dall'applicazione.

1.  Fare clic con il pulsante destro del mouse sul progetto WorkerRoleB e scegliere **Add Reference**.

    ![Aggiunta di un riferimento nel progetto WorkerRoleB](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png)

2.  In **Gestione riferimenti** aggiungere un riferimento al progetto MvcWebRole oppure al progetto di applicazione Web, se l'interfaccia utente Web è in esecuzione in un sito Web di Azure.

    ![Aggiunta di un riferimento a MvcWebRole](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png)

Aggiunta di un pacchetto SCL 2.0Aggiunta del pacchetto NuGet per la versione 2.0 della libreria client di archiviazione al progetto
-----------------------------------------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Con Visual Studio 2013 è possibile ignorare questa sezione in quanto il pacchetto di archiviazione Azure è stato installato nel nuovo progetto di ruolo di lavoro.

Quando il progetto è stato aggiunto, non è stata automaticamente ottenuta la versione aggiornata del pacchetto NuGet per la libreria client di archiviazione, ma piuttosto la vecchia versione 1.7 del pacchetto, dal momento che era inclusa nel modello del progetto. A questo punto, la soluzione dispone di due versioni del pacchetto NuGet di archiviazione Azure: la versione 2.0 nei progetti MvcWebRole e WorkerRoleA e la versione 1.7 nel progetto WorkerRoleB. È necessario disinstallare la versione 1.7 e installare la versione 2.0 nel progetto WorkerRoleB.

1.  Nel menu **Tools** fare clic su **Library Package Manager**, quindi scegliere **Manage NuGet Packages for Solution**.

2.  Dopo aver selezionato **Installed Packages** nel riquadro a sinistra, scorrere verso il basso fino a visualizzare il pacchetto di archiviazione di Azure,

    che sarà elencato due volte: una per la versione 1.7 e l'altra per la versione 2.0.

3.  Selezionare la versione 1.7 del pacchetto e fare clic su **Manage**.

    Le caselle di controllo dei progetti MvcWebRole e WorkerRoleA sono deselezionate, mentre la casella di controllo del progetto WorkerRoleB è selezionata.

4.  Deselezionare la casella di controllo relativa al progetto WorkerRoleB e quindi fare clic su **OK**.

5.  Quando viene richiesto se si desidera disinstallare i pacchetti dipendenti fare clic su **No**.

    Al termine della disinstallazione nella finestra di dialogo NuGet sarà presente solo la versione 2.0 del pacchetto.

6.  Fare clic su **Manage** per la versione 2.0 del pacchetto.

    Le caselle di controllo dei progetti MvcWebRole e WorkerRoleB sono selezionate, mentre la casella di controllo del progetto WorkerRoleA è deselezionata.

7.  Selezionare la casella di controllo relativa al progetto WorkerRoleA e quindi fare clic su **OK**.

Aggiunta di un riferimento a SCL 1.7Aggiunta di un riferimento a un assembly SCL 1.7
------------------------------------------------------------------------------------

> [WACOM.NOTE] Ignorare questa sezione se è stato installato l'SDK più recente e si utilizza Visual Studio 2013.

La versione 2.0 della libreria client di archiviazione (SCL, Storage Client Library) non include tutti gli strumenti necessari per la diagnostica, quindi è necessario aggiungere un riferimento a un assembly della versione 1.7, come già eseguito in precedenza per gli altri due progetti.

1.  Fare clic con il pulsante destro del mouse sul progetto WorkerRoleB e scegliere **Add Reference**.

2.  Fare clic sul pulsante **Sfoglia** nella parte inferiore della finestra di dialogo.

3.  Passare alla cartella seguente:

         C:\Programmi\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Selezionare *Microsoft.WindowsAzure.StorageClient.dll*, quindi fare clic su **Aggiungi**.

5.  Nella finestra di dialogo **Gestione riferimenti** fare clic su **OK**.

Aggiunta del pacchetto SendGridAggiunta del pacchetto NuGet di SendGrid al progetto
-----------------------------------------------------------------------------------

Per inviare la posta elettronica tramite SendGrid è necessario installare il pacchetto NuGet di SendGrid.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto WorkerRoleB e scegliere **Manage NuGet Packages**.

    ![Manage NuGet Packages](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png)

2.  Nella finestra di dialogo **Manage NuGet Packages** selezionare la scheda **Online**, immettere "sendgrid" nella casella di ricerca e quindi premere Invio.

3.  Fare clic su **Installa** sul pacchetto **SendGrid**.

    ![Installazione del pacchetto SendGrid](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png)

4.  Chiudere la finestra di dialogo.

Aggiunta delle impostazioni del progettoAggiunta delle impostazioni del progetto
--------------------------------------------------------------------------------

Come per il ruolo di lavoro A, affinché sia possibile utilizzare il ruolo di lavoro B con tabelle, code e blob sono necessarie le credenziali dell'account di archiviazione. Inoltre, per poter inviare la posta elettronica, è necessario che al ruolo di lavoro siano associate le credenziali da incorporare nelle chiamate al servizio SendGrid. Infine, allo scopo di creare un collegamento di annullamento della sottoscrizione da includere nei messaggi di posta elettronica inviati, è necessario che il ruolo di lavoro conosca l'URL dell'applicazione. Questi valori sono archiviati nelle impostazioni del progetto.

Per ottenere le credenziali dell'account di archiviazione la procedura è analoga a quella appresa nella [terza esercitazione](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage).

1.  In **Esplora soluzioni**, nella sezione **Ruoli** del progetto cloud, fare clic con il pulsante destro del mouse su **WorkerRoleB** e scegliere **Proprietà**.

2.  Fare clic sulla scheda **Impostazioni**.

3.  Assicurarsi che nell'elenco a discesa **Configurazione del servizio** sia selezionata l'opzione **Tutte le configurazioni**.

4.  Selezionare la scheda **Impostazioni** e quindi fare clic su **Aggiungi impostazione**.

5.  Immettere "StorageConnectionString" nella colonna **Nome**.

6.  Scegliere **Stringa di connessione** nell'elenco a discesa **Tipo**.

7.  Fare clic sui puntini di sospensione (**...**) all'estrema destra della riga per aprire la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

8.  Nella finestra di dialogo **Create Storage Connection String** fare clic sul pulsante di opzione **Your subscription**.

9.  Scegliere la stessa **sottoscrizione** e lo stesso **nome account** selezionati per il ruolo Web e il ruolo di lavoro A.

10. Eseguire la stessa procedura completata per configurare le impostazioni della stringa di connessione **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**.

    Quindi, creare e configurare le tre nuove impostazioni utilizzate solo dal ruolo di lavoro B.

11. Nella scheda **Settings** della finestra **Properties** fare clic su **Add Setting**, quindi aggiungere tre nuove impostazioni di tipo **String**:

    -   **Name**: SendGridUserName, **Value**: il nome utente di SendGrid stabilito nella [seconda esercitazione](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).

    -   **Name**: SendGridPassword, **Value**: la password di SendGrid.

    -   **Name**: AzureMailServiceURL, **Value**: l'URL di base dell'applicazione al momento della distribuzione; ad esempio: http://sampleurl.cloudapp.net.

    ![Nuove impostazioni nel progetto WorkerRoleB](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png)

### Aggiunta del codice da eseguire all'avvio del ruolo di lavoro

1.  Nel progetto WorkerRoleB eliminare WorkerRole.cs.

2.  Fare clic con il pulsante destro del mouse sul progetto WorkerRoleB e scegliere **Add Existing Item**.

    ![Aggiunta di un elemento esistente nel ruolo di lavoro B](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png)

3.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file WorkerRoleB.cs nel progetto WorkerRoleB e fare clic su **Aggiungi**.

	> [WACOM.NOTE] Per Visual Studio 2013 con l'ultimo SDK e l'ultimo pacchetto NuGet di SendGrid, aprire *WorkerRoleB.cs* e apportare le modifiche seguenti al codice: (1) Eliminare l'istruzione `using` per `SendGridMail.Transport`. (2) Modificare entrambe le istanze di `SendGrid.GenerateInstance` in `SendGrid.GetInstance`. (3) Modificare entrambe le istanze di `REST.GetInstance` in `Web.GetInstance`.

1.  Aprire WorkerRoleB.cs ed esaminare il codice.

    Come già osservato nel ruolo di lavoro A, il metodo `OnStart` inizializza le classi di contesto necessarie per utilizzare le entità di Archiviazione di Azure. Verifica inoltre l'esistenza di tutte le tabelle, le code e i contenitori BLOB necessari nel metodo `Run`.

    La differenza confrontata con il ruolo di lavoro A è l'aggiunta del contenitore blob e della coda di sottoscrizione tra le risorse da creare qualora non esistessero già. Il contenitore BLOB verrà utilizzato per ottenere i file che contengono il testo HTML e normale per il corpo del messaggio di posta elettronica. La coda di sottoscrizione viene utilizzata per inviare messaggi di posta elettronica di conferma della sottoscrizione.

         public override bool OnStart()
         {
             ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

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

    Lo scopo della sospensione è quello di ridurre al minimo i costi delle transazioni di archiviazione Azure, come illustrato nell'[esercitazione precedente](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/).

    Quando un elemento della coda viene estratto tramite il metodo [GetMessage](http://msdn.microsoft.com/it-it/library/windowsazure/ee741827.aspx) diverrà visibile per 30 secondi a tutti gli altri ruoli lavoro e Web che accedono alla coda. È in questo modo che è possibile assicurarsi che solo un'istanza del ruolo di lavoro selezioni eventuali messaggi in coda per elaborarli. È possibile impostare esplicitamente questo intervallo di *lease esclusivo* (la durata per cui l'elemento della coda è invisibile) passando un parametro di [scadenza della visibilità](http://msdn.microsoft.com/it-it/library/windowsazure/ee758454.aspx) al metodo `GetMessage`. Se il ruolo di lavoro impiega oltre 30 secondi per elaborare un messaggio della coda, sarà opportuno aumentare l'intervallo di lease esclusivo allo scopo di impedire l'elaborazione dello stesso messaggio da parte di altre istanze del ruolo.

    D'altro canto, non è consigliabile impostare l'intervallo di lease esclusivo su un valore eccessivamente lungo. Ad esempio, se si imposta questo intervallo su 48 ore e il ruolo di lavoro si arresta in maniera imprevista dopo aver rimosso un messaggio dalla coda, un altro ruolo di lavoro non sarà in grado di elaborare il messaggio per 48 ore. La durata massima dell'intervallo di lease esclusivo è di 7 giorni.

    Il metodo [GetMessages](http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx) (notare la "s" alla fine del nome) è utile per estrarre fino a 32 messaggi dalla coda con una sola chiamata. Ogni accesso alla coda implica un costo della transazione contenuto, che rimane invariato qualora vengano restituiti 32 messaggi o zero messaggi. Il codice seguente recupera fino a 32 messaggi con una sola chiamata e quindi li elabora.

    foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))

         {
             ProcessQueueMessage(msg);
             messageFound = true;
         }

    Quando si utilizza `GetMessages` per rimuovere più messaggi, assicurarsi che la scadenza della visibilità offra all'applicazione tempo sufficiente a elaborare tutti i messaggi. Alla scadenza del periodo di visibilità altre istanze del ruolo potranno accedere al messaggio e, in tal caso, la prima istanza non sarà in grado di eliminarlo al termine dell'elaborazione dell'elemento di lavoro.

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

    Next, we get the `SendEmail` row from the `message` table. This row has all of the information needed to send the email, except for the blobs that contain the HTML and plain text body of the email.

             var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
             var retrievedResult = messageTable.Execute(retrieveOperation);
             var emailRowInMessageTable = retrievedResult.Result as SendEmail;
             if (emailRowInMessageTable == null)
             {
                 Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                     partitionKey, rowKey, GetRoleInstance());
                 return;
             }

    Then the code sends the email and archives the `SendEmail` row.

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

    L'operazione effettiva di invio del messaggio di posta elettronica tramite SendGrid viene eseguita dal metodo `SendEmailToList`. Se si desidera utilizzare un servizio diverso da SendGrid, sarà sufficiente modificare il codice in questo metodo.

    **Nota:** Se nelle impostazioni del progetto sono presenti credenziali non valide, la chiamata a SendGrid non riuscirà, ma l'applicazione non riceverà alcuna indicazione dell'esito negativo. Se si utilizza SendGrid in un'applicazione di produzione, si consideri la configurazione di credenziali separate per l'API Web in modo da evitare la produzione di errori non segnalati quando un amministratore cambia la password del proprio account SendGrid. Per ulteriori informazioni, vedere l'articolo relativo alle [credenziali per più account di SendGrid MultiAuth](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials). È possibile configurare le credenziali all'indirizzo <https://sendgrid.com/credentials>.

         private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
             string htmlMessageBodyRef, string textMessageBodyRef)
         {
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(fromEmailAddress);
             email.AddTo(emailAddress);
             email.Html = GetBlobText(htmlMessageBodyRef);
             email.Text = GetBlobText(textMessageBodyRef);
             email.Subject = subjectLine;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                 RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
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

    Nel metodo `GetBlobText` il codice ottiene il valore delle dimensioni del blob e lo utilizza per inizializzare l'oggetto `MemoryStream` per motivi di prestazioni. Se le dimensioni non vengono fornite, l'oggetto `MemoryStream` allocherà 256 byte, per poi allocarne altri 512 quando il download supererà il primo valore impostato, e così via, raddoppiando la quantità allocata ogni volta. Per un blob di grandi dimensioni questo processo si rivelerebbe inefficiente se confrontato all'allocazione della quantità corretta all'inizio del download.

    Il metodo `Run` chiama `ProcessSubscribeQueueMessage` quando rileva un elemento di lavoro nella coda di sottoscrizione:

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
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(mailingList.FromEmailAddress);
             email.AddTo(subscriber.EmailAddress);
             string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                 "/subscribe
        id=" + subscriberGUID + "&listName=" + subscriber.ListName;
             email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                 "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                 "<a href= private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)? {?     var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(mailingList.FromEmailAddress);
             email.AddTo(subscriber.EmailAddress);
             string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                 "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
             email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +?         "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                 "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
             email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +?         "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                 "{1}", mailingList.Description, subscribeURL);
             email.Subject = "Subscribe to " + mailingList.Description;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);? }?quot;{1} private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)? {?     var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(mailingList.FromEmailAddress);
             email.AddTo(subscriber.EmailAddress);
             string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                 "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
             email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +?         "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                 "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
             email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +?         "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                 "{1}", mailingList.Description, subscribeURL);
             email.Subject = "Subscribe to " + mailingList.Description;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);? }?quot;>Confirm Subscription</a>", mailingList.Description, subscribeURL);
             email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                 "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                 "{1}", mailingList.Description, subscribeURL);
             email.Subject = "Subscribe to " + mailingList.Description;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);
         }

TestTest del ruolo di lavoro B
------------------------------

1.  Premere F5 per eseguire l'applicazione.

2.  Andare alla pagina **Messages** per visualizzare il messaggio creato per eseguire il test del ruolo di lavoro A. Dopo qualche minuto, aggiornare la pagina: la riga non sarà più visualizzata nell'elenco in quanto sarà stata archiviata.

3.  Controllare la cartella Posta in arrivo dove si prevede di ricevere il messaggio di posta elettronica. Si noti che potrebbero verificarsi dei ritardi nell'invio dei messaggi di posta elettronica da parte di SendGrid o nel recapito al proprio client di posta elettronica, pertanto potrebbe essere necessario attendere brevemente per visualizzare il messaggio. Potrebbe essere inoltre necessario controllare la cartella della posta indesiderata.

Passaggi successiviPassaggi successivi
--------------------------------------

È stata creata l'applicazione Azure Email Service da zero e il risultato ottenuto è identico al progetto completato che è stato scaricato. Per eseguire la distribuzione sul cloud, il test nel cloud e promuoverla alla produzione, è possibile utilizzare le stesse procedure illustrate nella [seconda esercitazione](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/). Se si sceglie di creare un'architettura alternativa, vedere [l'esercitazione introduttiva a Siti Web di Azure](/en-us/develop/net/tutorials/get-started) per informazioni su come distribuire il progetto MVC in un sito Web di Azure.

Per ulteriori informazioni sull'archiviazione Azure, vedere la risorsa seguente:

-   [Articolo sulle](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx)nozioni fondamentali dell'Archiviazione di Microsoft Azure sul blog di Bruno Terkaly

Per ulteriori informazioni sul servizio tabelle di Azure, vedere le risorse seguenti:

-   [Articolo sulle](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx)nozioni fondamentali sull'archiviazione tabelle di Azure nel blog di Bruno Terkaly
-   [Articolo su](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx)come ottenere il massimo dalle tabelle di Microsoft Azure nel blog del team di Servizio di archiviazione di Azure
-   [Articolo su come utilizzare il servizio di archiviazione tabelle in .NET](http://www.windowsazure.com/it-it/develop/net/how-to-guides/table-services/)
-   [Articolo di](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx)approfondimento sulle tabelle della libreria client di archiviazione 2.0 di Microsoft Azure sul blog del team di Servizio di archiviazione di Azure
-   [Articolo sulla progettazione di una strategia di partizionamento scalabile per l'archiviazione tabelle di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/hh508997.aspx)

Per ulteriori informazioni sul servizio di accodamento di Azure e sulle code del bus di servizio Azure, vedere le risorse seguenti:

-   [Articolo sullo schema di lavoro incentrato sulle code (creazione di applicazioni per cloud funzionanti con Microsoft Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
-   [Code di Azure e Azure Service Bus: confronto e contrapposizioni](http://msdn.microsoft.com/it-it/library/windowsazure/hh767287.aspx)
-   [Articolo su come utilizzare il servizio di archiviazione di accodamento in .NET](/en-us/develop/net/how-to-guides/queue-service/)

Per ulteriori informazioni sul servizio Blob di Azure, vedere le risorse seguenti:

-   [Articolo sull'archiviazione blob non strutturata (creazione di applicazioni per cloud funzionanti con Microsoft Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
-   [Come utilizzare il servizio di archiviazione BLOB di Azure in .NET](/en-us/develop/net/how-to-guides/blob-storage/)

Per ulteriori informazioni sul ridimensionamento automatico dei ruoli del servizio cloud di Azure, vedere le risorse seguenti:

-   [Come utilizzare il blocco applicazione per la scalabilità automatica](/en-us/develop/net/how-to-guides/autoscaling/)
-   [Scalabilità automatica e Azure](http://msdn.microsoft.com/it-it/library/hh680945(v=PandP.50).aspx)
-   [Articolo sulla](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04)creazione di soluzioni elastiche e scalabili automaticamente con Azure (video del Channel 9 di MSDN)

RingraziamentiRingraziamenti
----------------------------

Queste esercitazioni e l'applicazione di esempio sono state scritte da [Rick Anderson](http://blogs.msdn.com/b/rickandy/) e da Tom Dykstra. Cogliamo l'occasione per ringraziare le seguenti persone per la loro assistenza:

-   Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart))
-   [Cory Fowler](http://blog.syntaxc4.net/) (Twitter [@SyntaxC4](https://twitter.com/SyntaxC4) )
-   [Joe Giardino](http://blogs.msdn.com/b/windowsazurestorage/)
-   Don Glover
-   Jai Haridas
-   [Scott Hunter](http://blogs.msdn.com/b/scothu/) (Twitter: [@coolcsh](http://twitter.com/coolcsh))
-   [Brian Swan](http://blogs.msdn.com/b/brian_swan/)
-   [Daniel Wang](http://blogs.msdn.com/b/daniwang/)
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

