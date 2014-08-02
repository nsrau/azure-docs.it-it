<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" urlDisplayName="Step 3: Web Role" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 3: Web role" metaKeywords="Azure tutorial, Email Service application, ASP.NET MVC web role, MVC controllers, Web API controller, Cloud Service project" description="The third tutorial in a series that teaches how to create and deploy the Email Service app in an Azure cloud service." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building the web role for the Azure Email Service application - 3 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Creazione del ruolo Web per l'applicazione Azure Email Service - 3 di 5
=======================================================================

Questa è la terza di una serie di cinque esercitazioni in cui viene illustrato come compilare e distribuire l'applicazione di esempio Azure Email Service. Per informazioni sull'applicazione e sulla serie di esercitazioni, vedere la [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

In questa esercitazione si apprenderà come:

-   Creare una soluzione contenente un progetto di servizio cloud con un ruolo Web e un ruolo di lavoro.
-   Utilizzare tabelle, BLOB e code di Azure in controller e viste MVC 4.
-   Gestire conflitti di concorrenza quando si utilizzano le tabelle di Azure.
-   Configurare un ruolo Web o un progetto Web per utilizzare l'account di archiviazione di Azure.

Creazione della soluzioneCreazione della soluzione di Visual Studio
-------------------------------------------------------------------

Per iniziare, creare una soluzione di Visual Studio con un progetto per il front-end Web e un progetto per uno dei ruoli di lavoro di Azure back-end. In seguito verrà aggiunto il secondo ruolo di lavoro.

Se si desidera eseguire l'interfaccia utente Web in un sito Web di Azure anziché in un servizio cloud di Azure, vedere la sezione [Architettura alternativa](#alternativearchitecture) più avanti in questa esercitazione.

### Creare un progetto di servizio cloud con un ruolo Web e un ruolo di lavoro

1.  Avviare Visual Studio con privilegi elevati.

    > [WACOM.NOTE] Per Visual Studio 2013, non è necessario utilizzare privilegi elevati, perché per i nuovi progetti viene utilizzato per impostazione predefinita l'emulatore di calcolo Express.

2.  Scegliere **Nuovo progetto** dal menu **File**.

    ![Menu Nuovo progetto](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png)

3.  Espandere **C\#** e selezionare **Cloud** in **Modelli installati**, quindi selezionare **Servizio cloud Azure**.

4.  Assegnare il nome **AzureEmailService** all'applicazione e fare clic su **OK**.

    ![Finestra di dialogo Nuovo progetto](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png)

5.  Nella finestra di dialogo **New Azure Cloud Service** selezionare **Ruolo Web ASP.NET** e fare clic sulla freccia che punta a destra.

    > [WACOM.NOTE] Il codice scaricato utilizzato per questa esercitazione è MVC 4, ma non è possibile creare un ruolo Web MVC 4 in Visual Studio 2013 utilizzando le istruzioni scritte per Visual Studio 2012. Per Visual Studio 2013, eseguire le operazioni seguenti: (1) Ignorare i passaggi per la creazione del ruolo Web ed eseguire il passaggio relativo al ruolo di lavoro. (2) Dopo la creazione del ruolo di lavoro, fare clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni**, quindi scegliere **Aggiungi** -- **Nuovo progetto**. Nel riquadro sinistro della finestra di dialogo **Aggiungi nuovo progetto** espandere **Web** e selezionare **Visual Studio 2012**. (3) Scegliere **Applicazione Web ASP.NET MVC 4**, assegnare il nome **MvcWebRole** al progetto e quindi fare clic su **OK**. (4) Nella finestra di dialogo **New ASP.NET Project** selezionare il modello **Applicazione Internet**. (5) Fare clic con il pulsante destro del mouse su **Ruoli** in **AzureEmailService** in **Esplora soluzioni**, quindi scegliere **Aggiungi** - **Progetto di ruolo Web nella soluzione**. (6) Nella casella **Associa a progetto di ruolo** selezionare il progetto **MvcWebRole** e quindi fare clic su **OK**.

    ![Finestra di dialogo New Azure Cloud Project](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png)

6.  Nella colonna a destra passare il puntatore su **MvcWebRole1** e quindi fare clic sull'icona della matita per cambiare il nome del ruolo Web.

7.  Immettere MvcWebRole come nuovo nome, quindi premere INVIO.

    ![Finestra di dialogo New Azure Cloud Project - ridenominazione del ruolo Web](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png)

8.  Attenersi alla stessa procedura per aggiungere un **Ruolo di lavoro**, assegnare il nome WorkerRoleA e quindi fare clic su **OK**.

    ![Finestra di dialogo New Azure Cloud Project - aggiunta di un ruolo di lavoro](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png)

9.  Nella finestra di dialogo **New ASP.NET Project** selezionare il modello **Applicazione Internet**.

10. Nell'elenco a discesa **Motore di visualizzazione** assicurarsi che sia selezionata l'opzione **Razor**, quindi fare clic su **OK**.

    ![Finestra di dialogo Nuovo progetto](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png)

### Impostare l'intestazione, il menu e il piè di pagina

In questa sezione viene illustrato come aggiornare le intestazioni, i piè di pagina e le voci di menu visibili in ogni pagina dell'interfaccia utente Web dell'amministratore. L'applicazione includerà tre set di pagine Web dell'amministratore, una per le liste di distribuzione, una per i sottoscrittori di tali liste e una per i messaggi.

1.  In **Esplora soluzioni** espandere la cartella Views\\Shared e aprire il file \_Layout.cshtml.

    ![File \_Layout.cshtml in Esplora soluzioni](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-opening-layout-cshtml.png)

2.  Nell'elemento **&lt;title\>** sostituire "My ASP.NET MVC Application" con "Azure Email Service".

3.  Nell'elemento **&lt;p\>** con la classe "site-title" sostituire "your logo here" con "Azure Email Service" e "Home" con "MailingList".

    ![title e header in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-title-and-logo-in-layout.png)

4.  Eliminare la sezione menu:

    ![menu in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-menu-in-layout.png)

5.  Inserire una nuova sezione menu al posto dell'altra:

         <ul id="menu">
             <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
             <li>@Html.ActionLink("Messages", "Index", "Message")</li>
             <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
         </ul>

6.  Nell'elemento **&lt;footer\>** sostituire "My ASP.NET MVC Application" con "Azure Email Service".

    ![footer in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-footer-in-layout.png)

### Eseguire l'applicazione in locale

1.  Premere CTRL+F5 per eseguire l'applicazione.

    La home page dell'applicazione verrà visualizzata nel browser predefinito.

    ![home page](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png)

    L'applicazione viene eseguita nell'emulatore di calcolo di Azure. Nell'area di notifica di Windows verrà visualizzata l'icona dell'emulatore di calcolo:

    ![Emulatore di calcolo nell'area di notifica](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png)

Configurazione della tracciaConfigurazione della traccia
--------------------------------------------------------

Per abilitare il salvataggio dei dati di traccia, aprire il file *WebRole.cs* e aggiungere il metodo `ConfigureDiagnostics` seguente. Aggiungere il codice che chiama questo nuovo metodo nel metodo `OnStart`.

> [WACOM.NOTE] Per Visual Studio 2013, anziché eseguire la procedura per cambiare manualmente il codice in *WebRole.cs*, fare clic con il pulsante destro del mouse sul progetto MvcWebRole, scegliere **Aggiungi elemento esistente**, quindi aggiungere il file *WebRole.cs* del progetto scaricato.

    private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
        config.Logs.BufferQuotaInMB = 500;
        config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
        config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

        DiagnosticMonitor.Start(
            "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
            config);
        }
        
        public override bool OnStart()
        {
            ConfigureDiagnostics();
        return base.OnStart();
        }

Il metodo `ConfigureDiagnostics` viene illustrato nella [seconda esercitazione](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).

RiavviiAggiunta di codice per la gestione efficace dei riavvii.
---------------------------------------------------------------

Le applicazioni del servizio cloud di Azure vengono riavviate circa due volte al mese per gli aggiornamenti del sistema operativo. Per ulteriori informazioni sugli aggiornamenti del sistema operativo, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Quando un'applicazione Web sta per essere arrestata, viene generato un evento `OnStop`. Il boilerplate del ruolo Web creato da Visual Studio non esegue l'override del metodo `OnStop`, quindi l'applicazione avrà solo pochi secondi per completare le richieste HTTP prima di essere arrestata. È possibile aggiungere codice per eseguire l'override del metodo `OnStop` e assicurarsi che gli arresti vengano eseguiti normalmente.

Per gestire gli arresti e i riavvii, aprire il file *WebRole.cs* e aggiungere l'override seguente del metodo `OnStop`.

      public override void OnStop()
      {
          Trace.TraceInformation("OnStop called from WebRole");
          var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
          while (rcCounter.NextValue() > 0)
          {
              Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
              System.Threading.Thread.Sleep(1000);
          }           
      }

Per questo codice è necessaria un'istruzione `using` aggiuntiva:

      using System.Diagnostics;

Sono disponibili fino a 5 minuti per l'uscita del metodo `OnStop` prima che l'applicazione venga arrestata. Sarebbe possibile aggiungere una chiamata di sospensione di 5 minuti al metodo `OnStop` per assegnare all'applicazione la quantità massima di tempo per elaborare le richieste correnti, ma se l'applicazione è correttamente dimensionata, dovrebbe essere in grado di elaborare le richieste rimanenti in molto meno di 5 minuti. È consigliabile interrompere l'applicazione il più rapidamente possibile, in modo che possa essere riavviata altrettanto rapidamente per continuare a elaborare le richieste.

Quando un ruolo viene portato offline da Azure, il servizio di bilanciamento del carico interrompe l'invio di richieste all'istanza del ruolo e in seguito viene chiamato il metodo `OnStop`. Se non si dispone di un'altra istanza del ruolo, non verrà elaborata alcuna richiesta finché non vengono completati l'arresto e il riavvio del ruolo, operazioni che in genere richiedono diversi minuti. Questo è uno dei motivi per cui il contratto di servizio di Azure richiede che vi siano almeno due istanze di ogni ruolo in modo da trarre vantaggio dal tempo di attività garantito.

Nel codice illustrato per il metodo `OnStop` viene creato un contatore delle prestazioni di ASP.NET per `Requests Current`. Il valore del contatore `Requests Current` contiene il numero corrente di richieste, incluse quelle in coda, in esecuzione o in attesa di essere scritte nel client. Il valore di `Requests Current` viene controllato ogni secondo e, quando diventa zero, viene restituito il metodo `OnStop`. Quando viene restituito `OnStop`, il ruolo viene arrestato.

I dati traccia non vengono salvati quando vengono chiamati dal metodo `OnStop` senza l'esecuzione di un [trasferimento su richiesta](http://msdn.microsoft.com/en-us/library/windowsazure/gg433075.aspx). È possibile visualizzare le informazioni di traccia di `OnStop` in tempo reale con l'utilità [dbgview](http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx) da una connessione Desktop remoto.

Aggiornamento della libreria client di archiviazioneAggiornamento del pacchetto NuGet della libreria client di archiviazione
----------------------------------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Questo passaggio potrebbe non essere necessario. Se il pacchetto NuGet di Archiviazione di Azure non viene visualizzato nell'elenco di aggiornamenti, significa che la versione installata è aggiornata.

Il framework di API utilizzato per le tabelle, le code e i BLOB di Archiviazione di Azure si chiama Storage Client Library (SCL). Questa API è inclusa in un pacchetto NuGet nel modello di progetto di servizio cloud. Tuttavia, alla data in cui è stata scritta questa esercitazione, i modelli di progetto includono la versione 1.7 di SCL, non la versione 2.0 corrente. Pertanto, prima di iniziare a scrivere codice, sarà necessario aggiornare il pacchetto NuGet.

1.  Nel menu **Strumenti** di Visual Studio passare il puntatore su **Gestione pacchetti libreria**, quindi fare clic su **Manage NuGet Packages for Solution**.

    ![Manage NuGet Packages for Solution nel menu](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png)

2.  Nel riquadro sinistro della finestra di dialogo **Manage NuGet Packages** selezionare **Updates**, quindi scorrere verso il basso fino al pacchetto **Archiviazione di Azure** e fare clic su **Aggiorna**.

    ![Pacchetto Archiviazione di Azure nella finestra di dialogo Manage NuGet Packages](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png)

3.  Nella finestra di dialogo **Seleziona progetti** assicurarsi che siano selezionati entrambi i progetti e quindi fare clic su **OK**.

    ![Selezione di entrambi i progetti nella finestra di dialogo Seleziona progetti](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png)

4.  Accettare le condizioni di licenza per completare l'installazione del pacchetto, quindi chiudere la finestra di dialogo **Manage NuGet Packages**.

5.  Nel progetto WorkerRoleA in *WorkerRole.cs*, se è presente l'istruzione `using` seguente, eliminarla perché non è più necessaria:

         using Microsoft.WindowsAzure.StorageClient;

La versione 1.7 di SCL include un provider LINQ che semplifica la scrittura di codice per le query su tabelle. Alla data in cui è stata scritta questa esercitazione, la versione 2.0 di Table Service Layer (TSL) non include ancora un provider LINQ. Se si desidera utilizzare LINQ, è comunque necessario disporre di accesso al provider LINQ SCL 1.7 nello spazio dei nomi [Microsoft.WindowsAzure.Storage.Table.DataServices](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.table.dataservices.aspx). La versione 2.0 di TSL è stata progettata per migliorare le prestazioni e il provider LINQ 1.7 non trae vantaggi da tutti questi miglioramenti. Nell'applicazione di esempio si utilizza TSL 2.0, quindi non si utilizza LINQ per le query. Per ulteriori informazioni su SCL e TSL 2.0, vedere le risorse elencate alla fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

> [WACOM.NOTE] In Storage Client Library 2.1 è stato aggiunto di nuovo il supporto per LINQ, ma in questa esercitazione non si utilizza LINQ per le query sulle tabelle di archiviazione. La versione corrente di SCL supporta inoltre la programmazione asincrona, ma in questa esercitazione non viene illustrato il codice asincrono. Per ulteriori informazioni sulla programmazione asincrona, con un esempio di codice che la utilizza con SCL di Azure, vedere il capitolo dell'e-book seguente e il progetto scaricabile associato: [Utilizzare il supporto asincrono di .NET 4.5 per evitare chiamate di blocco](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

Aggiunta di un riferimento a SCL 1.7Aggiunta di un riferimento a un assembly SCL 1.7
------------------------------------------------------------------------------------

> [WACOM.NOTE] Se si utilizza SCL 2.1 o versione successiva (Visual Studio 2013 con l'SDK più recente), ignorare questa sezione.

La versione 2.0 di Storage Client Library (SCL) non include tutti gli strumenti necessari per la diagnostica, quindi è necessario aggiungere un riferimento a un assembly della versione 1.7.

1.  Fare clic con il pulsante destro del mouse sul progetto MvcWebRole e scegliere **Aggiungi riferimento**.

2.  Fare clic sul pulsante **Sfoglia** nella parte inferiore della finestra di dialogo.

3.  Passare alla cartella seguente:

         C:\Programmi\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Selezionare *Microsoft.WindowsAzure.StorageClient.dll*, quindi fare clic su **Aggiungi**.

5.  Nella finestra di dialogo **Gestione riferimenti** fare clic su **OK**.

6.  Ripetere la procedura per il progetto WorkerRoleA.

Codice App\_StartAggiunta di codice per creare tabelle, code e contenitori BLOB nel metodo Application\_Start
-------------------------------------------------------------------------------------------------------------

Nell'applicazione Web si utilizzeranno la tabella `MailingList`, la tabella `Message`, la coda `azuremailsubscribequeue` e il contenitore BLOB `azuremailblobcontainer`. Sarebbe possibile creare questi elementi manualmente con uno strumento come Esplora archivi di Azure, ma in questo caso sarebbe necessario eseguire questa operazione manualmente ogni volta che l'applicazione viene avviata con un nuovo account di archiviazione. In questa sezione viene illustrato come aggiungere codice che viene eseguito all'avvio dell'applicazione, controlla se esistono le tabelle, le code e i contenitori BLOB necessari e in caso contrario li crea.

È possibile aggiungere questo codice di avvio eseguibile una sola volta al metodo `OnStart` nel file *WebRole.cs* o nel file *Global.asax*. Per questa esercitazione, l'inizializzazione di Archiviazione di Azure verrà eseguita nel file *Global.asax*, poiché è compatibile con Siti Web di Azure oltre che con i ruoli Web del servizio cloud di Azure.

1.  In **Esplora soluzioni** espandere *Global.asax* e quindi aprire *Global.asax.cs*.

2.  Aggiungere un nuovo metodo `CreateTablesQueuesBlobContainers` dopo il metodo `Application_Start` e quindi chiamare il nuovo metodo dal metodo `Application_Start`, come illustrato nell'esempio seguente:

         protected void Application_Start()
         {
             AreaRegistration.RegisterAllAreas();
             WebApiConfig.Register(GlobalConfiguration.Configuration);
             FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
             RouteConfig.RegisterRoutes(RouteTable.Routes);
             BundleConfig.RegisterBundles(BundleTable.Bundles);
             AuthConfig.RegisterAuth();
             // Verify that all of the tables, queues, and blob containers used in this application
             // exist, and create any that don't already exist.
             CreateTablesQueuesBlobContainers();
         }

         private static void CreateTablesQueuesBlobContainers()
         {
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
             // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
             //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
             var tableClient = storageAccount.CreateCloudTableClient();
             var mailingListTable = tableClient.GetTableReference("MailingList");
             mailingListTable.CreateIfNotExists();
             var messageTable = tableClient.GetTableReference("Message");
             messageTable.CreateIfNotExists();
             var blobClient = storageAccount.CreateCloudBlobClient();
             var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
             blobContainer.CreateIfNotExists();
             var queueClient = storageAccount.CreateCloudQueueClient();
             var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
             subscribeQueue.CreateIfNotExists();
         }

3.  Fare clic con il pulsante destro del mouse sulla linea ondulata blu sotto `RoleEnvironment`, scegliere **Risolvi**, quindi selezionare **using Microsoft.WindowsAzure.ServiceRuntime**.

    ![rightClick](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-4.png)

4.  Fare clic con il pulsante destro del mouse sulla linea ondulata blu sotto `CloudStorageAccount`, scegliere **Risolvi**, quindi selezionare **using Microsoft.WindowsAzure.Storage**.

5.  In alternativa, è possibile aggiungere manualmente le istruzioni using seguenti:

         using Microsoft.WindowsAzure.ServiceRuntime;
         using Microsoft.WindowsAzure.Storage;

6.  Compilare l'applicazione. Il file verrà salvato e verrà effettuato un controllo per verificare che non siano stati inseriti errori di compilazione.

Nelle sezioni seguenti verranno compilati i componenti dell'applicazione Web e sarà possibile testarli con i servizi di archiviazione per lo sviluppo oppure con l'account di archiviazione senza la necessità di creare prima manualmente tabelle, code o contenitori BLOB.

Mailing ListCreazione e test del controller e delle viste Mailing List
----------------------------------------------------------------------

L'interfaccia utente Web **Mailing List** viene utilizzata dagli amministratori per creare, modificare e visualizzare liste di distribuzione, ad esempio "Annunci del Dipartimento di storia della Contoso University " e "Annunci di lavoro di Fabrikam Engineering".

### Aggiungere la classe di entità MailingList alla cartella Models

La classe di entità `MailingList` viene utilizzata per le righe della tabella `MailingList` che contengono informazioni sulla lista, ad esempio la descrizione e l'indirizzo di posta elettronica del mittente per i messaggi inviati alla lista.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella `Models` nel progetto MVC e scegliere **Aggiungi elemento esistente**.

    ![Aggiunta di un elemento esistente nella cartella Models](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-models.png)

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *MailingList.cs* nella cartella `Models` e fare clic su **Aggiungi**.

3.  Aprire il file *MailingList.cs* ed esaminare il codice.

         public class MailingList : TableEntity
         {
             public MailingList()
             {
                 this.RowKey = "mailinglist";
             }

             [Required]
             [RegularExpression(@"[\w]+",
              ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
             [Display(Name = "List Name")]
             public string ListName
             {
                 get
                 {
                     return this.PartitionKey;
                 }
                 set
                 {
                     this.PartitionKey = value;
                 }
             }

             [Required]
             [Display(Name = "'From' Email Address")]
             public string FromEmailAddress { get; set; }

             public string Description { get; set; }
         }

    L'API TSL 2.0 di Archiviazione di Azure richiede che le classi di entità utilizzate per le operazioni su tabelle derivino da [TableEntity](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx). Questa classe definisce i campi `PartitionKey`, `RowKey`, `TimeStamp` e `ETag`. Le proprietà `TimeStamp` e `ETag` vengono utilizzate dal sistema. Più avanti in questa esercitazione verrà illustrato come utilizzare la proprietà `ETag` per la gestione della concorrenza.

    È disponibile anche una classe [DynamicTableEntity](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) da utilizzare se si desidera gestire le righe di tabella come raccolte Dictionary di coppie chiave-valore anziché utilizzare le classi di modello predefinite. Per ulteriori informazioni, vedere il blog di [approfondimento sulle tabelle della libreria client 2.0 di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx).

    La chiave di partizione della tabella `mailinglist` corrisponde al nome della lista. In questa classe di entità il valore della chiave di partizione è accessibile tramite la proprietà `PartitionKey`, definita nella classe `TableEntity`, o tramite la proprietà `ListName`, definita nella classe `MailingList`. La proprietà `ListName` utilizza `PartitionKey` come variabile sottostante. Definendo la proprietà `ListName`, è possibile utilizzare un nome di variabile più descrittivo nel codice e risulta più facile programmare l'interfaccia utente Web, poiché gli attributi di convalida e DataAnnotation possono essere aggiunti alla proprietà `ListName`, ma anche direttamente alla proprietà `PartitionKey`.

    Tramite l'attributo `RegularExpression` della proprietà `ListName`, MVC convalida l'input utente per verificare che il valore del nome della lista contenga solo caratteri alfanumerici o di sottolineatura. Questa restrizione è stata implementata per semplificare i nomi delle liste in modo che possano essere facilmente utilizzati nelle stringhe delle query negli URL.

    **Nota:** se si desidera un formato meno restrittivo per il nome della lista, è possibile consentire l'utilizzo di altri caratteri e applicare la codifica URL ai nomi utilizzati nelle stringhe delle query. Tuttavia, alcuni caratteri non sono consentiti nelle chiavi di partizione o nelle chiavi di riga delle tabelle di Azure e sarà necessario escludere almeno questi caratteri. Per informazioni sui caratteri non consentiti o che causano problemi nei campi delle chiavi di partizione o delle chiavi di riga, vedere [Informazioni sul modello di dati del servizio tabelle](http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx) e [Carattere % in PartitionKey o RowKey](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx).

    La classe `MailingList` definisce un costruttore predefinito che imposta `RowKey` sulla stringa "mailinglist" hardcoded, in quanto tutte le righe della lista di distribuzione presenti in questa tabella hanno questo valore come chiave di riga. Per una descrizione della struttura della tabella, vedere la [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/). Per questo scopo è possibile scegliere qualsiasi valore di costante, purché non sia mai identico a un indirizzo di posta elettronica, che corrisponde alla chiave di riga per le righe del sottoscrittore nella tabella.

    È necessario immettere sempre il nome della lista e l'indirizzo di posta elettronica del mittente quando viene creata una nuova entità `MailingList`, in modo che abbiano gli attributi `Required`.

    Gli attributi `Display` definiscono la didascalia da utilizzare per un campo nell'interfaccia utente MVC.

### Aggiungere il controller MVC MailingList

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Controllers nel progetto MVC e scegliere **Aggiungi elemento esistente**.

    ![Aggiunta di un elemento esistente nella cartella Controllers](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-controllers.png)

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *MailingListController.cs* nella cartella `Controllers` e fare clic su **Aggiungi**.

3.  Aprire il file *MailingListController.cs* ed esaminare il codice.

    Il costruttore predefinito crea un oggetto `CloudTable` da utilizzare con la tabella `mailinglist`.

         public class MailingListController : Controller
         {
             private CloudTable mailingListTable;

             public MailingListController()
             {
                 var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
                 // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
                 //    var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

                 var tableClient = storageAccount.CreateCloudTableClient();
                 mailingListTable = tableClient.GetTableReference("mailinglist");
             }

    Il codice ottiene le credenziali dell'account di archiviazione di Azure dal file di impostazioni del progetto di servizio cloud per stabilire una connessione con l'account di archiviazione. Queste impostazioni verranno configurate più avanti nell'esercitazione, prima di testare il controller. Se si prevede di eseguire il progetto MVC in un sito Web di Azure, è possibile ottenere la stringa di connessione dal file Web.config.

    Il metodo successivo, `FindRow`, viene chiamato ogni volta che il controller deve cercare una voce specifica della lista di distribuzione nella tabella `MailingList`, ad esempio per modificarla. Il codice recupera una singola entità `MailingList` utilizzando i valori passati relativi alla chiave di partizione e alla chiave di riga. Le righe modificate dal controller sono quelle identificate dalla chiave "MailingList", quindi è possibile che "MailingList" sia hardcoded per la chiave di riga, ma il modello utilizzato per i metodi `FindRow` in tutti i controller prevede di specificare sia una chiave di partizione che una chiave di riga.

         private MailingList FindRow(string partitionKey, string rowKey)
         {
             var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 throw new Exception("No mailing list found for: " + partitionKey);
             }

             return mailingList;
         }

    Risulta utile confrontare il metodo `FindRow` nel controller `MailingList`, che restituisce una riga della lista di distribuzione, con il metodo `FindRow` nel controller `Subscriber`, che restituisce una riga del sottoscrittore della stessa tabella `mailinglist`.

         private Subscriber FindRow(string partitionKey, string rowKey)
         {
             var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var subscriber = retrievedResult.Result as Subscriber;
             if (subscriber == null)
             {
                 throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
             }
             return subscriber;
         }

    L'unica differenza nelle due query è il tipo di modello che passano al metodo [TableOperation.Retrieve](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx). Il tipo di modello specifica lo schema, ovvero le proprietà, della riga o delle righe che si prevede vengano restituite dalla query. Una singola tabella può avere schemi diversi in righe diverse. In genere, si specifica lo stesso tipo di modello quando si legge una riga utilizzata per creare la riga.

    Nella pagina **Index** vengono visualizzate tutte le righe della lista di distribuzione, quindi la query nel metodo `Index` restituisce tutte le entità `MailingList` identificate dalla chiave di riga "mailinglist". Le altre righe della tabella hanno l'indirizzo di posta elettronica come chiave di riga e contengono informazioni sul sottoscrittore.

                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                 lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();

    La query del metodo `Index` è circondata da codice progettato per gestire le condizioni di timeout.

         public ActionResult Index()
         {
             TableRequestOptions reqOptions = new TableRequestOptions()
             {
                 MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                 RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
             };
             List<MailingList> lists;
             try
             {
                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                 lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();
             }
             catch (StorageException se)
             {
                 ViewBag.errorMessage = "Timeout error, try again. ";
                 Trace.TraceError(se.Message);
                 return View("Error");
             }

             return View(lists);
         }

    Se non si specificano i parametri di timeout, l'API esegue automaticamente tre tentativi con limiti di timeout incrementati in modo esponenziale. Per un'interfaccia Web in cui un utente attende la visualizzazione della pagina, si tratta di tempi di attesa inaccettabilmente lunghi. Pertanto, questo codice specifica una ripetizione lineare dei tentativi, in modo che il limite di timeout non venga incrementato ogni volta, e un limite di timeout ragionevole per l'attesa dell'utente.

    Quando l'utente fa clic sul pulsante **Create** della pagina **Create**, lo strumento di associazione di modelli MVC crea un'entità `MailingList` dall'input immesso nella visualizzazione e il metodo `HttpPost Create` la aggiunge alla tabella.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(MailingList mailingList)
         {
             if (ModelState.IsValid)
             {
                 var insertOperation = TableOperation.Insert(mailingList);
                 mailingListTable.Execute(insertOperation);
                 return RedirectToAction("Index");
             }

             return View(mailingList);
         }

    Per la pagina **Edit**, il metodo `HttpGet Edit` cerca la riga e il metodo `HttpPost` la aggiorna.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, MailingList editedMailingList)
        {
            if (ModelState.IsValid)
            {
                var mailingList = new MailingList();
                UpdateModel(mailingList);
                try
                {
                    var replaceOperation = TableOperation.Replace(mailingList);
                    mailingListTable.Execute(replaceOperation);
                    return RedirectToAction("Index");
                }
                catch (StorageException ex)
                {
                    if (ex.RequestInformation.HttpStatusCode == 412)
                    {
                        // Concurrency error
                        var currentMailingList = FindRow(partitionKey, rowKey);
                        if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                        {
                            ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                        }
                        if (currentMailingList.Description != editedMailingList.Description)
                        {
                            ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                        }
                        ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                            + "was modified by another user after you got the original value. The "
                            + "edit operation was canceled and the current values in the database "
                            + "have been displayed. If you still want to edit this record, click "
                            + "the Save button again. Otherwise click the Back to List hyperlink.");
                         ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
                    }
                    else
                    {
                        throw; 
                    }
                }
            }
            return View(editedMailingList);
        }

    Il blocco try-catch gestisce gli errori di concorrenza. Se un utente seleziona una lista di distribuzione per la modifica e quindi, mentre è visualizzata la pagina **Edit** nel browser un altro utente modifica la stessa lista di distribuzione, verrà generata un'eccezione di concorrenza. In questo caso, il codice visualizza un messaggio di avviso e indica i cambi che sono stati modificati dall'altro utente.  L'API TSL utilizza 'ETag' per verificare la presenza di conflitti di concorrenza. Ogni volta che una riga di tabella viene aggiornata, il valore di 'ETag' viene modificato.  Quando si recupera una riga da modificare, il valore di 'ETag' viene salvato e quindi viene passato quando si esegue un'operazione di aggiornamento o eliminazione. La visualizzazione 'Edit' include un campo nascosto per il valore di ETag. Se durante un aggiornamento viene rilevato che il valore di 'ETag' sul record da aggiornare è diverso da quello passato all'operazione di aggiornamento, verrà generata un'eccezione di concorrenza. Se si ritiene che i conflitti di concorrenza non siano importanti, è possibile impostare il campo ETag su un asterisco ("*") nell'entità passata all'operazione di aggiornamento. In questo caso i conflitti verranno ignorati. 

    Nota: l'errore HTTP 412 non è specifico per gli errori di concorrenza. Può essere generato per altri errori dall'API SCL.

    Per la pagina **Delete**, il metodo 'HttpGet Delete' cerca nella riga per visualizzarne il contenuto e il metodo 'HttpPost' elimina la riga 'MailingList' insieme a eventuali altre righe 'Subscriber' associate nella tabella 'MailingList'.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            var listRows = mailingListTable.ExecuteQuery(query).ToList();
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    mailingListTable.ExecuteBatch(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                mailingListTable.ExecuteBatch(batchOperation);
            }
            return RedirectToAction("Index");
        }

    Nel caso in cui sia necessario eliminare un numero elevato di sottoscrittori, il codice elimina i record in batch. Il costo della transazione per l'eliminazione di una riga è identico a quello dell'eliminazione di 100 righe in un batch. Il numero massimo di operazioni che è possibile eseguire in un batch è pari a 100. 

    Anche se il ciclo elabora sia le righe 'MailingList' che le righe 'Subscriber', le legge tutte nella classe di entità 'MailingList', poiché gli unici campi necessari per l'operazione 'Delete' sono 'PartitionKey', 'RowKey' e 'ETag'.

### Aggiungere le viste MVC MailingList

1.  In **Esplora soluzioni** creare una nuova cartella nella cartella *Views* del progetto MVC e denominarla *MailingList*.

2.  Fare clic con il pulsante destro del mouse sulla nuova cartella *Views\\MailingList* e scegliere **Aggiungi elemento esistente**.

    ![Aggiunta di un elemento esistente nella cartella Views](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-views.png)

3.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare i file con estensione cshtml nella cartella *Views\\MailingList* e fare clic su **Aggiungi**.

4.  Aprire il file *Edit.cshtml* ed esaminare il codice.

         @model MvcWebRole.Models.MailingList
                 @{
             ViewBag.Title = "Edit Mailing List";
         }
                 <h2>Edit Mailing List</h2>
               @using (Html.BeginForm()) {
               @Html.AntiForgeryToken()
               @Html.ValidationSummary(true)
               @Html.HiddenFor(model => model.ETag)
               <fieldset>
                 <legend>MailingList</legend>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ListName)
                 </div>
                 <div class="editor-field">
                     @Html.DisplayFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.Description)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.Description)
                     @Html.ValidationMessageFor(model => model.Description)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.FromEmailAddress)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.FromEmailAddress)
                     @Html.ValidationMessageFor(model => model.FromEmailAddress)
                 </div>
                 <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>       
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Questo codice è tipico per le viste MVC. Si noti il campo nascosto incluso per preservare il valore di `ETag`, utilizzato per gestire i conflitti di concorrenza. Si noti inoltre che il campo `ListName` include un helper `DisplayFor` anziché `EditorFor`. Per la pagina **Edit** la modifica del nome della lista non è stata abilitata, in quanto sarebbe stato necessario codice complesso nel controller: il metodo `HttpPost Edit` deve infatti eliminare la riga della lista di distribuzione e tutte le righe del sottoscrittore associate, quindi reinserirle con il nuovo valore di chiave. In un'applicazione di produzione si potrebbe decidere che valga la pena aggiungere questa complessità. Come verrà illustrato in seguito, il controller `Subscriber` non consente modifiche al nome della lista, poiché è interessata solo una riga alla volta.

    Il codice di *Create.cshtml* e di *Delete.cshtml* è simile a quello di *Edit.cshtml*.

