<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" urlDisplayName="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 4: Worker role A" metaKeywords="Azure tutorial, .NET multi-tier app, multi-tier architecture" description="The fourth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role A (email scheduler) for the Azure Email Service application - 4 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Creazione del ruolo di lavoro A (utilità di pianificazione della posta elettronica) per l'applicazione Azure Email Service - 4 di 5.
====================================================================================================================================

Questa è la quarta di una serie di cinque esercitazioni in cui viene illustrato come creare e distribuire l'applicazione di esempio Azure Email Service. Per informazioni sull'applicazione e sulla serie di esercitazioni, vedere la [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

In questa esercitazione si apprenderà come:

-   Eseguire query e aggiornare le tabelle di Archiviazione di Azure.
-   Aggiungere elementi di lavoro in una coda da assegnare a un altro ruolo di lavoro per l'elaborazione.
-   Gestire arresti pianificati tramite override del metodo `OnStop`.
-   Gestire arresti non pianificati verificando che non vengano persi messaggi di posta elettronica e che non vengano inviati duplicati.
-   Testare un ruolo di lavoro che utilizza le tabelle di Archiviazione di Azure utilizzando Esplora archivi Azure.

Il progetto di ruolo di lavoro A è già stato creato insieme al progetto di servizio cloud. A questo punto è necessario programmare il ruolo di lavoro e configurarlo per l'utilizzo dell'account di archiviazione di Azure.

Aggiunta di un riferimento al progettoAggiunta di un riferimento al progetto Web
--------------------------------------------------------------------------------

Poiché le classi di entità vengono definite nel progetto Web, è necessario aggiungere un apposito riferimento. Le stesse classi di entità verranno utilizzate nel ruolo di lavoro B per leggere e scrivere dati nelle tabelle di Azure utilizzate dall'applicazione.

**Nota:** in un'applicazione di produzione in genere non si imposta un riferimento a un progetto Web da un progetto di ruolo di lavoro, perché in caso contrario si farebbe riferimento a numerosi assembly dipendenti non necessari nel ruolo di lavoro. Solitamente si mantengono classi di modello condivise in un progetto di libreria di classi a cui fanno riferimento sia il progetto Web che il progetto di ruolo di lavoro. Per semplificare la struttura della soluzione, in questa esercitazione le classi di modello sono archiviate nel progetto Web.

1.  Fare clic con il pulsante destro del mouse sul progetto WorkerRoleA e scegliere **Aggiungi riferimento**.

    ![Aggiunta di un riferimento nel progetto WorkerRoleA](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-reference-menu.png)

2.  In **Gestione riferimenti** aggiungere un riferimento al progetto MvcWebRole oppure al progetto di applicazione Web, se l'interfaccia utente Web è in esecuzione in un sito Web di Azure, quindi fare clic su **OK**.

    ![Aggiunta di un riferimento a MvcWebRole](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png)

Aggiunta di un riferimento a SCL 1.7Aggiunta di un riferimento a un assembly SCL 1.7
------------------------------------------------------------------------------------

> [WACOM.NOTE] Se è installato l'SDK 2.3 o versione successiva, ignorare questo passaggio.

La versione 2.0 di Storage Client Library (SCL) non include tutti gli strumenti necessari per la diagnostica, quindi è necessario aggiungere un riferimento a un assembly della versione 1.7. Questo passaggio dovrebbe essere già stato completato nell'esercitazione precedente, ma le istruzioni vengono comunque ripetute nel caso non sia stato eseguito.

1.  Fare clic con il pulsante destro del mouse sul progetto WorkerRoleA e scegliere **Aggiungi riferimento**.

2.  Fare clic sul pulsante **Sfoglia** nella parte inferiore della finestra di dialogo.

3.  Passare alla cartella seguente:

         C:\Programmi\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

4.  Selezionare *Microsoft.WindowsAzure.StorageClient.dll*, quindi fare clic su **Aggiungi**.

5.  Nella finestra di dialogo **Gestione riferimenti** fare clic su **OK**.

Aggiunta del modello SendEmailAggiunta del modello SendEmail
------------------------------------------------------------

Il ruolo di lavoro A crea le righe `SendEmail` nella tabella `Message` e il ruolo di lavoro B le legge per ottenere le informazioni necessarie per l'invio di posta elettronica. Nell'immagine seguente è illustrato un subset di proprietà relative a dure righe `Message` e a tre righe `SendEmail` nella tabella `Message`.

    ![message table with sendmail][mtas-sendMailTbl]

Queste righe della tabella `Message` servono a diversi scopi:

-   Forniscono tutte le informazioni necessarie al ruolo di lavoro B per l'invio di un singolo messaggio di posta elettronica.
-   Controllano se un messaggio è stato inviato per evitare che vengano inviati duplicati in caso di riavvio di un ruolo di lavoro dopo un errore.
-   Consentono al ruolo di lavoro A di determinare se sono stati inviati tutti i messaggi di posta elettronica relativi a uno specifico messaggio in modo da contrassegnarlo come completato.``

Per la lettura e la scrittura delle righe `SendEmail`, è necessaria una classe di modello. Poiché deve essere accessibile sia al ruolo di lavoro A che al ruolo di lavoro B e considerando che tutte le altre classi di modello sono definite nel progetto Web, è consigliabile definire anche questa classe nel progetto Web.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Models nel progetto Web e scegliere **Aggiungi elemento esistente**.

    ![Aggiunta di un elemento esistente alla cartella Models nel progetto Web](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-add-existing-for-sendemail-model.png)

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *SendEmail.cs* nella cartella Models del progetto Web e fare clic su Aggiungi.

3.  Aprire il file *SendEmail.cs* ed esaminare il codice.

		public class SendEmail : TableEntity
	    {
	        public long MessageRef { get; set; }
	        public string EmailAddress { get; set; }
	        public DateTime? ScheduledDate { get; set; }
	        public String FromEmailAddress { get; set; }
	        public string SubjectLine { get; set; }
	        public bool? EmailSent { get; set; }
	        public string SubscriberGUID { get; set; }
	        public string ListName { get; set; }
	    }

    Questo codice è simile ad altre classi di modello, con la differenza che non sono inclusi attributi DataAnnotations poiché a questo modello, che non viene utilizzato in un controller MVC, non è associata alcuna interfaccia utente.

Aggiunta del codice del ruolo di lavoroAggiunta del codice da eseguire all'avvio del ruolo di lavoro
----------------------------------------------------------------------------------------------------

1.  Nel progetto WorkerRoleA aprire il file *WorkerRole.cs* ed esaminare il codice.

	    public class WorkerRole : RoleEntryPoint
	    {
	        public override void Run()
	        {
	            // This is a sample worker implementation. Replace with your logic.
	            Trace.WriteLine("WorkerRole1 entry point called", "Information");
	
	            while (true)
	            {
	                Thread.Sleep(10000);
	                Trace.WriteLine("Working", "Information");
	            }
	        }
	
	        public override bool OnStart()
	        {
	            // Set the maximum number of concurrent connections 
	            ServicePointManager.DefaultConnectionLimit = 12;
	
	            // For information on handling configuration changes
	            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
	
	            return base.OnStart();
	        }
	    }

    Questo è il codice del modello predefinito per il ruolo di lavoro. Sono disponibili un metodo `OnStart`,` in cui è possibile inserire il codice di inizializzazione che viene eseguito solo all'avvio di un'istanza del ruolo di lavoro, e un metodo `Run`, che viene chiamato dopo il completamento del metodo `OnStart. Questo codice verrà sostituito con il codice effettivo di inizializzazione ed esecuzione.

2.  Eliminare *WorkerRole.cs*, quindi fare clic con il pulsante destro del mouse sul progetto WorkerRoleA e scegliere **Aggiungi elemento esistente**.

    ![Aggiunta di un elemento esistente nel ruolo di lavoro A](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-existing.png)

3.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *WorkerRoleA.cs* nel progetto WorkerRoleA e fare clic su **Aggiungi**.

4.  Aprire il file *WorkerRoleA.cs* ed esaminare il codice.

    Il metodo `OnStart` inizializza gli oggetti di contesto necessari per utilizzare le entità di Archiviazione di Azure. Verifica inoltre l'esistenza di tutte le tabelle, le code e i contenitori BLOB che verranno utilizzati nel metodo `Run`. Il codice che esegue queste attività è simile a quello illustrato in precedenza nei costruttori dei controller MVC. In seguito verrà configurata la stringa di connessione utilizzata da questo metodo.

         public override bool OnStart()
         {
             ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

             ConfigureDiagnostics();
             Trace.TraceInformation("Initializing storage account in WorkerA");
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

             CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
             sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
             var tableClient = storageAccount.CreateCloudTableClient(); 
             mailingListTable = tableClient.GetTableReference("mailinglist"); 
             messageTable = tableClient.GetTableReference("message"); 
             messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

             // Create if not exists for queue, blob container, SentEmail table. 
             sendEmailQueue.CreateIfNotExists(); 
             messageTable.CreateIfNotExists(); 
             mailingListTable.CreateIfNotExists(); 
             messagearchiveTable.CreateIfNotExists(); 

             return base.OnStart();
         }

    Nella documentazione precedente sull'utilizzo di Archiviazione di Azure viene illustrato il codice di inizializzazione in un ciclo che verifica la presenza di errori di trasporto. Ciò non è più necessario, poiché l'API include ora un meccanismo incorporato di ripetizione tentativi che assorbe gli errori di rete temporanei fino a un massimo di tre tentativi.

    Il metodo `ConfigureDiagnostics` chiamato dal metodo `OnStart` configura la traccia, in modo che sia possibile visualizzare l'output dei metodi `Trace.Information` e `Trace.Error`. Questo metodo viene illustrato nella [seconda esercitazione](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).

    Il metodo `OnStop` imposta la variabile globale `onStopCalled` su True, quindi attende che il metodo `Run` imposti la variabile globale `returnedFromRunMethod` su True, a indicare che è possibile eseguire una chiusura normale.

         public override void OnStop()
         {
             onStopCalled = true;
             while (returnedFromRunMethod == false)
             {
                 System.Threading.Thread.Sleep(1000);
             }
         }

    Il metodo `OnStop` viene chiamato quando il ruolo di lavoro sta per arrestarsi per uno dei motivi seguenti:

    -   È necessario riavviare la macchina virtuale (l'istanza del ruolo Web o del ruolo di lavoro) oppure il computer che la ospita.
    -   Il servizio cloud è stato interrotto tramite il pulsante **Stop** nel portale di gestione di Azure.
    -   È stato distribuito un aggiornamento nel progetto di servizio cloud.

    Il metodo `Run` monitora la variabile `onStopCalled` e interrompe il pull di nuovi elementi di lavoro da elaborare quando la variabile viene impostata su `True`. Questo coordinamento tra i metodi `OnStop` e `Run` consente un arresto normale del processo di lavoro.

    In Azure vengono periodicamente installati aggiornamenti del sistema operativo per garantire la sicurezza, l'affidabilità e le prestazioni della piattaforma. Questi aggiornamenti richiedono in genere l'arresto e il riavvio del computer che ospita il servizio cloud. Per ulteriori informazioni, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

    Il metodo `Run` esegue due funzioni:

    -   Analizza la tabella `message` per verificare la presenza di messaggi pianificati per l'invio nella giornata corrente o in precedenza, per cui non sono stati ancora creati elementi di lavoro della coda.

    -   Analizza la tabella `message` per verificare la presenza di messaggi con uno stato indicante che sono stati creati tutti gli elementi di lavoro della coda ma non sono stati ancora inviati tutti i messaggi di posta elettronica. Se trova un messaggio di questo tipo, analizza le relative righe `SendEmail` per verificare se sono stati inviati tutti i messaggi di posta elettronica e, in caso affermativo, aggiorna lo stato impostandolo su `Completed` e archivia la riga `message`.

    Il metodo controlla inoltre la variabile globale `onStopCalled`. Se la variabile è `True`, il metodo interrompe il pull di nuovi elementi di lavoro da elaborare e viene restituito quando vengono completate tutte le attività già avviate.

         public override void Run()
         {
             Trace.TraceInformation("WorkerRoleA entering Run()");
             while (true)
             {
                 try
                 {
                     var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                     // If OnStop has been called, return to do a graceful shutdown.
                     if (onStopCalled == true)
                     {
                         Trace.TraceInformation("onStopCalled WorkerRoleB");
                         returnedFromRunMethod = true;
                         return;
                     }
                     // Retrieve all messages that are scheduled for tomorrow or earlier
                     // and are in Pending or Queuing status.
                     string typeAndDateFilter = TableQuery.CombineFilters(
                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                         TableOperators.And,
                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                     TableOperation replaceOperation;
                     // Process each message (queue emails to be sent).
                     foreach (Message messageToProcess in messagesToProcess)
                     {
                         string restartFlag = "0";
                         // If the message is already in Queuing status,
                         // set flag to indicate this is a restart.
                         if (messageToProcess.Status == "Queuing")
                         {
                             restartFlag = "1";
                         }

                         // If the message is in Pending status, change
                         // it to Queuing.
                         if (messageToProcess.Status == "Pending")
                         {
                             messageToProcess.Status = "Queuing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }

                         // If the message is in Queuing status, 
                         // process it and change it to Processing status;
                         // otherwise it's already in processing status, and 
                         // in that case check if processing is complete.
                         if (messageToProcess.Status == "Queuing")
                         {
                             ProcessMessage(messageToProcess, restartFlag);

                             messageToProcess.Status = "Processing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }
                         else
                         {
                             CheckAndArchiveIfComplete(messageToProcess);
                         }
                     }

                     // Sleep for one minute to minimize query costs. 
                     System.Threading.Thread.Sleep(1000 * 60);
                 }
                 catch (Exception ex)
                 {
                     string err = ex.Message;
                     if (ex.InnerException != null)
                     {
                         err += " Inner Exception: " + ex.InnerException.Message;
                     }
                     Trace.TraceError(err);
                     // Don't fill up Trace storage if we have a bug in queue process loop.
                     System.Threading.Thread.Sleep(1000 * 60);
                 }
             }
         }

    Si noti che tutte le operazioni vengono eseguite in un ciclo infinito in un blocco `while` e che è stato eseguito il wrapping di tutto il codice del blocco `while` in un blocco `try`-`catch` per evitare un'eccezione non gestita. Se si verifica un'eccezione non gestita, verrà generato l'evento [UnhandledException](http://msdn.microsoft.com/en-us/library/system.appdomain.unhandledexception.aspx), il processo di lavoro verrà terminato e il ruolo verrà portato offline. Il ruolo di lavoro verrà riavviato da Azure, ma saranno richiesti diversi minuti. Il blocco `try` chiama `TraceError` per registrare l'errore, quindi viene sospeso per 60 secondi in modo che, se l'errore è persistente, il relativo messaggio non verrà ripetuto un numero eccessivo di volte. In un'applicazione di produzione, è possibile che il blocco `try` includa un messaggio di posta elettronica indirizzato a un amministratore.

    Il metodo `Run` elabora una query per le righe `message` nella tabella `message` con la data pianificata entro il giorno successivo:

                     // Retrieve all messages that are scheduled for tomorrow or earlier
                     // and are in Pending or Queuing status.
                     string typeAndDateFilter = TableQuery.CombineFilters(
                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                         TableOperators.And,
                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

    **Nota:** uno dei vantaggi dello spostamento delle righe di messaggi nella tabella `messagearchive` dopo l'elaborazione è che la query deve specificare solo `PartitionKey` e `RowKey` come criteri di ricerca. Se le righe elaborate non venissero archiviate, la query dovrebbe anche specificare un campo non di chiave (`Status`) ed effettuare la ricerca in più righe. Le dimensioni della tabella sarebbero maggiori e la query richiederebbe più tempo e potrebbe iniziare a recuperare token di continuazione.

    Se lo stato di un messaggio è `Pending`, l'elaborazione non è ancora iniziata. Se lo stato è `Queuing`, l'elaborazione è iniziata in precedenza ma è stata interrotta prima della creazione di tutti i messaggi in coda. In questo caso è necessario effettuare un controllo aggiuntivo nel ruolo di lavoro B quando invia ogni messaggio di posta elettronica, per verificare che l'invio non sia già iniziato. Questo è lo scopo della variabile `restartFlag`.

                         string restartFlag = "0";
                         if (messageToProcess.Status == "Queuing")
                         {
                             restartFlag = "1";
                         }

    In seguito il codice imposta le righe `message` con lo stato `Pending` su `Queuing`. Quindi, per tali righe oltre ad altre già con lo stato `Queuing`, chiama il metodo `ProcessMessage` per creare gli elementi di lavoro della coda e inviare i messaggi di posta elettronica per il messaggio.

                         if (messageToProcess.Status == "Pending")
                         {
                             messageToProcess.Status = "Queuing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }

                         if (messageToProcess.Status == "Queuing")
                         {
                             ProcessMessage(messageToProcess, restartFlag);

                             messageToProcess.Status = "Processing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }
                         else
                         {
                             CheckAndArchiveIfComplete(messageToProcess);
                         }

    Dopo l'elaborazione di un messaggio con lo stato `Queuing`, il codice imposta lo stato della riga `Message` su `Processing`. Le righe della tabella `message` il cui stato non è `Pending` o `Queuing` sono già impostate sullo stato `Processing`. Per tali righe il codice chiama un metodo che controlla se sono stati inviati tutti i messaggi di posta elettronica per il messaggio. Se tutti i messaggi di posta elettronica sono stati inviati, la riga `message` viene archiviata.

    Dopo l'elaborazione di tutti i record recuperati dalla query, il codice viene sospeso per un minito.

                 // Sleep for one minute to minimize query costs.
                 System.Threading.Thread.Sleep(1000*60);

    Ogni query di Archiviazione di Azure comporta un addebito minimo, anche se non restituisce dati, quindi la ripetizione continua dell'analisi aumenterebbe inutilmente i costi di Azure. Alla data in cui è stata scritta questa esercitazione il costo ammonta a $ 0,10 per ogni milione di transazioni (una query viene calcolata come transazione), quindi il tempo di sospensione può essere impostato su molto meno di un minuto, in modo da ridurre al minimo il costo dell'analisi delle tabelle per rilevare i messaggi da inviare. Per ulteriori informazioni sui prezzi, vedere la [prima esercitazione](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

    **Nota sul threading e sull'utilizzo ottimale della CPU:** il metodo `Run` prevede due attività, ovvero l'inserimento dei messaggi di posta elettronica nella coda e la ricerca di messaggi completati, che vengono eseguite in sequenza in un singolo thread. Una piccola macchina virtuale (VM, Virtual Machine) è dotata di 1,75 GB di RAM e di una sola CPU, quindi l'esecuzione di queste attività in sequenza con un singolo thread non dovrebbe creare problemi. Si supponga però che per l'esecuzione efficiente dell'applicazione sia necessaria una quantità di memoria maggiore di quella fornita dalla piccola VM. Una VM media prevede 3,5 GB di RAM e 2 CPU, ma l'applicazione in questione utilizzerebbe solo una CPU perché è a singolo thread. Per sfruttare tutte le CPU, è necessario creare un thread di lavoro per ognuna di esse. Anche in questo caso, una singola CPU non viene utilizzata appieno da un unico thread. Quando un thread effettua chiamate di I/O o di rete, deve attendere che tali chiamate vengano completate e durante l'attesa non esegue operazioni utili. Se il metodo `Run` viene implementato con due thread, mentre uno dei due attende il completamento dell'operazione di I/O o di rete, l'altro può eseguire operazioni utili.

    Il metodo `ProcessMessage` recupera tutti gli indirizzi di posta elettronica per la lista di distribuzione di destinazione e crea per ognuno di essi un elemento di lavoro della coda. Durante la creazione degli elementi di lavoro, crea anche le righe `SendEmail` nella tabella `Message`. Queste righe forniscono al ruolo di lavoro B le informazioni necessarie per inviare i messaggi di posta elettronica e includono una proprietà `EmailSent` che controlla se ogni messaggio è stato o meno inviato.

         private void ProcessMessage(Message messageToProcess, string restartFlag)
         {
             // Get Mailing List info to get the "From" email address.
             var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                 return;
             }
             // Get email addresses for this Mailing List.
             string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

             foreach (Subscriber subscriber in subscribers)
             {
                 // Verify that the subscriber email address has been verified.
                 if (subscriber.Verified == false)
                 {
                     Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                     continue;
                 }

                 // Create a SendEmail entity for this email.              
                 var sendEmailRow = new SendEmail
                 {
                     PartitionKey = messageToProcess.PartitionKey,
                     RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                     EmailAddress = subscriber.EmailAddress,
                     EmailSent = false,
                     MessageRef = messageToProcess.MessageRef,
                     ScheduledDate = messageToProcess.ScheduledDate,
                     FromEmailAddress = mailingList.FromEmailAddress,
                     SubjectLine = messageToProcess.SubjectLine,
                     SubscriberGUID = subscriber.SubscriberGUID,
                     ListName = mailingList.ListName
                 };

                 // When we try to add the entity to the SendEmail table, 
                 // an exception might happen if this worker role went 
                 // down after processing some of the email addresses and then restarted.
                 // In that case the row might already be present, so we do an Upsert operation.
                 try
                 {
                     var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                     messageTable.Execute(upsertOperation);
                 }
                 catch (Exception ex)
                 {
                     string err = "Error creating SendEmail row:  " + ex.Message;
                     if (ex.InnerException != null)
                     {
                         err += " Inner Exception: " + ex.InnerException;
                     }
                     Trace.TraceError(err);
                 }

                 // Create the queue message.
                 string queueMessageString =
                     sendEmailRow.PartitionKey + "," +
                     sendEmailRow.RowKey + "," +
                     restartFlag;
                 var queueMessage = new CloudQueueMessage(queueMessageString);
                 sendEmailQueue.AddMessage(queueMessage);
             }

             Trace.TraceInformation("ProcessMessage end PK: "
                 + messageToProcess.PartitionKey);
         }

    Il codice recupera prima la riga della lista di distribuzione dalla tabella `mailinglist` per la lista di distribuzione di destinazione. Questa riga include l'indirizzo di posta elettronica del mittente, che deve essere fornito al ruolo di lavoro B per l'invio dei messaggi.

             // Get Mailing List info to get the "From" email address.
             var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                 return;
             }

    Quindi, esegue una query sulla tabella `mailinglist` per trovare tutte le righe del sottoscrittore per la lista di distribuzione di destinazione.

             // Get email addresses for this Mailing List.
             string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

    Nel ciclo che elabora i risultati della query, il codice inizialmente controlla se l'indirizzo di posta elettronica del sottoscrittore è stato verificato e, in caso negativo, non viene accodato nessun messaggio.

                 // Verify that the subscriber email address has been verified.
                 if (subscriber.Verified == false)
                 {
                     Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                     continue;
                 }

    In seguito, il codice crea una riga `SendEmail` nella tabella `message`. Questa riga contiene le informazioni che verranno utilizzati dal ruolo di lavoro B per l'invio di un messaggio di posta elettronica. La riga viene creata con la proprietà `EmailSent` impostata su `false`.

                 // Create a SendEmail entity for this email.              
                 var sendEmailRow = new SendEmail
                 {
                     PartitionKey = messageToProcess.PartitionKey,
                     RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                     EmailAddress = subscriber.EmailAddress,
                     EmailSent = false,
                     MessageRef = messageToProcess.MessageRef,
                     ScheduledDate = messageToProcess.ScheduledDate,
                     FromEmailAddress = mailingList.FromEmailAddress,
                     SubjectLine = messageToProcess.SubjectLine,
                     SubscriberGUID = subscriber.SubscriberGUID,
                     ListName = mailingList.ListName
                 };
                 try
                 {
                     var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                     messageTable.Execute(upsertOperation);
                 }
                 catch (Exception ex)
                 {
                     string err = "Error creating SendEmail row:  " + ex.Message;
                     if (ex.InnerException != null)
                     {
                         err += " Inner Exception: " + ex.InnerException;
                     }
                     Trace.TraceError(err);
                 }

    Il codice utilizza un'operazione "upsert" perché la riga potrebbe essere già esistente se il ruolo di lavoro A viene riavviato in seguito a un errore.

    L'ultima attività da eseguire per ogni indirizzo di posta elettronica consiste nel creare l'elemento di lavoro della coda che attiverà l'invio di un messaggio di posta elettronica da parte del ruolo di lavoro B. L'elemento di lavoro della coda contiene il valore della chiave di partizione e della chiave di riga della riga `SendEmail` appena creata, oltre al contrassegno di riavvio impostato in precedenza. La riga `SendEmail` contiene tutte le informazioni necessarie al ruolo di lavoro B per l'invio di un messaggio di posta elettronica.

                 // Create the queue message.
                 string queueMessageString =
                     sendEmailRow.PartitionKey + "," +
                     sendEmailRow.RowKey + "," +
                     restartFlag;
                 var queueMessage = new CloudQueueMessage(queueMessageString);
                 sendEmailQueue.AddMessage(queueMessage);

    Il metodo `CheckAndUpdateStatusIfComplete` controlla tutti i messaggi con lo stato Processing per verificare se sono stati inviati tutti i messaggi di posta elettronica. Se non rileva messaggi di posta elettronica non inviati, aggiorna lo stato della riga impostandolo su `Completed` e archivia la riga.

         private void CheckAndArchiveIfComplete(Message messageToCheck)
         {
             // Get the list of emails to be sent for this message: all SendEmail rows
             // for this message.  
             string pkrkFilter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));
             var query = new TableQuery<SendEmail>().Where(pkrkFilter);
             var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();

             if (emailToBeSent != null)
             {
                 return;
             }

             // All emails have been sent; copy the message row to the archive table.

             // Insert the message row in the messagearchive table
             var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
             messageToCheck.Status = "Complete";
             var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
             messagearchiveTable.Execute(insertOrReplaceOperation);

             // Delete the message row from the message table.
             var deleteOperation = TableOperation.Delete(messageToDelete);
             messageTable.Execute(deleteOperation);
         }

Configurazione dell'archiviazioneConfigurazione della stringa di connessione di archiviazione
---------------------------------------------------------------------------------------------

Se non è ancora stato fatto, configurare le credenziali dell'account di archiviazione per il ruolo di lavoro A.

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **WorkerRoleA** nella sezione **Ruoli** del progetto cloud **AzureEmailService**, quindi scegliere **Proprietà**.

2.  Assicurarsi che nell'elenco a discesa **Configurazione del servizio** sia selezionata l'opzione **Tutte le configurazioni**.

3.  Selezionare la scheda **Impostazioni** e quindi fare clic su **Aggiungi impostazione**.

4.  Immettere *StorageConnectionString* nella colonna **Nome**.

5.  Scegliere **Stringa di connessione** nell'elenco a discesa **Tipo**.

6.  Fare clic sui puntini di sospensione (**...**) all'estremità destra della riga per creare una nuova stringa di connessione.

7.  Nella finestra di dialogo**Stringa di connessione all'account di archiviazione** fare clic su **Your subscription**.

8.  Scegliere i valori corretti per **Subscription** e **Account name**, quindi fare clic su **OK**.

9.  Impostare la stringa di connessione di diagnostica. È possibile utilizzare lo stesso account di archiviazione per la stringa di connessione di diagnostica, ma è consigliabile utilizzarne un altro per le informazioni di traccia.

TestTest del ruolo di lavoro A
------------------------------

1.  Premere F5 per eseguire l'applicazione.

> [WACOM.NOTE] Con Visual Studio 2013 e la versione più recente dell'SDK, è possibile che venga visualizzato un messaggio di errore di tipo "riferimento ambiguo" per il riferimento a `LogLevel`. Fare clic con il pulsante destro del mouse su `LogLevel`, scegliere Resolve e quindi selezionare `Microsoft.WindowsAzure.Diagnostics.LogLevel`.

1.  Utilizzare le pagine Web dell'amministratore per creare una lista di distribuzione e i relativi sottoscrittori. Impostare la proprietà `Verified` su `True` per almeno un sottoscrittore e specificare un indirizzo di posta elettronica a cui è possibile ricevere posta.

    Non verranno inviati messaggi di posta elettronica finché non si implementa il ruolo di lavoro B, ma verranno utilizzati gli stessi dati di test per testare il ruolo di lavoro B.

2.  Creare un messaggio da inviare alla lista di distribuzione creata, quindi impostare la data pianificata sul giorno corrente o nel passato.

    ![Nuovo messaggio con lo stato Pending](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png)

3.  Dopo poco più di un minuto (a causa del tempo di sospensione di un minuto impostato nel metodo Run) aggiornare la pagina Web Messages. Lo stato visualizzato a questo punto è Processing. È possibile che venga visualizzato prima si Queuing, ma il passaggio da Queuing a Processing avviene in genere talmente rapidamente che lo stato Queuing non viene rilevato inosservato.

    ![Nuovo messaggio con lo stato Processing](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png)

4.  Aprire Esplora archivi Azure e selezionare l'account di archiviazione di test.

5.  In Esplora archivi Azure, sotto **Storage Type**, selezionare **Queues** e quindi **azuremailqueue**.

    Nella lista di distribuzione di destinazione verrà visualizzato un messaggio in coda per ogni sottoscrittore verificato.

    ![Messaggio in coda in Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png)

6.  Fare doppio clic su un messaggio in coda e quindi nella finestra di dialogo **Message Detail** selezionare la scheda **Message**.

    Verrà visualizzato il contenuto del messaggio in coda, ovvero la chiave di partizione (data 2012-12-14), la chiave di riga (valore MessageRef e indirizzo di posta elettronica) e il contrassegno di riavvio, separati da una virgola.

    ![Contenuto del messaggio in coda in Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png)

7.  Chiudere la finestra di dialogo **Message Detail**.

8.  In **Storage Type** selezionare **Tables** e quindi la tabella **Message**.

9.  Fare clic su **Query** per visualizzare tutte le righe della tabella.

    Verrà visualizzato il messaggio pianificato, con "Message" nella chiave di riga, seguito da una riga per ogni sottoscrittore verificato, con l'indirizzo di posta elettronica nella chiave di riga.

    ![Righe della tabella Message in Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png)

10. Fare doppio clic su una riga nella cui chiave è incluso un indirizzo di posta elettronica per visualizzare il contenuto della riga `SendEmail` creata dal ruolo di lavoro A.

    ![Riga SendEmail nella tabella Message](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png)

Passaggi successiviPassaggi successivi
--------------------------------------

In questa esercitazione è stato illustrato come creare il ruolo di lavoro A e verificare se crea i messaggi della coda e le righe della tabella necessari al ruolo di lavoro B per inviare i messaggi di posta elettronica. Nell'[esercitazione successiva](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/) verrà illustrato come creare e testare il ruolo di lavoro B.

Per collegamenti a risorse aggiuntive per l'utilizzo di tabelle, code e BLOB si Archiviazione di Azure, vedere la fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

[Esercitazione 5](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)

