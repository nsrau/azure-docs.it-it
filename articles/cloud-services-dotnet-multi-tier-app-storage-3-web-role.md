<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Creazione del ruolo Web per l'applicazione Azure Email Service - 3 di 5

Questa è la terza di una serie di cinque esercitazioni in cui viene illustrato come compilare e distribuire l'applicazione di esempio Azure Email Service. Per informazioni sull'applicazione e sulla serie di esercitazioni, vedere la [prima esercitazione della serie][].

In questa esercitazione si apprenderà come:

-   Creare una soluzione contenente un progetto di servizio cloud con un ruolo Web e un ruolo di lavoro.
-   Utilizzare tabelle, BLOB e code di Azure in controller e visualizzazioni MVC 5.
-   Gestire conflitti di concorrenza quando si utilizzano le tabelle di Azure.

## Segmenti dell'esercitazione

-   [Creare la soluzione di Visual Studio][]
-   [Aggiornare la libreria client di archiviazione del pacchetto NuGet][]
-   [Configurare i progetti per l'uso dell'emulatore di archiviazione][]
-   [Configurare la traccia e gestire i riavvii][]
-   [Aggiungere codice per creare tabelle, code e contenitori BLOB nel metodo Application\_Start][]
-   [Creare e testare la lista di distribuzione][]
-   [Creare e testare il controller e le visualizzazioni Subscriber][]
-   [Creare e testare il controller e le visualizzazioni Message][]
-   [Creare e testare il controller e le visualizzazioni Unsubscribe][]
-   [Passaggi successivi][]

## <a name="cloudproject"></a>Creare la soluzione di Visual Studio

Per iniziare, creare una soluzione di Visual Studio con un progetto per il front-end Web e un progetto per uno dei ruoli di lavoro di Azure back-end. In seguito verrà aggiunto il secondo ruolo di lavoro.

### Creare un progetto di servizio cloud con un ruolo Web e un ruolo di lavoro

1.  Avviare Visual Studio.

2.  Scegliere **Nuovo progetto** dal menu **File**.

    ![Menu Nuovo progetto][]

3.  Espandere **C\#** e selezionare **Cloud** in **Modelli installati**, quindi selezionare **Servizio cloud Azure**.

4.  Assegnare il nome **AzureEmailService** all'applicazione e fare clic su **OK**.

    ![New Project dialog box][]

5.  Nella finestra di dialogo **New Azure Cloud Service** selezionare **Ruolo Web ASP.NET** e fare clic sulla freccia che punta a destra.

    ![Finestra di dialogo New Azure Cloud Project][]

6.  Nella colonna a destra passare il puntatore su **WebRole1** e quindi fare clic sull'icona della matita per cambiare il nome del ruolo Web.

7.  Immettere MvcWebRole come nuovo nome, quindi premere INVIO.

    ![Finestra di dialogo New Azure Cloud Project - ridenominazione del ruolo Web][]

8.  Attenersi alla stessa procedura per aggiungere un **Ruolo di lavoro**, assegnare il nome WorkerRoleA e quindi fare clic su **OK**.

    ![Finestra di dialogo New Azure Cloud Project - aggiunta di un ruolo di lavoro][]

9.  Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC**, selezionare la casella di controllo **API Web**, quindi fare clic su **Modifica autenticazione**.

    ![New Project dialog box][1]

10. Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione**, quindi fare clic su **OK**.

    ![No authentication][]

11. Nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **OK**.

### Impostare l'intestazione, il menu e il piè di pagina

In questa sezione viene illustrato come aggiornare le intestazioni, i piè di pagina e le voci di menu visibili in ogni pagina dell'interfaccia utente Web dell'amministratore. L'applicazione includerà tre set di pagine Web dell'amministratore, una per le liste di distribuzione, una per i sottoscrittori di tali liste e una per i messaggi.

1.  Se non è stata ancora scaricata la [soluzione completa][], scaricarla prima di procedere al passaggio successivo.

    Se è necessario aggiungere codice nella parte rimanente dell'esercitazione, sarà possibile copiare file dal progetto scaricato al nuovo progetto, invece di copiare e incollare frammenti di codice. L'esercitazione illustra e spiega le parti fondamentali del codice copiato.

    Per aggiungere un file dal progetto scaricato, fare clic con il pulsate destro del mouse sul progetto o sulla cartella a cui si vuole aggiungere il file, quindi scegliere **Aggiungi - Elemento esistente** dal menu di scelta rapida. Passare quindi alla posizione in cui è stato scaricato il progetto completato, selezionare i file da usare, quindi fare clic su **Aggiungi**. In caso di visualizzazione della finestra di dialogo **File di destinazione esistente**, fare clic su **Sì**.

2.  Nel progetto MvcWebRole aggiungere il file *Views\\Shared\_Layout.cshtml* dal progetto scaricato, facendo clic con il pulsante destro del mouse sulla cartella *Shared* in *Views* per aggiungere il file.

    Saranno aggiunte voci per intestazione, piè di pagina e menu per le pagine Mailing List, Message e Subscriber:

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### Eseguire l'applicazione in locale