5.  Aprire il file *Index.cshtml* ed esaminare il codice.

         @model IEnumerable<MvcWebRole.Models.MailingList>
         @{
             ViewBag.Title = "Mailing Lists";
         }
         <h2>Mailing Lists</h2>
         <p>
             @Html.ActionLink("Create New", "Create")
         </p>
         <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.ListName)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.Description)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.FromEmailAddress)
                 </th>
                 <th></th>
             </tr>
         @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.ListName)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Description)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.FromEmailAddress)
                 </td>
                 <td>
                     @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
                     @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
                 </td>
             </tr>
         }
         </table>

    Anche questo codice è tipico per le viste MVC. I collegamenti ipertestuali **Edit** e **Delete** specificano i parametri delle stringhe delle query sulle chiavi di partizione e le chiavi di riga per identificare una riga specifica. Per le entità `MailingList`, è in realtà necessaria solo la chiave di partizione, in quanto la chiave di riga è sempre "MailingList", ma vengono mantenute entrambe in modo che il codice delle viste MVC sia coerente in tutti i controller e le viste.

### Impostare MailingList come controller predefinito

1.  Aprire il file *Route.config.cs* nella cartella *App\_Start*.

2.  Nella riga che specifica i valori predefiniti, sostituire il controller predefinito "Home" con "MailingList".

          routes.MapRoute(
              name: "Default",
              url: "{controller}/{action}/{id}",
              defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

Configurazione dell'archiviazioneConfigurazione del ruolo Web per l'utilizzo dell'account di archiviazione di Azure
-------------------------------------------------------------------------------------------------------------------

Verranno immesse le impostazioni per l'account di archiviazione di test, che verrà utilizzato durante l'esecuzione del progetto in locale. Le nuove impostazioni dovranno essere aggiunte sia per il cloud che in locale, ma il valore per il cloud potrà essere modificato in seguito. Le stesse impostazioni verranno in seguito aggiunte per il ruolo di lavoro A.

Se si desidera eseguire l'interfaccia utente Web in un sito Web di Azure anziché in un servizio cloud di Azure, vedere la sezione [Architettura alternativa](#alternativearchitecture) più avanti in questa esercitazione.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **MvcWebRole** nella sezione **Ruoli** del progetto cloud **AzureEmailService**, quindi scegliere **Proprietà**.

    ![Proprietà del ruolo Web](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png)

2.  Assicurarsi che nell'elenco a discesa **Configurazione del servizio** sia selezionata l'opzione **Tutte le configurazioni**.

3.  Selezionare la scheda **Impostazioni** e quindi fare clic su **Aggiungi impostazione**.

4.  Immettere "StorageConnectionString" nella colonna **Nome**.

5.  Scegliere **Stringa di connessione** nell'elenco a discesa **Tipo**.

6.  Fare clic sui puntini di sospensione (**...**) all'estrema destra della riga per aprire la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

    ![Pulsante destro del mouse su Proprietà](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png)

7.  Nella finestra di dialogo **Create Storage Connection String** fare clic sul pulsante di opzione **Your subscription** e quindi sul collegamento **Download Publish Settings**.

    > [WACOM.NOTE] Con la versione più recente dell'SDK, non è necessario scaricare niente. Scegliere un account di archiviazione tra quelli disponibili in un elenco a discesa.

    **Nota:** se le impostazioni di archiviazione sono già state configurate per la seconda esercitazione e la presente esercitazione viene eseguita nello stesso computer, non è necessario scaricarle di nuovo. È sufficiente fare clic su **Your subscription** e quindi scegliere i valori corretti per **Subscription** e **Account Name**.

    ![Pulsante destro del mouse su Proprietà](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-enter.png)

    Quando si fa clic sul collegamento **Download Publish Settings**, in Visual Studio viene avviata una nuova istanza del nuovo browser predefinito, con l'URL della pagina di download delle impostazioni di pubblicazione nel portale di gestione di Azure. Se non si è connessi al portale, viene richiesto di effettuare l'accesso. Dopo l'accesso, nel browser viene richiesto di salvare le impostazioni di pubblicazione. Prendere nota del percorso in cui si salvano le impostazioni.

    ![Impostazioni di pubblicazione](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-3.png)

8.  Nella finestra di dialogo **Create Storage Connection String** fare clic su **Import** e quindi passare al file di impostazioni di pubblicazione salvato nel passaggio precedente.

9.  Selezionare la sottoscrizione e l'account di archiviazione che si desidera utilizzare, quindi fare clic su **OK**.

    ![Selezione dell'account di archiviazione](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-5.png)

10. Impostare la stringa di connessione `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` eseguendo la stessa procedura completata per la stringa di connessione `StorageConnectionString`.

    Non è necessario scaricare di nuovo il file di impostazioni di pubblicazione. Quando si fa clic sui puntini di sospensione accanto alla stringa di connessione `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`, si noterà che le informazioni sulla sottoscrizione sono memorizzate nella finestra di dialogo **Create Storage Connection String**. Dopo aver fatto clic sul pulsante di opzione **Your subscription**, è sufficiente selezionare gli stessi valori di **Subscription** e **Account Name** selezionati in precedenza e quindi fare clic su **OK**.

11. Impostare le stringhe di connessione per il ruolo WorkerRoleA eseguendo la stessa procedura completata per le due stringhe di connessione del ruolo MvcWebRole.

La nuova impostazione aggiunta con il pulsante **Aggiungi impostazioni** è stata aggiunta al codice XML nel file *ServiceDefinition.csdf* e in ognuno dei due file di configurazione *.cscfg*. In Visual Studio viene aggiunto il codice XML seguente nel file *ServiceDefinition.csdf*.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

In ogni file di configurazione *.cscfg* viene aggiunto il codice XML seguente.

       <Setting name="StorageConnectionString"
       value="DefaultEndpointsProtocol=https;
       AccountName=azuremailstorage;
       AccountKey=[your account key]" />

È possibile aggiungere manualmente impostazioni nel file *ServiceDefinition.csdf* e nei due file di configurazione *.cscfg*, ma l'utilizzo dell'editor di proprietà offre i vantaggi seguenti per le stringhe di connessione:

-   La nuova impostazione viene aggiunta in un'unica posizione e in tutti e tre i file viene aggiunto il codice XML di impostazione corretto.
-   Per tutti e tre i file di impostazioni viene generato il codice XML corretto. Il file *ServiceDefinition.csdf* definisce le impostazioni che devono essere presenti in ogni file di configurazione *.cscfg*. Se le impostazioni presenti nel file *ServiceDefinition.csdf* non sono coerenti con quelle aggiunte nei file di configurazione *.cscfg*, è possibile che venga visualizzato il messaggio di errore seguente in Visual Studio: "The current service model is out of sync. Make sure both the service configuration and definition files are valid."

    ![Messaggio di errore su file di configurazione e definizione del servizio non validi](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png)

Se viene visualizzato questo messaggio di errore, l'editor di proprietà non funzionerà finché non verrà risolto il problema di incoerenza.

### Testare l'applicazione

1.  Premere CTRL+F5 per eseguire il progetto.

    ![Pagina MailingList Index vuota](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png)

2.  Utilizzare la funzione **Create** per aggiungere alcune liste di distribuzione, quindi verificare il corretto funzionamento delle opzioni **Edit** e **Delete**.

    ![Pagina MailingList Index con righe](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png)

SubscriberCreazione e test del controller e delle viste Subscriber
------------------------------------------------------------------

L'interfaccia utente Web **Subscriber** viene utilizzata dagli amministratori per aggiungere nuovi sottoscrittori a una lista di distribuzione, nonché per modificare, visualizzare ed eliminare i sottoscrittori esistenti.

### Aggiungere la classe di entità Subscriber alla cartella Models

La classe di entità `Subscriber` viene utilizzata per le righe della tabella `MailingList` contenenti informazioni sui sottoscrittori di una lista. Queste righe contengono informazioni come l'indirizzo di posta elettronica della persona, con l'indicazione se è stato o meno verificato.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Models* nel progetto MVC e scegliere **Aggiungi elemento esistente**.

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *Subscriber.cs* nella cartella *Models* e fare clic su **Aggiungi**.

3.  Aprire il file *Subscriber.cs* ed esaminare il codice.

             public class Subscriber : TableEntity
             {
                 [Required]
                 public string ListName
                 {
                     get
                     {
                         return this.PartitionKey;
                     }
                     set
                     {
                         this.PartitionKey = value;
                     }
                 }
            
                 [Required]
                 [Display(Name = "Email Address")]
                 public string EmailAddress
                 {
                     get
                     {
                         return this.RowKey;
                     }
                     set
                     {
                         this.RowKey = value;
                     }
                 }
            
                 public string SubscriberGUID { get; set; }
            
                 public bool
         Verified { get; set; }
             }

    Analogamente alla classe di entità `MailingList`, la classe di entità `Subscriber` viene utilizzata per leggere e scrivere righe nella tabella `mailinglist`. `Come chiave delle righe `Subscriber viene utilizzato l'indirizzo di posta elettronica anziché la costante "mailinglist". Per una descrizione della struttura della tabella, vedere la [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/). Pertanto, viene definita una proprietà `EmailAddress` che utilizza la proprietà `RowKey` come campo sottostante, allo stesso modo in cui `ListName` utilizza `PartitionKey`. Come illustrato in precedenza, in questo modo è possibile inserire gli attributi di formattazione e convalida DataAnnotations nelle proprietà.

    Il valore `SubscriberGUID`, generato quando viene creata un'entità `Subscriber`, viene utilizzato nei collegamenti di sottoscrizione e di annullamento della sottoscrizione per assicurarsi che gli indirizzi di posta elettronica corrispondenti vengano utilizzati solo dalle persone autorizzate. Quando viene inizialmente creata una riga per un nuovo sottoscrittore, il valore di `Verified ` è `false`. Diventa `true` solo dopo che il nuovo sottoscrittore ha fatto clic sul collegamento ipertestuale `Confirm` nel messaggio di posta elettronica di benvenuto.**** Se un messaggio viene inviato a un elenco mentre il valore di `Verified` per uno dei sottoscrittori è `false`, a tale sottoscrittore non viene inviato alcun messaggio di posta elettronica.

    La proprietà `Verified` nell'entità `Subscriber` è definita come nullable. Quando si specifica che una query deve restituire le entità `Subscriber`, è possibile che per alcune delle righe recuperate non sia disponibile una proprietà `Verified`. Pertanto, l'entità `Subscriber` definisce la propria proprietà `Verified` come nullable in modo che rifletta più accuratamente il contenuto effettivo di una riga, se una query restituisce righe della tabella senza una proprietà *Verified*. A differenza delle tabelle di SQL Server, in cui ogni riga presenta lo stesso schema, nelle tabelle di Archiviazione di Azure ogni riga è una semplice raccolta di proprietà ed è possibile che a ognuna sia associato un set di proprietà diverso. Ad esempio, nell'applicazione di esempio Azure Email Service le righe con "MailingList" come chiave di riga non hanno una proprietà `Verified`. Se una query restituisce una riga di tabella senza la proprietà `Verified`, quando viene creata un'istanza della classe di entità `Subscriber` la proprietà `Verified` nell'oggetto entità sarà Null. Se la proprietà non fosse nullable, si otterrebbe lo stesso valore `false` per le righe in cui `Verified` è `false` e per quelle senza una proprietà `Verified`. Pertanto, una procedura consigliata per la gestione delle tabelle di Azure consiste nel rendere nullable ogni proprietà di una classe di entità, in modo da leggere accuratamente le righe create con classi di entità diverse o con versioni diverse della classe di entità corrente.

### Aggiungere il controller MVC Subscriber

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Controllers* nel progetto MVC e scegliere **Aggiungi elemento esistente**.

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *SubscriberController.cs* nella cartella *Controllers* e fare clic su **Aggiungi**. Assicurarsi di recuperare *Subscriber.cs* e non *Subscribe.cs*, che verrà aggiunto in seguito.**

3.  Aprire il file *SubscriberController.cs* ed esaminare il codice.

    La maggior parte del codice di questo controller è simile a quello del controller `MailingList`. Anche il nome della tabella è identico, perché le informazioni sui sottoscrittori vengono mantenute nella tabella `MailingList`. Dopo il metodo `FindRow` è riportato un metodo `GetListNames`. Questo metodo ottiene i dati per un elenco a discesa nelle pagine **Create** e **Edit**, da cui è possibile selezionare la lista di distribuzione a cui sottoscrivere un indirizzo di posta elettronica.

         private List<MailingList> GetListNames()
         {
             var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
             var lists = mailingListTable.ExecuteQuery(query).ToList();
             return lists;
         }

    Questa è la stessa query illustrata per il controller `MailingList`. Per l'elenco a discesa, sono necessarie informazioni sulle liste di distribuzione, quindi selezionare solo quelle con RowKey = "mailinglist".

    Per il metodo che recupera i dati per la pagina **Index**, sono necessarie le righe con informazioni sui sottoscrittori, quindi selezionare tutte le righe senza RowKey = "MailingList".

         public ActionResult Index()
         {
             var query = (new TableQuery<Subscriber>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist")));
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();
             return View(subscribers);
         }

    Si noti che la query specifica che i dati verranno letti in oggetti `Subscriber` (tramite `<Subscriber>`) ma i dati verranno letti dalla tabella `mailinglist`.

    **Nota:** il numero di sottoscrittori potrebbe diventare troppo elevato per poterlo gestire in questo modo in una singola query. In una versione futura dell'esercitazione ci auguriamo di implementare una funzionalità di paging e di illustrare come gestire i token di continuazione. È necessario gestire i token di continuazione quando si eseguono query che possono restituire più di 1.000 righe: Azure restituisce 1.000 righe e un token di continuazione da utilizzare per eseguire un'altra query che inizia dove è terminata quella precedente. Esplora archivi Azure non gestisce i token di continuazione, pertanto le query non restituiranno più di 1.000 righe. Per ulteriori informazioni sui set di risultati di grani dimensioni e sui token di continuazione, vedere gli articoli relativi a come [sfruttare al meglio le tabelle di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) e come [prevedere i token di continuazione nelle tabelle di Azure](http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously).

    Nel metodo `HttpGet Create` vengono configurati i dati per l'elenco a discesa e nel metodo `HttpPost` vengono impostati i valori predefiniti prima di salvare la nuova entità.

         public ActionResult Create()
         {
             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description");
             var model = new Subscriber() { Verified = false };
             return View(model);
         }

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(Subscriber subscriber)
         {
             if (ModelState.IsValid)
             {
                 subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                 if (subscriber.Verified.HasValue == false)
                 {
                     subscriber.Verified = false;
                 }

                 var insertOperation = TableOperation.Insert(subscriber);
                 mailingListTable.Execute(insertOperation);
                 return RedirectToAction("Index");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);

             return View(subscriber);
         }

    La pagina `HttpPost Edit` è più complessa di quella del controller `MailingList`, perché la pagina `Subscriber` consente di cambiare il nome della lista o l'indirizzo di posta elettronica, che sono entrambi campi chiave. Se l'utente cambia uno di questi campi, è necessario eliminare il record esistente e aggiungerne uno nuovo, anziché aggiornare quello esistente. Nel codice seguente viene illustrata la parte del metodo Edit che gestisce le diverse procedure per le modifiche chiave e non chiave:

             if (ModelState.IsValid)
             {
                 try
                 {
                     UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
                     if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
                     {
                         //Keys didn't change -- Update the row
                         var replaceOperation = TableOperation.Replace(editedSubscriber);
                         mailingListTable.Execute(replaceOperation);
                     }
                     else
                     {
                         // Keys changed, delete the old record and insert the new one.
                         if (editedSubscriber.PartitionKey != partitionKey)
                         {
                             // PartitionKey changed, can't do delete/insert in a batch.
                             var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                             mailingListTable.Execute(deleteOperation);
                             var insertOperation = TableOperation.Insert(editedSubscriber);
                             mailingListTable.Execute(insertOperation);
                         }
                         else
                         {
                             // RowKey changed, do delete/insert in a batch.
                             var batchOperation = new TableBatchOperation();
                             batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                             batchOperation.Insert(editedSubscriber);
                             mailingListTable.ExecuteBatch(batchOperation);
                         }
                     }
                     return RedirectToAction("Index");

    I parametri passati dallo strumento di associazione di modelli MVC al metodo `Edit` includono i valori originali del nome della lista e dell'indirizzo di posta elettronica (nei parametri `partitionKey` e `rowKey` parameters) e i valori immessi dall'utente (nei parametri `listName` e `emailAddress`):

         public ActionResult Edit(string partitionKey, string rowKey, string listName, string emailAddress)

    I parametri passati al metodo `UpdateModel` escludono le proprietà `PartitionKey` e `RowKey` dall'associazione di modelli:

             var excludeProperties = new string[] { "PartitionKey", "RowKey" };

    Il motivo è che le proprietà `ListName` e `EmailAddress` utilizzano `PartitionKey` e `RowKey` come proprietà sottostanti ed è possibile che l'utente abbia cambiato uno di questi valori. Quando lo strumento di associazione di modelli aggiorna il modello impostando la proprietà `ListName`, la proprietà `PartitionKey` viene automaticamente aggiornata. Se invece lo strumento di associazione di modelli aggiornasse la proprietà `PartitionKey` con il relativo valore originale dopo l'aggiornamento della proprietà `ListName`, sovrascriverebbe il nuovo valore impostato dalla proprietà `ListName`. La proprietà `EmailAddress` aggiorna automaticamente la proprietà `RowKey` nello stesso modo.

    Dopo l'aggiornamento dell'oggetto modello `editedSubscriber`, il codice determina se la chiave di partizione o la chiave di riga è stata cambiata. Se uno di questi valori è stato cambiato, la riga del sottoscrittore esistente deve essere eliminata e ne deve essere inserita una nuova. Se è stato cambiato solo uno di questi valori, le operazioni di eliminazione e inserimento possono essere eseguite in una transazione batch atomica.

    Si noti che il codice crea una nuova entità da passare all'operazione `Delete`:

             // RowKey changed, do delete/insert in a batch.
             var batchOperation = new TableBatchOperation();
             batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
             batchOperation.Insert(editedSubscriber);
             mailingListTable.ExecuteBatch(batchOperation);

    Le entità passate alle operazioni in un batch devono essere entità distinte. Ad esempio, non è possibile creare un'entità `Subscriber`, passarla a un'operazione `Delete`, quindi cambiare un valore nella stessa entità `Subscriber` e passarla a un'operazione `Insert`. In caso contrario, lo stato dell'entità dopo la modifica della proprietà avrebbe effetto sia per l'operazione Delete che per Insert.

    **Nota:** le operazioni di un batch devono trovarsi tutte nella stessa partizione. Poiché una modifica al nome della lista causa una modifica della chiave di partizione, non può essere eseguita in una transazione.

### Aggiungere le viste MVC Subscriber

1.  In **Esplora soluzioni** creare una nuova cartella nella cartella *Views* del progetto MVC e denominarla *Subscriber*.

2.  Fare clic con il pulsante destro del mouse sulla nuova cartella *Views\\Subscriber* e scegliere **Aggiungi elemento esistente**.

3.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare tutti e cinque i file con estensione cshtml nella cartella *Views\\Subscriber* e fare clic su **Aggiungi**.

4.  Aprire il file *Edit.cshtml* ed esaminare il codice.

         @model MvcWebRole.Models.Subscriber
            
         @{
             ViewBag.Title = "Edit Subscriber";
         }
            
         <h2>Edit Subscriber</h2>
            
         @using (Html.BeginForm()) {
             @Html.AntiForgeryToken()
             @Html.ValidationSummary(true)
              @Html.HiddenFor(model => model.SubscriberGUID)
              @Html.HiddenFor(model => model.ETag)
              <fieldset>
                 <legend>Subscriber</legend>
                 <div class="display-label">
                      @Html.DisplayNameFor(model => model.ListName)
                 </div>
                 <div class="editor-field">
                     @Html.DropDownList("ListName", String.Empty)
                     @Html.ValidationMessageFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.EmailAddress)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.EmailAddress)
                     @Html.ValidationMessageFor(model => model.EmailAddress)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.Verified)
                 </div>
                 <div class="display-field">
                     @Html.EditorFor(model => model.Verified)
                 </div>
                 <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
            
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Questo codice è simile a quello illustrato in precedenza per la vista `MailingList` **Edit**. Il valore `SubscriberGUID` non viene visualizzato, quindi non viene automaticamente inserito in un campo modulo per il metodo `HttpPost` del controller. Pertanto, per preservare questo valore viene incluso un campo nascosto.

    Le altre viste contengono codice simile a quello illustrato per il controller `MailingList`.

### Testare l'applicazione

1.  Premere CTRL+F5 per eseguire il progetto, quindi fare clic su **Subscribers**.Subscriber

    ![Pagina Subscriber Index vuota](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png)

2.  Utilizzare la funzione **Create** per aggiungere alcune liste di distribuzione, quindi verificare il corretto funzionamento delle opzioni **Edit** e **Delete**.

    ![Pagina Subscriber Index con righe](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png)

MessageCreazione e test del controller e delle viste Message
------------------------------------------------------------

L'interfaccia utente Web **Message** viene utilizzata dagli amministratori per creare, modificare e visualizzare informazioni sui messaggi pianificati per l'invio alle liste di distribuzione.

### Aggiungere la classe di entità Message alla cartella Models

La classe di entità `Message` viene utilizzata per le righe della tabella `Message` contenenti informazioni su un messaggio pianificato per l'invio a una lista. Queste righe includono informazioni come la riga dell'oggetto, la lista a cui inviare un messaggio e la data pianificata per l'invio.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Models* nel progetto MVC e scegliere **Aggiungi elemento esistente**.

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *Message.cs* nella cartella Models e fare clic su **Aggiungi**.

3.  Aprire il file *Message.cs* ed esaminare il codice.

         public class Message : TableEntity
         {
             private DateTime
         _scheduledDate;
             private long _messageRef;

             public Message()
             {
                 this.MessageRef = DateTime.Now.Ticks;
                 this.Status = "Pending";
             }

             [Required]
             [Display(Name = "Scheduled Date")]
             // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
             [DataType(DataType.Date)]
             public DateTime
         ScheduledDate 
             {
                 get
                 {
                     return _scheduledDate;
                 }
                 set
                 {
                     _scheduledDate = value;
                     this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
                 }
             }
                
             public long MessageRef 
             {
                 get
                 {
                     return _messageRef;
                 }
                 set
                 {
                     _messageRef = value;
                     this.RowKey = "message" + value.ToString();
                 }
             }

             [Required]
             [Display(Name = "List Name")]
             public string ListName { get; set; }

             [Required]
             [Display(Name = "Subject Line")]
             public string SubjectLine { get; set; }

             // Pending, Queuing, Processing, Complete
             public string Status { get; set; }
         }

    La classe `Message` definisce un costruttore predefinito che imposta la proprietà `MessageRef` su un valore univoco per il messaggio. Poiché il valore fa parte della chiave di riga, il setter della proprietà `MessageRef` imposta automaticamente anche la proprietà `RowKey`. Il setter della proprietà `MessageRef` concatena il valore letterale "message" e il valore `MessageRef` e li inserisce nella proprietà `RowKey`.

    Il valore di `MessageRef` viene creato ottenendo il valore di `Ticks` da `DateTime.Now`. In questo modo è possibile assicurarsi che, per impostazione predefinita, quando i messaggi vengono visualizzati nell'interfaccia utente Web sono disposti nell'ordine in cui sono stati creati per una specifica data pianificata (`ScheduledDate` è la chiave di partizione). Sarebbe utilizzare un GUID per rendere univoche le righe del messaggio, ma in questo caso l'ordine di recupero predefinito sarebbe casuale.

    Il costruttore predefinito imposta inoltre lo stato predefinito Pending per le nuove righe di `message`.

    Per ulteriori informazioni sulla struttura della tabella `Message`, vedere la [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

### Aggiungere il controller MVC Message

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Controllers nel progetto MVC e scegliere **Aggiungi elemento esistente**.

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *MessageController.cs* nella cartella *Controllers* e fare clic su **Aggiungi**.

3.  Aprire il file *MessageController.cs* ed esaminare il codice.

    La maggior parte del codice di questo controller è simile a quello del controller `Subscriber`. La differenza è il codice inserito per utilizzare i BLOB. Per ogni messaggio, vengono caricati e archiviati in BLOB i contenuti HTML e di testo semplice sotto forma di file con estensione htm e txt.

    I BLOB vengono archiviati in appositi contenitori. L'applicazione Azure Email Service archivia tutti i BLOB in un singolo contenitore denominato "azuremailblobcontainer", quindi il codice del costruttore del controller ottiene un riferimento a questo contenitore:

         public class MessageController : Controller
         {
             private TableServiceContext serviceContext;
             private static CloudBlobContainer blobContainer;

               public MessageController()
             {
                 var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
                 // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
                 //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

                 // Get context object for working with tables and a reference to the blob container.
                 var tableClient = storageAccount.CreateCloudTableClient();
             serviceContext = tableClient.GetTableServiceContext();
                 var blobClient = storageAccount.CreateCloudBlobClient();
                 blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
             }

    Per ogni file selezionato dall'utente per il caricamento, la vista MVC fornisce un oggetto `HttpPostedFile` contenente le relative informazioni. Quando l'utente crea un nuovo messaggio, viene utilizzato l'oggetto `HttpPostedFile` per salvare il file in un BLOB. Quando l'utente modifica un messaggio, può scegliere di caricare un file sostitutivo o di lasciare invariato il BLOB.

    Il controller include un metodo chiamato dai metodi `HttpPost Create` e `HttpPost Edit` per salvare un BLOB:

         private void SaveBlob(string blobName, HttpPostedFileBase httpPostedFile)
         {
             // Retrieve reference to a blob. 
             CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);
             // Create the blob or overwrite the existing blob by uploading a local file.
             using (var fileStream = httpPostedFile.InputStream)
             {
                 blob.UploadFromStream(fileStream);
             }
         }

    Il metodo `HttpPost Create` salva i due BLOB e quindi aggiunge la riga della tabella `Message`. I BLOB vengono denominati concatenando il valore di `MessageRef` con l'estensione del nome file "htm" o "txt".

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
         {
             if (file == null)
             {
                 ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
             }

             if (txtFile == null)
             {
                 ModelState.AddModelError(string.Empty, "Please provide a Text file path");
             }

             if (ModelState.IsValid)
             {
                 SaveBlob(message.MessageRef + ".htm", file);
                 SaveBlob(message.MessageRef + ".txt", txtFile);

                 var insertOperation = TableOperation.Insert(message);
                 messageTable.Execute(insertOperation);

                 return RedirectToAction("Index");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description");
             return View(message);
         }

    Il metodo `HttpGet Edit` verifica che lo stato del messaggio recuperato sia `Pending` in modo che l'utente non possa cambiare un messaggio dopo che il ruolo di lavoro B ha iniziato a elaborarlo. Un codice simile si trova nel metodo `HttpPost Edit` e nei metodi `Delete` e `DeleteConfirmed`.

         public ActionResult Edit(string partitionKey, string rowKey)
         {
             var message = FindRow(partitionKey, rowKey);
             if (message.Status != "Pending")
             {
                 throw new Exception("Message can't be edited because it isn't in Pending status.");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description", message.ListName);
             return View(message);
         }

    Nel metodo `HttpPost Edit` il codice salva un nuovo BLOB solo se l'utente sceglie di caricare un nuovo file. Nel codice seguente la parte di gestione della concorrenza del metodo viene omessa, così come è stato eseguito per il controller `MailingList` in precedenza.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Edit(string partitionKey, string rowKey, Message editedMsg,
             DateTime scheduledDate, HttpPostedFileBase httpFile, HttpPostedFileBase txtFile)
         {
             if (ModelState.IsValid)
             {
                 var excludePropLst = new List<string>();
                 excludePropLst.Add("PartitionKey");
                 excludePropLst.Add("RowKey");

                 if (httpFile == null)
                 {
                     // They didn't enter a path or navigate to a file, so don't update the file.
                     excludePropLst.Add("HtmlPath");
                 }
                 else
                 {
                     // They DID enter a path or navigate to a file, assume it's changed.
                     SaveBlob(editedMsg.MessageRef + ".htm", httpFile);
                 }

                 if (txtFile == null)
                 {
                     excludePropLst.Add("TextPath");
                 }
                 else
                 {
                     SaveBlob(editedMsg.MessageRef + ".txt", txtFile);
                 }

                 string[] excludeProperties = excludePropLst.ToArray();

                 try
                 {
                     UpdateModel(editedMsg, string.Empty, null, excludeProperties);
                     if (editedMsg.PartitionKey == partitionKey)
                     {
                         // Keys didn't change -- update the row.
                         var replaceOperation = TableOperation.Replace(editedMsg);
                         messageTable.Execute(replaceOperation);
                     }
                     else
                     {
                         // Partition key changed -- delete and insert the row.
                         // (Partition key has scheduled date which may be changed;
                         // row key has MessageRef which does not change.)
                         var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
                         messageTable.Execute(deleteOperation);
                         var insertOperation = TableOperation.Insert(editedMsg);
                         messageTable.Execute(insertOperation);
                     }
                     return RedirectToAction("Index");
                 }

    La modifica della data pianificata causa anche la modifica della chiave di partizione, quindi è necessario eliminare e inserire una riga. Questa operazione non può essere eseguita in una transazione, perché ha effetto su più partizioni.

    Il metodo `HttpPost Delete` elimina i BLOB quando elimina la riga nella tabella:

         [HttpPost, ActionName("Delete")]
         public ActionResult DeleteConfirmed(String partitionKey, string rowKey)
         {
             // Get the row again to make sure it's still in Pending status.
             var message = FindRow(partitionKey, rowKey);
             if (message.Status != "Pending")
             {
                 throw new Exception("Message can't be deleted because it isn't in Pending status.");
             }

             DeleteBlob(message.MessageRef + ".htm");
             DeleteBlob(message.MessageRef + ".txt");
             var deleteOperation = TableOperation.Delete(message);
             messageTable.Execute(deleteOperation);
             return RedirectToAction("Index");
         }

         private void DeleteBlob(string blobName)
         {
             var blob = blobContainer.GetBlockBlobReference(blobName);
             blob.Delete();
         }

### Aggiungere le viste MVC Message

1.  In **Esplora soluzioni** creare una nuova cartella nella cartella *Views* del progetto MVC e denominarla `Message`.

2.  Fare clic con il pulsante destro del mouse sulla nuova cartella *Views\\Message* e scegliere **Aggiungi elemento esistente**.

3.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare tutti e cinque i file con estensione cshtml nella cartella *Views\\Message* e fare clic su **Aggiungi**.

4.  Aprire il file *Edit.cshtml* ed esaminare il codice.

         @model MvcWebRole.Models.Message
            
         @{
             ViewBag.Title = "Edit Message";
         }
            
         <h2>Edit Message</h2>
            
         @using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
         {
             @Html.AntiForgeryToken()
             @Html.ValidationSummary(true)
             @Html.HiddenFor(model => model.ETag)
             <fieldset>
                 <legend>Message</legend>
                 @Html.HiddenFor(model => model.MessageRef)
                 @Html.HiddenFor(model => model.PartitionKey)
                 @Html.HiddenFor(model => model.RowKey)
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ListName, "MailingList")
                 </div>
                 <div class="editor-field">
                     @Html.DropDownList("ListName", String.Empty)
                     @Html.ValidationMessageFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.SubjectLine)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.SubjectLine)
                     @Html.ValidationMessageFor(model => model.SubjectLine)
                 </div>
                 <div class="editor-label">
                     HTML Path: Leave blank to keep current HTML File.
                 </div>
                 <div class="editor-field">
                     <input type="file" name="file" />
                 </div>
                 <div class="editor-label">
                     Text Path: Leave blank to keep current Text File.
                 </div>
                 <div class="editor-field">
                     <input type="file" name="TxtFile" />
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ScheduledDate)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.ScheduledDate)
                     @Html.ValidationMessageFor(model => model.ScheduledDate)
                 </div>
                 <div class="display-label">
                     @Html.DisplayNameFor(model => model.Status)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.Status)
                 </div>
                <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
            
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Per il metodo `HttpPost Edit` sono necessarie una chiave di partizione e una chiave di riga, che vengono fornite dal codice in campi nascosti. I campi nascosti non sono necessari nel controller `Subscriber` perché (a) le proprietà `ListName` e `EmailAddress` nel modello `Subscriber` aggiornano le proprietà `PartitionKey` e `RowKey` e (b) le proprietà `ListName` e `EmailAddress` sono incluse con gli helper `EditorFor` nella vista Edit. Quando lo strumento di associazione di modelli MVC per il modello `Subscriber` aggiorna la proprietà `ListName`, la proprietà `PartitionKey` viene automaticamente aggiornata e quando lo strumento di associazione di modelli MVC aggiorna la proprietà `EmailAddress` nel modello `Subscriber`, la proprietà `RowKey` viene automaticamente aggiornata. Nel modello `Message` i campi mappati alla chiave di partizione e alla chiave di riga non sono campi modificabili, quindi non vengono impostati in questo modo.

    Un campo nascosto viene incluso anche per la proprietà `MessageRef`. Si tratta dello stesso valore della chiave di partizione, ma viene incluso per consentire una maggiore chiarezza del codice nel metodo `HttpPost Edit`. Includendo il campo nascosto `MessageRef`, il codice del metodo `HttpPost Edit` può fare riferimento al valore di `MessageRef` tramite questo nome quando costruisce i nomi dei file per i BLOB.

5.  Aprire il file *Index.cshtml* ed esaminare il codice.

         @model IEnumerable<MvcWebRole.Models.Message>
            
         @{
             ViewBag.Title = "Messages";
         }
            
         <h2>Messages</h2>
            
         <p>
             @Html.ActionLink("Create New", "Create")
         </p>
         <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.ListName)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.SubjectLine)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.ScheduledDate)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.Status)
                 </th>
                 <th></th>
             </tr>
             @foreach (var item in Model)
             {
                 <tr>
                     <td>
                         @Html.DisplayFor(modelItem => item.ListName)
                     </td>
                     <td>
                         @Html.DisplayFor(modelItem => item.SubjectLine)
                     </td>
                     <td>
                         @Html.DisplayFor(modelItem => item.ScheduledDate)
                     </td>
                     <td>
                         @item.Status
                     </td>
                     <td>
                         @if (item.Status == "Pending")
                         {
                             @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                             @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
                         }
                         @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
                     </td>
                 </tr>
             }
            
         </table>

    Una delle differenze rispetto alle altre viste **Index** è che i collegamenti **Edit** e **Delete** vengono visualizzati solo per i messaggi il cui stato è `Pending`:

         @if (item.Status == "Pending")
         {
             @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
             @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
         }

    In questo modo è possibile impedire all'utente di apportare modifiche a un messaggio dopo che il ruolo di lavoro A ha iniziato a elaborarlo.

    Le altre viste contengono codice simile a quello della vista **Edit** o ad altre illustrate per altri controller.