1.  Premere CTRL+F5 per eseguire l'applicazione.

    Se si è abituati ad avviare progetti Web diversi da progetti del servizio cloud di Azure, si noterà che il processo di visualizzazione della home page nel browser richiede più tempo rispetto al solito.

    ![home page][]

    Il ritardo è dovuto al fatto che Visual Studio avvia l'emulatore di calcolo di Azure e l'emulatore di archiviazione di Azure. Nell'area di notifica di Windows verrà visualizzata l'icona dell'emulatore di calcolo:

    ![Emulatore di calcolo nell'area di notifica][]

2.  Chiudere il browser.

## <a name="updatescl"></a>Aggiornare la libreria client di archiviazione del pacchetto NuGet

Il framework di API utilizzato per le tabelle, le code e i BLOB di Archiviazione di Azure si chiama Storage Client Library (SCL). Questa API è inclusa in un pacchetto NuGet nel modello di progetto di servizio cloud. Gli aggiornamenti per SCL, tuttavia, sono spesso rilasciati dopo la creazione dei modelli del progetto. È quindi sempre consigliabile verificare la disponibilità di aggiornamenti per il pacchetto NuGet SCL.

1.  Nel menu **Strumenti** di Visual Studio passare il puntatore su **Gestione pacchetti libreria**, quindi fare clic su **Manage NuGet Packages for Solution**.

    ![Manage NuGet Packages for Solution nel menu][]

2.  Nel riquadro sinistro della finestra di dialogo **Manage NuGet Packages** selezionare **Updates**, quindi scorrere verso il basso fino al pacchetto **Archiviazione di Azure** e fare clic su **Aggiorna**.

    ![Pacchetto Archiviazione di Azure nella finestra di dialogo Manage NuGet Packages][]

3.  Nella finestra di dialogo **Seleziona progetti** assicurarsi che siano selezionati entrambi i progetti e quindi fare clic su **OK**.

    ![Selezione di entrambi i progetti nella finestra di dialogo Seleziona progetti][]

4.  Accettare le condizioni di licenza per completare l'installazione del pacchetto, quindi chiudere la finestra di dialogo **Manage NuGet Packages**.

## <a name="configurestorage"></a>Configurare i progetti per l'uso dell'emulatore di archiviazione

Il codice per il ruolo Web e il ruolo di lavoro che sarà aggiunto in seguito userà una stringa di connessione con nome StorageConnectionString per la connessione all'Archiviazione di Azure. In questa sezione l'impostazione sarà aggiunta alle proprietà del ruolo e sarà configurata per l'uso dell'emulatore di archiviazione. La seconda esercitazione della serie mostra come configurare la stringa di connessione per l'uso di un account di archiviazione di Azure.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **MvcWebRole** nella sezione **Ruoli** del progetto cloud **AzureEmailService**, quindi scegliere **Proprietà**.

    ![Proprietà del ruolo Web][]

2.  Assicurarsi che nell'elenco a discesa **Configurazione del servizio** sia selezionata l'opzione **Tutte le configurazioni**.

3.  Selezionare la scheda **Impostazioni** e quindi fare clic su **Aggiungi impostazione**.

4.  Immettere "StorageConnectionString" nella colonna **Nome**.

5.  Scegliere **Stringa di connessione** nell'elenco a discesa **Tipo**.

6.  Fare clic sui puntini di sospensione (**...**) all'estrema destra della riga per aprire la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

    ![Pulsante destro del mouse su Proprietà][]

7.  Nella finestra di dialogo **Crea stringa di connessione di archiviazione** fare clic sul pulsante di opzione **Emulatore di archiviazione di Azure** e quindi su **OK**.

    La stringa di connessione `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` è impostata sull'emulatore di archiviazione per impostazione predefinita, quindi non occorre modificarla.

8.  Per aggiungere la stringa di connessione di archiviazione per il ruolo WorkerRoleA, eseguire la stessa procedura usata per il ruolo MvcWebRole.

La nuova impostazione aggiunta con il pulsante **Aggiungi impostazioni** è stata aggiunta al codice XML nel file *ServiceDefinition.csdf* e in ognuno dei due file di configurazione *.cscfg*. In Visual Studio viene aggiunto il codice XML seguente nel file *ServiceDefinition.csdf*.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

In ogni file di configurazione *.cscfg* viene aggiunto il codice XML seguente.

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

È possibile aggiungere manualmente impostazioni nel file *ServiceDefinition.csdf* e nei due file di configurazione *.cscfg*, ma l'utilizzo dell'editor di proprietà offre i vantaggi seguenti per le stringhe di connessione:

-   La nuova impostazione viene aggiunta in un'unica posizione e in tutti e tre i file viene aggiunto il codice XML di impostazione corretto.
-   Per tutti e tre i file di impostazioni viene generato il codice XML corretto. Il file *ServiceDefinition.csdf* definisce le impostazioni che devono essere presenti in ogni file di configurazione *.cscfg*. Se le impostazioni presenti nel file *ServiceDefinition.csdf* non sono coerenti con quelle aggiunte nei file di configurazione *.cscfg*, è possibile che venga visualizzato il messaggio di errore seguente in Visual Studio: *Il modello di servizio corrente non è sincronizzato. Verificare la validità dei file di configurazione e di definizione del servizio.*

    ![Messaggio di errore su file di configurazione e definizione del servizio non validi][]

Se si riceve questo messaggio di errore, l'editor di proprietà non funzionerà finché non sarà risolto manualmente il problema di incoerenza tramite la modifica dei file.

## <a name="tracing"></a>Configurare la traccia e gestire i riavvii

1.  Nel progetto MvcWebRole aggiungere il file *WebRole.cs* al progetto scaricato,

in modo da aggiungere un metodo che configura la registrazione ed effettua la chiamata dal metodo `OnStart` che sarà eseguito all'avvio del ruolo Web. Il codice nel nuovo metodo `ConfigureDiagnostics` è illustrato nella [seconda esercitazione][].

Sarà anche aggiunto codice che sarà eseguito quando il ruolo Web riceve una notifica relativa all'arresto imminente del ruolo stesso. Le applicazioni del servizio cloud di Azure vengono riavviate circa due volte al mese per gli aggiornamenti del sistema operativo. Per altre informazioni sugli aggiornamenti del sistema operativo, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo][]. Quando un'applicazione Web sta per essere arrestata, viene generato un evento `OnStop`. Il boilerplate del ruolo Web creato da Visual Studio non esegue l'override del metodo `OnStop`, quindi l'applicazione avrà solo pochi secondi per completare le richieste HTTP prima di essere arrestata. È possibile aggiungere codice per eseguire l'override del metodo `OnStop` e assicurarsi che gli arresti vengano eseguiti normalmente.

Il file appena aggiunto include il seguente override del metodo `OnStop`.

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

Il metodo `OnStop` ha a disposizione al massimo 5 minuti per la chiusura prima dell'arresto dell'applicazione. Sarebbe possibile aggiungere una chiamata di sospensione di 5 minuti al metodo `OnStop` per assegnare all'applicazione la quantità massima di tempo per elaborare le richieste correnti, ma se l'applicazione è correttamente dimensionata, dovrebbe essere in grado di elaborare le richieste rimanenti in molto meno di 5 minuti. È consigliabile interrompere l'applicazione il più rapidamente possibile, in modo che possa essere riavviata altrettanto rapidamente per continuare a elaborare le richieste.

Quando un ruolo viene portato offline da Azure, il servizio di bilanciamento del carico interrompe l'invio di richieste all'istanza del ruolo e in seguito viene chiamato il metodo `OnStop`. Se non si dispone di un'altra istanza del ruolo, non verrà elaborata alcuna richiesta finché non vengono completati l'arresto e il riavvio del ruolo, operazioni che in genere richiedono diversi minuti. Questo è uno dei motivi per cui il contratto di servizio di Azure richiede che vi siano almeno due istanze di ogni ruolo in modo da trarre vantaggio dal tempo di attività garantito.

Nel codice illustrato per il metodo `OnStop` viene creato un contatore delle prestazioni di ASP.NET per `Requests Current`. Il valore del contatore `Requests Current` contiene il numero corrente di richieste, incluse quelle in coda, in esecuzione o in attesa di essere scritte nel client. Il valore `Requests Current` è controllato ogni secondo e, quando diventa zero, sarà restituito il metodo `OnStop`. Dopo la restituzione di `OnStop`, il ruolo sarà arrestato.

I dati traccia non vengono salvati quando vengono chiamati dal metodo `OnStop` senza l'esecuzione di un [trasferimento su richiesta][]. È possibile visualizzare le informazioni di traccia di `OnStop` in tempo reale con l'utilità [dbgview][] da una connessione Desktop remoto.

## <a name="createifnotexists"></a>Aggiungere codice per creare tabelle, code e contenitori BLOB nel metodo Application\_Start

L'applicazione Web userà la tabella `MailingList`, la tabella `Message`, la coda `azuremailsubscribequeue` e il contenitore BLOB `azuremailblobcontainer`. Sarebbe possibile creare questi elementi manualmente con uno strumento come Esplora archivi di Azure, ma in questo caso sarebbe necessario eseguire questa operazione manualmente ogni volta che l'applicazione viene avviata con un nuovo account di archiviazione. In questa sezione viene illustrato come aggiungere codice che viene eseguito all'avvio dell'applicazione, controlla se esistono le tabelle, le code e i contenitori BLOB necessari e in caso contrario li crea.

È possibile aggiungere questo codice di avvio eseguibile una sola volta al metodo `OnStart` nel file *WebRole.cs* o nel file *Global.asax*. Per questa esercitazione, l'Archiviazione di Azure sarà inizializzata nel file *Global.asax*.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto MvcWebRole, quindi aggiungere il file *Global.asax.cs* dal progetto scaricato.

È stato aggiunto un nuovo metodo, chiamato dal metodo `Application_Start`:

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

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

Nelle sezioni seguenti verranno compilati i componenti dell'applicazione Web e sarà possibile testarli con i servizi di archiviazione per lo sviluppo oppure con l'account di archiviazione senza la necessità di creare prima manualmente tabelle, code o contenitori BLOB.

## <a name="mailinglist"></a>Creare e testare il controller e le visualizzazioni Mailing List

L'interfaccia utente Web **Mailing List** viene usata dagli amministratori per creare, modificare e visualizzare liste di distribuzione, ad esempio "Annunci del Dipartimento di storia della Contoso University " e "Annunci di lavoro di Fabrikam Engineering".

### Aggiungere la classe di entità MailingList alla cartella Models

La classe di entità `MailingList` viene usata per le righe della tabella `MailingList` che contengono informazioni sulla lista, ad esempio la descrizione e l'indirizzo di posta elettronica del mittente per i messaggi inviati alla lista.

1.  Nella cartella `Models` del progetto MVC aggiungere il file *MailingList.cs* dal progetto scaricato.

    Si userà la classe di entità `MailingList` per la lettura e la scrittura di liste di distribuzione nella tabella mailinglist.

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

    L'API di Archiviazione di Azure richiede che le classi di entità per le operazioni su tabella derivino da [TableEntity][]. `TableEntity` definisce i campi `PartitionKey`, `RowKey`, `TimeStamp` e `ETag`. Le proprietà `TimeStamp` e `ETag` sono usate dal sistema. Più avanti in questa esercitazione sarà illustrato come usare la proprietà `ETag` per la gestione della concorrenza.

    È disponibile anche una classe [DynamicTableEntity][] da utilizzare se si desidera gestire le righe di tabella come raccolte Dictionary di coppie chiave-valore anziché utilizzare le classi di modello predefinite. Per altre informazioni, vedere il blog di [approfondimento sulle tabelle della libreria client 2.0 di Archiviazione di Azure][].

    La chiave di partizione della tabella `mailinglist` corrisponde al nome della lista. In questa classe di entità il valore della chiave di partizione è accessibile tramite la proprietà `PartitionKey`, definita nella classe `TableEntity`, o tramite la proprietà `ListName`, definita nella classe `MailingList`. La proprietà `ListName` usa `PartitionKey` come variabile sottostante. Definendo la proprietà `ListName` , è possibile usare un nome di variabile più descrittivo nel codice e risulta più facile programmare l'interfaccia utente Web, poiché gli attributi di convalida e DataAnnotation possono essere aggiunti alla proprietà `ListName`, ma non possono essere aggiunti direttamente alla proprietà `PartitionKey`.

    Tramite l'attributo `RegularExpression` della proprietà `ListName`, MVC convalida l'input utente per verificare che il valore del nome della lista contenga solo caratteri alfanumerici o di sottolineatura. Questa restrizione è stata implementata per semplificare i nomi delle liste in modo che possano essere facilmente utilizzati nelle stringhe delle query negli URL.

    > [WACOM.NOTE] Se si vuole un formato meno restrittivo per il nome della lista, è possibile consentire l'uso di altri caratteri e applicare la codifica URL ai nomi usati nelle stringhe delle query. Tuttavia, alcuni caratteri non sono consentiti nelle chiavi di partizione o nelle chiavi di riga delle tabelle di Azure e sarà necessario escludere almeno questi caratteri. Per informazioni sui caratteri non consentiti o che causano problemi nei campi delle chiavi di partizione o delle chiavi di riga, vedere [Informazioni sul modello di dati del servizio tabelle][] e [Carattere % in PartitionKey o RowKey][].

    La classe `MailingList` definisce un costruttore predefinito che imposta `RowKey` sulla stringa "mailinglist" hardcoded, in quanto tutte le righe della lista di distribuzione presenti in questa tabella hanno questo valore come chiave di riga. Per una descrizione della struttura della tabella, vedere la [prima esercitazione della serie][]. Per questo scopo è possibile scegliere qualsiasi valore di costante, purché non sia mai identico a un indirizzo di posta elettronica, che corrisponde alla chiave di riga per le righe del sottoscrittore nella tabella.

    È necessario immettere sempre il nome della lista e l'indirizzo di posta elettronica del mittente quando viene creata una nuova entità `MailingList`, in modo che abbiano gli attributi `Required`.

    Gli attributi `Display` definiscono la didascalia da utilizzare per un campo nell'interfaccia utente MVC.

### Aggiungere il controller MVC MailingList

1.  Nella cartella *Controller* del progetto MVC aggiungere il file *MailingListController.cs* dal progetto scaricato.

    Il costruttore predefinito del controller crea un oggetto `CloudTable` da usare con la tabella `mailinglist`.

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    Il codice ottiene le credenziali dell'account di archiviazione di Azure dal file di impostazioni del progetto di servizio cloud per stabilire una connessione con l'account di archiviazione. Queste impostazioni verranno configurate più avanti nell'esercitazione, prima di testare il controller.

    Il metodo successivo, `FindRowAsync`, viene chiamato ogni volta che il controller deve cercare una voce specifica della lista di distribuzione nella tabella `MailingList`, ad esempio per modificarla. Il codice recupera una singola entità `MailingList` usando i valori passati relativi alla chiave di partizione e alla chiave di riga. Le righe modificate dal controller sono quelle identificate dalla chiave "MailingList", quindi è possibile che "MailingList" sia hardcoded per la chiave di riga, ma il modello usato per i metodi `FindRow` in tutti i controller prevede di specificare sia una chiave di partizione che una chiave di riga.

    > [WACOM.NOTE] L'applicazione usa codice asincrono ASP.NET 4.5 per operazioni I/O nel ruolo Web per usare in modo efficiente le risorse del server. Per informazioni sul codice asincrono nell'applicazione Web, vedere [Utilizzare il supporto asincrono di .NET 4.5 per evitare chiamate di blocco][].

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    Il codice in questo metodo `FindRow` restituisce una riga della lista di distribuzione. Il codice nel metodo `FindRow` corrispondente nel controller `Subscriber` restituisce una riga Subscriber dalla stessa tabella `mailinglist`. Il codice nei due metodi è identico, ad eccezione del tipo di modello usato con il metodo [TableOperation.Retrieve][].

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    L'oggetto `TableOperation` restituito dal metodo `TableOperation.Retrieve` specifica lo schema, ovvero le proprietà, della riga o delle righe che si prevede vengano restituite dalla query. Una singola tabella può avere schemi diversi in righe diverse. In genere, si specifica lo stesso tipo di modello quando si legge una riga utilizzata per creare la riga.

    Nella pagina **Index** sono visualizzate tutte le righe della lista di distribuzione, quindi la query nel metodo `Index` restituisce tutte le entità `MailingList` identificate dalla chiave di riga "mailinglist". Le altre righe della tabella hanno l'indirizzo di posta elettronica come chiave di riga e contengono informazioni sul sottoscrittore.

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    Il metodo `ExecuteQuerySegmentedAsync` suddivide in segmenti i set di risultati di grandi dimensioni. Restituisce fino a 1.000 righe. Quando si esegue una query in grado di recuperare più di 1.000 righe, si ottengono 1.000 righe e un token di continuazione. È possibile usare il token di continuazione per eseguire un'altra query che inizia dove è terminata quella precedente. Il codice riportato è semplificato per un'applicazione di esempio: tutti i segmenti sono aggregati in un elenco. Per un'applicazione di produzione sarebbe necessario implementare codice di paging. Per altre informazioni sui set di risultati di grani dimensioni e sui token di continuazione, vedere gli articoli relativi a come [sfruttare al meglio le tabelle di Azure][] e come [prevedere i token di continuazione nelle tabelle di Azure][].

    Quando si crea l'oggetto `OperationContext`, è possibile impostare il valore della proprietà `ClientID`, in modo da fornire un identificatore univoco che sarà incluso nei log scritti dall'Archiviazione di Azure. È possibile usare l'identificatore per fare risalire i log di operazioni di transazione al codice che ha provocato l'attività del servizio di archiviazione. Per informazioni sulla registrazione di archiviazione di Azure, vedere il post di blog relativo alla [registrazione di archiviazione di Azure e all'uso dei log per tenere traccia delle richieste di archiviazione][].

    Con l'API SCL 2.1 e versioni successive è anche possibile usare LINQ per le query su tabelle. Per esempi di codice che mostrano come usare LINQ, vedere [PhluffyFotos][].

    Se non si specificano criteri per l'esecuzione di nuovi tentativi, l'API esegue automaticamente tre tentativi con limiti di timeout incrementati in modo esponenziale. Per un'interfaccia Web in cui un utente attende la visualizzazione della pagina, si tratta di tempi di attesa inaccettabilmente lunghi. Pertanto, questo codice specifica una ripetizione lineare dei tentativi, in modo che il limite di timeout non venga incrementato ogni volta, e un limite di timeout ragionevole per l'attesa dell'utente. Il criterio per l'esecuzione di nuovi tentativi è specificato nell'oggetto `webUIRetryPolicy` passato al metodo `ExecuteQuerySegmentedAsync`. L'oggetto `webUIRetryPolicy` è definito nel costruttore del controller:

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    Il metodo `Index` include un blocco try-catch progettato per la gestione di condizioni di timeout.

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    Quando l'utente fa clic sul pulsante **Create** della pagina **Create**, lo strumento di associazione di modelli MVC crea un'entità `MailingList` dall'input immesso nella visualizzazione e il metodo `HttpPost Create` la aggiunge alla tabella.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    Per la pagina **Edit** il metodo `HttpPost Edit` aggiorna la riga.

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    Un blocco catch gestisce gli errori di concorrenza nel metodo `HttpPost Edit`.

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
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

    Se un utente seleziona una lista di distribuzione per la modifica e quindi, mentre è visualizzata la pagina **Modifica** nel browser, un altro utente modifica la stessa lista di distribuzione, sarà generata un'eccezione di concorrenza. In questo caso, il codice visualizza un messaggio di avviso e indica i cambi che sono stati modificati dall'altro utente. L'API TSL usa `ETag` per verificare la presenza di conflitti di concorrenza. Il valore di `ETag` cambia a ogni aggiornamento di una riga della tabella. Quando si recupera una riga da modificare, il valore di `ETag` sarà salvato e quando si esegue un'operazione di aggiornamento o eliminazione si passerà il valore `ETag` salvato. La visualizzazione `Edit` include un campo nascosto per il valore di ETag. Se durante un aggiornamento viene rilevato che il valore di `ETag` sul record da aggiornare è diverso dal valore di `ETag` passato all'operazione di aggiornamento, sarà generata un'eccezione di concorrenza. Se si ritiene che i conflitti di concorrenza non siano importanti, è possibile impostare il campo ETag su un asterisco ("\*") nell'entità passata all'operazione di aggiornamento. In questo caso i conflitti verranno ignorati.

    Nota: l'errore HTTP 412 non è specifico per gli errori di concorrenza. Può essere generato per altri errori dall'API SCL.

    Per la pagina **Delete**, il metodo `HttpPost Delete` elimina la riga `MailingList` insieme a eventuali righe `Subscriber` ad essa associate nella tabella `MailingList`.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    Nel caso in cui sia necessario eliminare un numero elevato di sottoscrittori, il codice elimina i record in batch. Il costo della transazione per l'eliminazione di una riga è identico a quello dell'eliminazione di 100 righe in un batch. Il numero massimo di operazioni che è possibile eseguire in un batch è pari a 100.

    Benché il ciclo elabori sia le righe `MailingList` che le righe `Subscriber`, le legge tutte nella classe di entità `MailingList`, poiché gli unici campi necessari per l'operazione `Delete` sono i campi `PartitionKey`, `RowKey` e `ETag`.

### Aggiungere le visualizzazioni MVC MailingList

1.  Nella cartella *Views* del progetto MVC creare una nuova cartella con nome *MailingList*.

2.  Nella nuova cartella *Views\\MailingList* aggiungere i quattro file con estensione *cshtml* dal progetto scaricato.

Nel file *Edit.cshtml* si noti il campo nascosto incluso per preservare il valore di `ETag`, usato per gestire i conflitti di concorrenza.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

Si noti anche che il campo `ListName` include un helper `DisplayFor` invece di un helper `EditorFor`.

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

Per la pagina **Edit** la modifica del nome della lista non è stata abilitata, in quanto sarebbe stato necessario codice complesso nel controller: il metodo `HttpPost Edit` deve infatti eliminare la riga della lista di distribuzione e tutte le righe del sottoscrittore associate, quindi reinserirle con il nuovo valore di chiave. In un'applicazione di produzione si potrebbe decidere che valga la pena aggiungere questa complessità. Come sarà illustrato in seguito, il controller `Subscriber` non permette modifiche al nome della lista, poiché è interessata solo una riga alla volta.

Nel file *Index.cshtml* i collegamenti ipertestuali **Modifica** ed **Elimina** specificano parametri di stringa di query di tipo chiave di partizione e chiave di riga in modo da identificare una riga specifica. Per le entità `MailingList` è in realtà necessaria solo la chiave di partizione, in quanto la chiave di riga è sempre "MailingList", ma sono mantenute entrambe in modo che il codice delle visualizzazioni MVC sia coerente in tutti i controller e le visualizzazioni.

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### Impostare MailingList come controller predefinito

1.  Aprire il file *Route.config.cs* nella cartella *App\_Start*.

2.  Nella riga che specifica i valori predefiniti, sostituire il controller predefinito "Home" con "MailingList".

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### Testare l'applicazione

1.  Premere CTRL+F5 per eseguire il progetto.

    ![Pagina MailingList Index vuota][]

2.  Utilizzare la funzione **Create** per aggiungere alcune liste di distribuzione, quindi verificare il corretto funzionamento delle opzioni **Edit** e **Delete**.

    ![Pagina MailingList Index con righe][]

## <a name="subscriber"></a><span class="short-header">Subscriber</span>Creazione e test del controller e delle visualizzazioni Subscriber

L'interfaccia utente Web **Subscriber** viene usata dagli amministratori per aggiungere nuovi sottoscrittori a una lista di distribuzione, nonché per modificare, visualizzare ed eliminare i sottoscrittori esistenti.

### Aggiungere la classe di entità Subscriber alla cartella Models

La classe di entità `Subscriber` è usata per le righe della tabella `MailingList` contenenti informazioni sui sottoscrittori di una lista. Queste righe contengono informazioni come l'indirizzo di posta elettronica della persona, con l'indicazione se è stato o meno verificato.

1.  Nella cartella *Modelli* del progetto MVC aggiungere il file *Subscriber.cs* dal progetto scaricato.

Analogamente alla classe di entità `MailingList`, la classe di entità `Subscriber` è usata per leggere e scrivere righe nella tabella `mailinglist`.

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
        
                public bool? Verified { get; set; }
            }
        

`Subscriber`Le righe usano l'indirizzo di posta elettronica anziché la costante "mailinglist" come chiave delle righe. Per una descrizione della struttura della tabella, vedere la [prima esercitazione della serie][]. Viene quindi definita una proprietà `EmailAddress` che usa la proprietà `RowKey` come campo sottostante, allo stesso modo in cui `ListName` usa `PartitionKey`. Come illustrato in precedenza, in questo modo è possibile inserire gli attributi di formattazione e convalida DataAnnotations nelle proprietà.

Il valore `SubscriberGUID` è generato quando viene creata un'entità `Subscriber`. viene usato nei collegamenti di sottoscrizione e di annullamento della sottoscrizione per assicurarsi che gli indirizzi di posta elettronica corrispondenti vengano utilizzati solo dalle persone autorizzate.

Quando è inizialmente creata una riga per un nuovo sottoscrittore, il valore `Verified` è `false`. Il valore `Verified` diventa `true` solo dopo che il nuovo sottoscrittore ha fatto clic sul collegamento ipertestuale **Confirm** nel messaggio di posta elettronica di benvenuto. Se un messaggio è inviato a un elenco mentre il valore di `Verified` per uno dei sottoscrittori è `false`, a tale sottoscrittore non sarà inviato alcun messaggio di posta elettronica.

La proprietà `Verified` nell'entità `Subscriber` è definita come nullable. Quando si specifica che una query deve restituire le entità `Subscriber`, è possibile che per alcune delle righe recuperate non sia disponibile una proprietà `Verified`. L'entità `Subscriber` definisce quindi la propria proprietà `Verified` come nullable in modo che rifletta più accuratamente il contenuto effettivo di una riga, se una query restituisce righe della tabella senza una proprietà *Verified*. A differenza delle tabelle di SQL Server, in cui ogni riga presenta lo stesso schema, nelle tabelle di Archiviazione di Azure ogni riga è una semplice raccolta di proprietà ed è possibile che a ognuna sia associato un set di proprietà diverso. Ad esempio, nell'applicazione di esempio Azure Email Service le righe con "MailingList" come chiave di riga non hanno una proprietà `Verified`. Se una query restituisce una riga di tabella senza la proprietà `Verified`, quando viene creata un'istanza della classe di entità `Subscriber`, la proprietà `Verified` nell'oggetto entità sarà Null. Se la proprietà non fosse nullable, si otterrebbe lo stesso valore `false` per le righe in cui `Verified` è `false` e per quelle senza una proprietà `Verified`. Pertanto, una procedura consigliata per la gestione delle tabelle di Azure consiste nel rendere nullable ogni proprietà di una classe di entità, in modo da leggere accuratamente le righe create con classi di entità diverse o con versioni diverse della classe di entità corrente.

### Aggiungere il controller MVC Subscriber

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Controllers* nel progetto MVC e scegliere **Aggiungi elemento esistente**.

2.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare il file *SubscriberController.cs* nella cartella *Controllers* e fare clic su **Aggiungi**. Assicurarsi di recuperare *Subscriber.cs* e non *Subscribe.cs*, che verrà aggiunto in seguito.

La maggior parte del codice di questo controller è simile a quello del controller `MailingList`. Anche il nome della tabella è identico, perché le informazioni sui sottoscrittori vengono mantenute nella tabella `MailingList`.

Oltre al metodo `FindRowAsync`, è disponibile un metodo `FindRow`, poiché è necessario chiamarlo da un blocco catch e non è possibile chiamare un metodo asincrono da un blocco catch.

Dopo i metodi `FindRow` è presente un metodo `GetListNamesAsync`. Questo metodo ottiene i dati per un elenco a discesa nelle pagine **Create** e **Edit**, da cui è possibile selezionare la lista di distribuzione a cui sottoscrivere un indirizzo di posta elettronica.

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

Questa è la stessa query illustrata per il controller `MailingList`. Per l'elenco a discesa, sono necessarie informazioni sulle liste di distribuzione, quindi selezionare solo quelle con RowKey = "mailinglist".

Per il metodo che recupera i dati per la pagina **Index**, sono necessarie le righe con informazioni sui sottoscrittori, quindi selezionare tutte le righe senza RowKey = "MailingList".

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

Si noti che la query specifica che i dati verranno letti in oggetti `Subscriber` (tramite `<Subscriber>`) ma i dati saranno letti dalla tabella `mailinglist`.

Il numero di sottoscrittori potrebbe diventare troppo elevato per poterlo gestire in questo modo in una singola query. Come accennato in precedenza, in un'applicazione di produzione si implementerebbe il paging tramite i token di continuazione.

Nel metodo `HttpGet Create` sono configurati i dati per l'elenco a discesa e nel metodo `HttpPost` sono impostati i valori predefiniti prima di salvare la nuova entità.

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

La pagina `HttpPost Edit` è più complessa di quella del controller `MailingList`, perché la pagina `Subscriber` permette di cambiare il nome della lista o l'indirizzo di posta elettronica, che sono entrambi campi chiave. Se l'utente cambia uno di questi campi, è necessario eliminare il record esistente e aggiungerne uno nuovo, anziché aggiornare quello esistente. Nel codice seguente viene illustrata la parte del metodo Edit che gestisce le diverse procedure per le modifiche chiave e non chiave:

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

I parametri passati dallo strumento di associazione di modelli MVC al metodo `Edit` includono i valori originali del nome della lista e dell'indirizzo di posta elettronica (nei parametri `partitionKey` e `rowKey`) e i valori immessi dall'utente (nei parametri `listName` e `emailAddress`):

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

I parametri passati al metodo `UpdateModel` escludono le proprietà `PartitionKey` e `RowKey` dall'associazione di modelli:

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

Le proprietà `ListName` e `EmailAddress` usano infatti `PartitionKey` e `RowKey` come proprietà sottostanti ed è possibile che l'utente abbia cambiato uno di questi valori. Quando lo strumento di associazione di modelli aggiorna il modello impostando la proprietà `ListName`, la proprietà `PartitionKey` è automaticamente aggiornata. Se invece lo strumento di associazione di modelli aggiornasse la proprietà `PartitionKey` con il relativo valore originale dopo l'aggiornamento della proprietà `ListName`, sovrascriverebbe il nuovo valore impostato dalla proprietà `ListName`. La proprietà `EmailAddress` aggiorna automaticamente la proprietà `RowKey` nello stesso modo.

Dopo l'aggiornamento dell'oggetto modello `editedSubscriber`, il codice determina se la chiave di partizione o la chiave di riga è stata cambiata. Se uno di questi valori è stato cambiato, la riga del sottoscrittore esistente deve essere eliminata e ne deve essere inserita una nuova. Se è stato cambiato solo uno di questi valori, le operazioni di eliminazione e inserimento possono essere eseguite in una transazione batch atomica.

Si noti che il codice crea una nuova entità da passare all'operazione `Delete`:

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

Le entità passate alle operazioni in un batch devono essere entità distinte. Ad esempio, non è possibile creare un'entità `Subscriber`, passarla a un'operazione `Delete`, quindi cambiare un valore nella stessa entità `Subscriber` e passarla a un'operazione `Insert`. In caso contrario, lo stato dell'entità dopo la modifica della proprietà avrebbe effetto sia per l'operazione Delete che per Insert.

Le operazioni di un batch devono trovarsi tutte nella stessa partizione. Poiché una modifica al nome della lista causa una modifica della chiave di partizione, non può essere eseguita in una transazione.

### Aggiungere le visualizzazioni MVC Subscriber

1.  In **Esplora soluzioni** creare una nuova cartella nella cartella *Views* del progetto MVC e denominarla *Subscriber*.

2.  Fare clic con il pulsante destro del mouse sulla nuova cartella *Views\\Subscriber* e scegliere **Aggiungi elemento esistente**.

3.  Passare alla cartella in cui è stata scaricata l'applicazione di esempio, selezionare tutti e cinque i file con estensione cshtml nella cartella *Views\\Subscriber* e fare clic su **Aggiungi**.

Nel file *Edit.cshtml* è incluso un campo nascosto per il valore `SubscriberGUID`, poiché si tratta di un campo nascosto e quindi non fornito automaticamente in un campo modulo.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### Testare l'applicazione

1.  Premere CTRL+F5 per eseguire il progetto, quindi fare clic su **Subscribers**.Subscriber

    ![Pagina Subscriber Index vuota][]

2.  Utilizzare la funzione **Create** per aggiungere alcune liste di distribuzione, quindi verificare il corretto funzionamento delle opzioni **Edit** e **Delete**.

    ![Pagina Subscriber Index con righe][]

## <a name="message"></a>Creare e testare il controller e le visualizzazioni Message

L'interfaccia utente Web **Message** viene usata dagli amministratori per creare, modificare e visualizzare informazioni sui messaggi pianificati per l'invio alle liste di distribuzione.

### Aggiungere la classe di entità Message alla cartella Models

1.  Nella cartella *Modelli* del progetto MVC aggiungere il file *Message.cs* dal progetto scaricato.

La classe di entità `Message` è usata per le righe della tabella `Message` contenenti informazioni su un messaggio pianificato per l'invio a una lista. Queste righe includono informazioni come la riga dell'oggetto, la lista a cui inviare un messaggio e la data pianificata per l'invio.

        public class Message : TableEntity
        {
            private DateTime? _scheduledDate;
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
            public DateTime? ScheduledDate 
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

Il valore `MessageRef` viene creato ottenendo il valore `Ticks` da `DateTime.Now`. In questo modo è possibile assicurarsi che, per impostazione predefinita, quando i messaggi sono visualizzati nell'interfaccia utente Web, saranno disposti nell'ordine in cui sono stati creati per una specifica data pianificata (`ScheduledDate` è la chiave di partizione). Sarebbe utilizzare un GUID per rendere univoche le righe del messaggio, ma in questo caso l'ordine di recupero predefinito sarebbe casuale.

Il costruttore predefinito imposta anche lo stato predefinito Pending per le nuove righe di `message`.

Per altre informazioni sulla struttura della tabella `Message`, vedere la [prima esercitazione della serie][].

### Aggiungere il controller MVC Message

1.  Nella cartella *Controllers* del progetto MVC aggiungere il file *MessageController.cs* dal progetto scaricato.

La maggior parte del codice di questo controller è simile a quello del controller `Subscriber`. La differenza è il codice inserito per utilizzare i BLOB. Per ogni messaggio, vengono caricati e archiviati in BLOB i contenuti HTML e di testo semplice sotto forma di file con estensione htm e txt.

I BLOB vengono archiviati in appositi contenitori. L'applicazione Azure Email Service archivia tutti i BLOB in un singolo contenitore denominato "azuremailblobcontainer", quindi il codice del costruttore del controller ottiene un riferimento a questo contenitore:

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

Per ogni file selezionato dall'utente per il caricamento, la visualizzazione MVC fornisce un oggetto `HttpPostedFile` contenente le relative informazioni. Quando l'utente crea un nuovo messaggio, sarà usato l'oggetto `HttpPostedFile` per salvare il file in un BLOB. Quando l'utente modifica un messaggio, può scegliere di caricare un file sostitutivo o di lasciare invariato il BLOB.

Il controller include un metodo chiamato dai metodi `HttpPost Create` e `HttpPost Edit` per salvare un BLOB:

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

Il metodo `HttpPost Create` salva i due BLOB e quindi aggiunge la riga della tabella `Message`. I BLOB vengono denominati concatenando il valore di `MessageRef` con l'estensione del nome file "htm" o "txt".

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
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
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

Il metodo `HttpGet Edit` verifica che lo stato del messaggio recuperato sia `Pending` in modo che l'utente non possa cambiare un messaggio dopo che il ruolo di lavoro B ha iniziato a elaborarlo. Codice analogo è presente nel metodo `HttpPost Edit` e nei metodi `Delete` e `DeleteConfirmed`.

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

Nel metodo `HttpPost Edit` il codice salva un nuovo BLOB solo se l'utente sceglie di caricare un nuovo file.

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

La modifica della data pianificata causa anche la modifica della chiave di partizione, quindi è necessario eliminare e inserire una riga. Questa operazione non può essere eseguita in una singola transazione, perché ha effetto su più partizioni.

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

Il metodo `HttpPost Delete` elimina i BLOB quando elimina la riga nella tabella:

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### Aggiungere le visualizzazioni MVC Message

1.  Nel progetto MVC creare una nuova cartella nella cartella *Views* con nome `Message`.

2.  Nella nuova cartella *Views\\Message* aggiungere i cinque file con estensione *cshtml* dal progetto scaricato.

Per il metodo `HttpPost Edit` sono necessarie una chiave di partizione e una chiave di riga, in modo che il codice nel file *Edit.cshtml* le possa specificare nei campi nascosti.

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
                

I campi nascosti non erano necessari nel controller `Subscriber` poiché (a) le proprietà `ListName` e `EmailAddress` del modello `Subscriber` aggiornano le proprietà `PartitionKey` e `RowKey` e (b) le proprietà `ListName` e `EmailAddress` erano incluse negli helper `EditorFor` nella visualizzazione Edit. Quando lo strumento di associazione di modelli MVC per il modello `Subscriber` aggiorna la proprietà `ListName`, la proprietà `PartitionKey` viene automaticamente aggiornata e quando lo strumento di associazione di modelli MVC aggiorna la proprietà `EmailAddress` nel modello `Subscriber`, la proprietà `RowKey` è aggiornata automaticamente. Nel modello `Message` i campi mappati alla chiave di partizione e alla chiave di riga non sono campi modificabili, quindi non sono impostati in questo modo.

Un campo nascosto viene incluso anche per la proprietà `MessageRef`. Si tratta dello stesso valore della chiave di partizione, ma viene incluso per consentire una maggiore chiarezza del codice nel metodo `HttpPost Edit`. Includendo il campo nascosto `MessageRef`, il codice del metodo `HttpPost Edit` può fare riferimento al valore di `MessageRef` tramite questo nome quando costruisce i nomi dei file per i BLOB.

La visualizzazione Message **Index** nel file *Index.cshtml* è diversa dalle altre visualizzazioni **Index**, poiché i collegamenti **Edit** e **Delete** sono visualizzati solo per i messaggi il cui stato è `Pending`:

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

In questo modo è possibile impedire all'utente di apportare modifiche a un messaggio dopo che il ruolo di lavoro A ha iniziato a elaborarlo.

### Testare l'applicazione

1.  Premere CTRL+F5 per eseguire il progetto, quindi fare clic su **Messages**.

    ![Pagina Message Index vuota][]

2.  Utilizzare la funzione **Create** per aggiungere alcune liste di distribuzione, quindi verificare il corretto funzionamento delle opzioni **Edit** e **Delete**.

    ![Pagina Subscriber Index con righe][2]

## <a name="unsubscribe"></a>Creare e testare il controller e le visualizzazioni Unsubscribe

A questo punto verrà implementata l'interfaccia utente per il processo di annullamento della sottoscrizione.

Questa esercitazione include solo istruzioni per la creazione del controller per il processo di annullamento della sottoscrizione, non per quello di sottoscrizione. Come illustrato nella [rima esercitazione][prima esercitazione della serie], l'interfaccia utente e il metodo di servizio per il processo di sottoscrizione sono inclusi nel download, ma sono stati esclusi dalle istruzioni dell'esercitazione poiché l'applicazione di esempio non implementa la sicurezza per il metodo di servizio. Per finalità di testing, è possibile usare le pagine dell'amministratore **Subscriber** per sottoscrivere gli indirizzi di posta elettronica alle liste.

### Aggiungere il modello di visualizzazione Unsubscribe alla cartella Models

1.  Nella cartella `Models` del progetto MVC aggiungere il file `UnsubscribeVM.cs` dal progetto scaricato.

Il modello di visualizzazione `UnsubscribeVM` è usato per passare i dati tra il controller `Unsubscribe` e la relativa visualizzazione.

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

I collegamenti Unsubscribe contengono `SubscriberGUID`. Questo valore è usato per ottenere l'indirizzo di posta elettronica, il nome della lista e la descrizione della lista dalla tabella `MailingList`. La visualizzazione contiene l'indirizzo di posta elettronica e la descrizione della lista da cui annullare la sottoscrizione, oltre a un pulsante **Confirm** che l'utente deve selezionare per completare il processo di annullamento.

### Aggiungere il controller Unsubscribe

1.  Nella cartella `Controllers` del progetto MVC aggiungere il file *UnsubscribeController.cs* dal progetto scaricato.

Il controller include un metodo `HttpGet Index` che visualizza la pagina iniziale per annullare la sottoscrizione e un metodo `HttpPost Index` che elabora il pulsante **Confirm** o **Cancel**.

Il metodo `HttpGet Index` usa il GUID e il nome della lista nella stringa della query per ottenere la riga della tabella `MailingList` relativa al sottoscrittore. Inserisce quindi tutte le informazioni necessarie nel modello di visualizzazione e mostra la pagina **Unsubscribe**. Imposta la proprietà `Confirmed` su Null in modo da indicare alla visualizzazione di mostrare la versione iniziale della pagina **Unsubscribe**.

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

Nota: SubscriberGUID non si trova nella chiave di partizione o nella chiave di riga, quindi si verificherà una riduzione delle prestazioni della query quando aumenteranno le dimensioni della partizione, ovvero il numero di messaggi di posta elettronica in una lista di distribuzione. Per informazioni sulle alternative disponibili per rendere più scalabile la query, vedere la [prima esercitazione della serie][].

Anche in questo caso il metodo `HttpPost Index` usa il GUID e il nome della lista per ottenere le informazioni sul sottoscrittore e popola le proprietà del modello di visualizzazione. Quindi, se il pulsante **Confirm** viene selezionato, elimina la riga del sottoscrittore dalla tabella `MailingList`. Se il pulsante **Confirm** viene selezionato, imposta anche la proprietà `Confirm` su `true`. In caso contrario, imposta la proprietà `Confirm` su `false`. Il valore della proprietà `Confirm` indica alla visualizzazione di mostrare la versione confermata o annullata della pagina **Unsubscribe**.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
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

### Creare le visualizzazioni MVC

1.  Nella cartella *Views* del progetto MVC creare una nuova cartella con nome *Unsubscribe*.

2.  Nella nuova cartella *Views\\Unsubscribe* aggiungere il file *Index.cshtml* dal progetto scaricato.

Nel file *Index.cshtml* la riga `Layout = null` specifica che il file \_Layout.cshtml non deve essere usato per visualizzare la pagina. La pagina **Unsubscribe** visualizza un'interfaccia utente molto semplice, senza le intestazioni e i piè di pagina usati per le pagine dell'amministratore.

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

Nel corpo della pagina la proprietà `Confirmed` determina ciò che sarà visualizzato nella pagina, **ossia i pulsanti** Confirm **e** Cancel se la proprietà è Null, il messaggio unsubscribe-confirmed se la proprietà è True oppure il messaggio unsubscribe-canceled se la proprietà è False.

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
                        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
                   </p>
                    <br />
                    <p>
                        <input type="submit" value="Confirm" name="action"/> 
                        &nbsp; &nbsp;
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

### Testare l'applicazione

1.  Premere CTRL+F5 per eseguire il progetto, quindi fare clic su **Subscribers**.

2.  Fare clic su **Create** e creare un nuovo sottoscrittore per una delle liste di distribuzione create durante i test precedenti.

    Lasciare aperta la finestra del browser nella pagina **Subscribers** **** Index.

3.  Aprire **Esplora server**, quindi selezionare il nodo **Azure / Archiviazione / (Sviluppo)**.

4.  Espandere **Tabelle**, fare clic con il pulsante destro del mouse sulla tabella **MailingList**, quindi scegliere **Visualizza tabella**.

5.  Fare doppio clic sulla riga del sottoscrittore aggiunta.

6.  Nella finestra di dialogo **Modifica entità** selezionare e copiare il valore di `SubscriberGUID`.

    ![Azure Storage Explorer][]

7.  Tornare nella finestra del browser. Nella barra degli indirizzi del browser sostituire "Subscriber" nell'URL con "unsubscribe?ID=[valoreguid]&listName=[nomelista]", dove [valoreguid] corrisponde al GUID copiato da Esplora archivi Azure e [nomelista] è il nome della lista di distribuzione, Ad esempio:

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    Verrà visualizzata la versione della pagina **Unsubscribe** in cui si chiede conferma:

    ![Unsubscribe page][]

8.  Fare clic su **Confirm**. Verrà visualizzata la conferma che la sottoscrizione dell'indirizzo di posta elettronica è stata annullata.

    ![Unsubscribe confirmed page][]

9.  Tornare nella pagina **Subscribers** **Index** per verificare che la riga del sottoscrittore non sia più presente.

## <a name="nextsteps"></a>Passaggi successivi

Come illustrato nella [prima esercitazione della serie][], questa esercitazione non mostra i componenti del processo di sottoscrizione, poiché il metodo del servizio API Web ASP.NET non implementa la sicurezza basata su segreto condiviso. Tuttavia, la restrizione IP configurata nella seconda esercitazione protegge anche il metodo del servizio ed è possibile sottoscrivere le funzionalità copiando i file seguenti dal progetto scaricato.

Per il metodo del servizio API Web ASP.NET:

-   Controllers\\SubscribeAPI.cs

Per la pagina Web aperta dai sottoscrittori quando fanno clic sul collegamento **Confirm** nel messaggio di posta elettronica generato dal metodo del servizio:

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

Nella [successiva esercitazione][] verrà configurato e programmato il ruolo di lavoro A, ossia quello che pianifica i messaggi di posta elettronica.

Per collegamenti a risorse aggiuntive per l'utilizzo di tabelle, code e BLOB di Archiviazione di Azure, vedere l'[ultima esercitazione di questa serie][].

<div>

<a href="/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">Esercitazione 4</a>

</div>

  [prima esercitazione della serie]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Creare la soluzione di Visual Studio]: #cloudproject
  [Aggiornare la libreria client di archiviazione del pacchetto NuGet]: #updatescl
  [Configurare i progetti per l'uso dell'emulatore di archiviazione]: #configurestorage
  [Configurare la traccia e gestire i riavvii]: #tracing
  [Aggiungere codice per creare tabelle, code e contenitori BLOB nel metodo Application\_Start]: #createifnotexists
  [Creare e testare la lista di distribuzione]: #mailinglist
  [Creare e testare il controller e le visualizzazioni Subscriber]: #subscriber
  [Creare e testare il controller e le visualizzazioni Message]: #message
  [Creare e testare il controller e le visualizzazioni Unsubscribe]: #unsubscribe
  [Passaggi successivi]: #nextsteps
  [Menu Nuovo progetto]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [New Project dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [Finestra di dialogo New Azure Cloud Project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [Finestra di dialogo New Azure Cloud Project - ridenominazione del ruolo Web]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [Finestra di dialogo New Azure Cloud Project - aggiunta di un ruolo di lavoro]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [No authentication]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [soluzione completa]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [home page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [Emulatore di calcolo nell'area di notifica]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [Manage NuGet Packages for Solution nel menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [Pacchetto Archiviazione di Azure nella finestra di dialogo Manage NuGet Packages]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [Selezione di entrambi i progetti nella finestra di dialogo Seleziona progetti]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [Proprietà del ruolo Web]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [Pulsante destro del mouse su Proprietà]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [Messaggio di errore su file di configurazione e definizione del servizio non validi]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [seconda esercitazione]: /en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [trasferimento su richiesta]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [approfondimento sulle tabelle della libreria client 2.0 di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Informazioni sul modello di dati del servizio tabelle]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx
  [Carattere % in PartitionKey o RowKey]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [Utilizzare il supporto asincrono di .NET 4.5 per evitare chiamate di blocco]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [sfruttare al meglio le tabelle di Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [prevedere i token di continuazione nelle tabelle di Azure]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [registrazione di archiviazione di Azure e all'uso dei log per tenere traccia delle richieste di archiviazione]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Pagina MailingList Index vuota]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [Pagina MailingList Index con righe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [Pagina Subscriber Index vuota]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [Pagina Subscriber Index con righe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [Pagina Message Index vuota]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Azure Storage Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [Unsubscribe page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [Unsubscribe confirmed page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [successiva esercitazione]: /en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [ultima esercitazione di questa serie]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