### Testare l'applicazione

1.  Premere CTRL+F5 per eseguire il progetto, quindi fare clic su **Messages**.

    ![Pagina Message Index vuota](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png)

2.  Utilizzare la funzione **Create** per aggiungere alcune liste di distribuzione, quindi verificare il corretto funzionamento delle opzioni **Edit** e **Delete**.

    ![Pagina Subscriber Index con righe](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png)

UnsubscribeCreazione e test delle viste e del controller Unsubscribe
--------------------------------------------------------------------

A questo punto verrà implementata l'interfaccia utente per il processo di annullamento della sottoscrizione.

**Nota:** in questa esercitazione verrà creato solo il controller per il processo di annullamento della sottoscrizione, non per quello di sottoscrizione. Come descritto nella [prima esercitazione](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/), l'interfaccia utente e il metodo di servizio per il processo di sottoscrizione sono stati esclusi finché non verranno implementate misure di sicurezza appropriate per il metodo di servizio. Fino a quel momento, è possibile utilizzare le pagine dell'amministratore **Subscriber** per sottoscrivere gli indirizzi di posta elettronica alle liste.

### Aggiungere il modello di vista Unsubscribe alla cartella Models

Il modello di vista `UnsubscribeVM` viene utilizzato per passare i dati tra il controller `Unsubscribe` e la relativa vista.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella `Models` nel progetto MVC e scegliere **Aggiungi elemento esistente**.

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file `UnsubscribeVM.cs` nella cartella Models e fare clic su **Aggiungi**.

3.  Aprire il file `UnsubscribeVM.cs` ed esaminare il codice.

         public class UnsubscribeVM
         {
             public string EmailAddress { get; set; }
             public string ListName { get; set; }
             public string ListDescription { get; set; }
             public string SubscriberGUID { get; set; }
             public bool
         Confirmed { get; set; }
         }

    I collegamenti Unsubscribe contengono `SubscriberGUID`. Questo valore viene utilizzato per ottenere l'indirizzo di posta elettronica, il nome della lista e la descrizione della lista dalla tabella `MailingList`. La vista contiene l'indirizzo di posta elettronica e la descrizione della lista da cui annullare la sottoscrizione, oltre a un pulsante **Confirm** che l'utente deve selezionare per completare il processo di annullamento.

### Aggiungere il controller Unsubscribe

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella `Controllers` nel progetto MVC e scegliere **Aggiungi elemento esistente**.

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *UnsubscribeController.cs* nella cartella *Controllers* e fare clic su **Aggiungi**.

3.  Aprire il file *UnsubscribeController.cs* ed esaminare il codice.

    Il controller include un metodo `HttpGet Index` che visualizza la pagina iniziale per annullare la sottoscrizione e un metodo `HttpPost Index` che elabora il pulsante **Confirm** o **Cancel**.

    Il metodo `HttpGet Index` utilizza il GUID e il nome della lista nella stringa della query per ottenere la riga della tabella `MailingList` relativa al sottoscrittore. Inserisce quindi tutte le informazioni necessarie nel modello di vista e visualizza la pagina **Unsubscribe**. Imposta la proprietà `Confirmed` su Null in modo da indicare alla vista di visualizzare la versione iniziale della pagina **Unsubscribe**.

          public ActionResult Index(string id, string listName)
          {
              if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
              {
                  ViewBag.errorMessage = "Empty subscriber ID or list name.";
                  return View("Error");
              }
              string filter = TableQuery.CombineFilters(
                  TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                  TableOperators.And,
                  TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
              var query = new TableQuery<Subscriber>().Where(filter);
              var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
              if (subscriber == null)
              {
                  ViewBag.Message = "You are already unsubscribed";
                  return View("Message");
              }
              var unsubscribeVM = new UnsubscribeVM();
              unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
              unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
              unsubscribeVM.SubscriberGUID = id;
              unsubscribeVM.Confirmed = null;
              return View(unsubscribeVM);
          }

    Nota: SubscriberGUID non si trova nella chiave di partizione o nella chiave di riga, quindi si verificherà una riduzione delle prestazioni della query quando aumenteranno le dimensioni della partizione, ovvero il numero di messaggi di posta elettronica in una lista di distribuzione. Per ulteriori informazioni sulle alternative disponibili per rendere più scalabile la query, vedere la [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

    Anche in questo caso il metodo `HttpPost Index` utilizza il GUID e il nome della lista per ottenere le informazioni sul sottoscrittore e popola le proprietà del modello di vista. Quindi, se il pulsante **Confirm** viene selezionato, elimina la riga del sottoscrittore dalla tabella `MailingList`. Se il pulsante **Confirm** viene selezionato, imposta inoltre la proprietà `Confirm` su `True`. In caso contrario, la imposta `su `False`.` Il valore della proprietà `Confirm` indica alla vista di visualizzare la versione confermata o annullata della pagina **Unsubscribe**.

         [HttpPost] 
         [ValidateAntiForgeryToken]
         public ActionResult Index(string subscriberGUID, string listName, string action)
         {
             string filter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();

             var unsubscribeVM = new UnsubscribeVM();
             unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
             unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
             unsubscribeVM.SubscriberGUID = subscriberGUID;
             unsubscribeVM.Confirmed = false;

             if (action == "Confirm")
             {
                 unsubscribeVM.Confirmed = true;
                 var deleteOperation = TableOperation.Delete(subscriber);
                 mailingListTable.Execute(deleteOperation);
             }

             return View(unsubscribeVM);
         }

### Creare le viste MVC

1.  In **Esplora soluzioni** creare una nuova cartella nella cartella *Views* del progetto MVC e denominarla *Unsubscribe*.

2.  Fare clic con il pulsante destro del mouse sulla nuova cartella *Views\\Unsubscribe* e scegliere **Aggiungi elemento esistente**.

3.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *Index.cshtml* nella cartella *Views\\Unsubscribe* e fare clic su **Aggiungi**.

4.  Aprire il file *Index.cshtml* ed esaminare il codice.

         @model MvcWebRole.Models.UnsubscribeVM
            
         @{
             ViewBag.Title = "Unsubscribe";
             Layout = null;
         }
            
         <h2>Email List Subscription Service</h2>
            
         @using (Html.BeginForm()) {
             @Html.AntiForgeryToken()
             @Html.ValidationSummary(true)
             <fieldset>
                 <legend>Unsubscribe from Mailing List</legend>
                 @Html.HiddenFor(model => model.SubscriberGUID)
                 @Html.HiddenFor(model => model.EmailAddress)
                 @Html.HiddenFor(model => model.ListName)
                 @if (Model.Confirmed == null) {
                     <p>
                         Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)

                    </p>
                     <br />
                     <p>
                         <input type="submit" value="Confirm" name="action"/> 
                            
                         <input type="submit" value="Cancel" name="action"/>
                     </p>
                 }
                 @if (Model.Confirmed == false) {
                     <p>
                         @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
                     </p>
                 }
                 @if (Model.Confirmed == true) {
                     <p>
                         @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
                     </p>
                 }
             </fieldset>
         }
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    La riga `Layout = null` specifica che il file \_Layout.cshtml non deve essere utilizzato per visualizzare la pagina. La pagina **Unsubscribe** visualizza un'interfaccia utente molto semplice, senza le intestazioni e i piè di pagina utilizzati per le pagine dell'amministratore.

    Nel corpo della pagina la proprietà `Confirmed` determina ciò che verrà visualizzato nella pagina, **ossia i pulsanti** Confirm **e** Cancel se la proprietà è Null, il messaggio unsubscribe-confirmed se la proprietà è True oppure il messaggio unsubscribe-canceled se la proprietà è False.

### Testare l'applicazione

1.  Premere CTRL+F5 per eseguire il progetto, quindi fare clic su **Subscribers**.

2.  Fare clic su **Create** e creare un nuovo sottoscrittore per una delle liste di distribuzione create durante i test precedenti.

    Lasciare aperta la finestra del browser nella pagina **Subscribers** **Index**.

3.  Aprire Esplora archivi Azure e selezionare l'account di archiviazione di test.

4.  Fare clic su **Tables** in **Storage Type**, selezionare la tabella **MailingList** e fare clic su **Query**.

5.  Fare doppio clic sulla riga del sottoscrittore aggiunta.

    ![Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-unsubscribe.png)

6.  Nella finestra di dialogo **Edit Entity** selezionare e copiare il valore di `SubscriberGUID`.

    ![Esplora archivi Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png)

7.  Tornare nella finestra del browser. Nella barra degli indirizzi del browser sostituire "Subscriber" nell'URL con "unsubscribe?ID=[valoreguid]&listName=[nomelista]", dove [valoreguid] corrisponde al GUID copiato da Esplora archivi Azure e [nomelista] è il nome della lista di distribuzione, ad esempio:

         http://127.0.0.1/unsubscribe
         ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    Verrà visualizzata la versione della pagina **Unsubscribe** in cui si chiede conferma:

    ![Pagina di annullamento della sottoscrizione](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png)

8.  Fare clic su **Confirm**. Verrà visualizzata la conferma che la sottoscrizione dell'indirizzo di posta elettronica è stata annullata.

    ![Pagina di conferma dell'avvenuto annullamento della sottoscrizione](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png)

9.  Tornare nella pagina **Subscribers** **Index** per verificare che la riga del sottoscrittore non sia più presente.

Architettura alternativa(Facoltativo) Creazione dell'architettura alternativa
-----------------------------------------------------------------------------

Le modifiche seguenti alle istruzioni si applicano se si desidera creare l'architettura alterativa, ossia eseguire l'interfaccia utente Web in un sito Web di Azure anziché in un ruolo Web del servizio cloud di Azure.

-   Quando si realizza la soluzione, creare prima il progetto di **applicazione Web ASP.NET MVC 4**, quindi aggiungere alla soluzione un progetto di **servizio cloud di Azure** con un ruolo di lavoro.

-   Archiviare la stringa di connessione di archiviazione Azure nel file Web.config anziché nel file di impostazioni del servizio cloud. Questa procedura funziona solo per i siti Web di Azure. Se si prova ad utilizzare il file Web.config per la stringa di connessione di archiviazione in un ruolo Web del servizio cloud di Azure, verrà visualizzato un messaggio di errore HTTP 500.

Aggiungere una nuova stringa di connessione denominata `StorageConnectionString` al file *Web.config*, come illustrato nell'esempio seguente:

           <connectionStrings>
              <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-MvcWebRole-20121010185535;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-MvcWebRole-20121010185535.mdf" providerName="System.Data.SqlClient" />
              <add name="StorageConnectionString" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[primarykey]" />
           </connectionStrings>

Ottenere i valori per la stringa di connessione dal [portale di gestione di Azure](http://manage.windowsazure.com): selezionare la scheda **Storage** e l'account di archiviazione, quindi fare clic su **Manage keys** nella parte inferiore della pagina.

-   Sostituire la stringa `RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString")` nel codice con `ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString`.

Passaggi successiviPassaggi successivi
--------------------------------------

Come illustrato nella [prima esercitazione della serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/), non verranno descritte in dettaglio le procedure per creare ul processo di sottoscrizione finché non verrà implementato un segreto condiviso per proteggere il metodo del servizio API Web ASP.NET. Tuttavia, la restrizione IP protegge anche il metodo del servizio ed è possibile sottoscrivere le funzionalità copiando i file seguenti dal progetto scaricato.

Per il metodo del servizio API Web ASP.NET:

-   Controllers\\SubscribeAPI.cs

Per la pagina Web aperta dai sottoscrittori quando fanno clic sul collegamento **Confirm** nel messaggio di posta elettronica generato dal metodo del servizio:

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

Nella [successiva esercitazione](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/) verrà configurato e programmato il ruolo di lavoro A, ossia quello che pianifica i messaggi di posta elettronica.

Per collegamenti a risorse aggiuntive per l'utilizzo di tabelle, code e BLOB si Archiviazione di Azure, vedere la fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

[Esercitazione 4](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)

