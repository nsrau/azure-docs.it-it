<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows phone application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Gestione dei conflitti relativi alla sincronizzazione offline dei dati in Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone" class="current">Windows Phone</a>
</div>

In questo argomento viene descritto come sincronizzare e gestire i conflitti quando si usano le funzionalità offline di Servizi mobili di Azure. In questa esercitazione si scaricherà un'app che supporta sia i dati offline che online, si integrerà il servizio mobile con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare e aggiornare il database durante l'esecuzione dell'app.

Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione ai dati offline][]. Prima di iniziare questa esercitazione è necessario completare [Introduzione ai dati offline in Servizi mobili][Introduzione ai dati offline].

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app per Windows Phone][]
2.  [Aggiunta di una colonna di scadenza per il database][]

	-   [Aggiornamento del database per i servizi mobili back-end .NET][]
	-   [Aggiornamento del database per i servizi mobili JavaScript][]

1.  [Test dell'app in un servizio mobile][]
2.  [Aggiornamento manuale dei dati nel back-end per creare un conflitto][]

Per completare questa esercitazione, è necessario disporre di Visual Studio 2012 e di [Windows Phone 8 SDK][].

## <a name="download-app"></a>Download del progetto di esempio

Questa esercitazione è basata sull'[esempio di codice per la gestione dei conflitti][], che è un progetto Windows Phone 8 per Visual Studio 2012.
L'interfaccia utente di questa app è simile a quella dell'app dell'esercitazione [Introduzione ai dati offline][], ma in questo caso è presente una nuova colonna di data per ciascun TodoItem.

![][]

1.  Scaricare la versione Windows Phone dell'[esempio di codice per la gestione dei conflitti][].

2.  Installare [SQLite for Windows Phone 8][] se non è ancora installato.

3.  In Visual Studio 2012 aprire il progetto scaricato. Aggiungere un riferimento a **SQLite for Windows Phone** in **Windows Phone** \> **Estensioni**.

4.  In Visual Studio 2012 premere **F5** per compilare ed eseguire l'app nel debugger.

5.  Nell'app digitare del testo per alcuni nuovi elementi todo e digitare **Salva** per salvare ognuno di essi. È anche possibile modificare la data di scadenza degli elementi todo aggiunti.

Si noti che l'app non è ancora connessa ad alcun servizio mobile, pertanto i pulsanti **Push** e **Pull** genereranno eccezioni.

## <a name="add-column"></a>Aggiunta di una colonna al modello di dati

In questa sezione verrà aggiornato il database per il servizio mobile in modo da includere una tabella TodoItem con una colonna di data di scadenza. L'app consente di modificare la data di scadenza di un elemento in fase di esecuzione in modo da poter generare conflitti di sincronizzazione in una sezione successiva di questa esercitazione.

La classe `TodoItem` dell'esempio è definita in MainPage.xaml.cs. Notare che la classe presenta l'attributo seguente che verrà usato per le operazioni di sincronizzazione rispetto a tale tabella.

        [DataTable("TodoWithDate")]

Aggiornare il database in modo da includere questa tabella.

### <a name="dotnet-backend"></a>Aggiornamento del database per i servizi mobili back-end .NET

Se si utilizza back-end .NET per il servizio mobile, attenersi alla procedura seguente per aggiornare lo schema per il database.

1.  Aprire il servizio mobile back-end .NET in Visual Studio.
2.  In Esplora soluzioni di Visual Studio, nel progetto di servizio, espandere la cartella **Modelli** e aprire ToDoItem.cs. Aggiungere la proprietà `DueDate` come segue.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }

3.  In Esplora soluzioni di Visual Studio espandere la cartella **App\_Start** e aprire il file WebApiConfig.cs.

    Nel file WebApiConfig.cs notare che la classe predefinita dell'inizializzatore del database deriva dalla classe `DropCreateDatabaseIfModelChanges`. Ciò significa che se viene apportata qualsiasi modifica al modello, la tabella verrà eliminata e ricreata per gestire il nuovo modello. Pertanto, i dati della tabella andranno persi e verrà eseguito il reseeding della tabella. Modificare il metodo Seed dell'inizializzatore del database in modo che la funzione di inizializzazione `Seed()` sia la seguente per inizializzare la nuova colonna DueDate. Salvare il file WebApiConfig.cs.

    > [WACOM.NOTE] Quando si utilizza l'inizializzatore del database predefinito, Entity Framework elimina e crea nuovamente il database ogni volta che rileva una modifica nel modello di dati nella definizione del modello Code First. Per apportare modifiche al modello di dati e conservare i dati esistenti nel database, è necessario utilizzare Migrazioni Code First. Per altre informazioni vedere [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati][].

        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  In Esplora soluzioni di Visual Studio espandere la cartella **Controller** e aprire ToDoItemController.cs. Rinominare la classe `TodoItemController` in `TodoWithDateController`. L'endpoint REST per le operazioni di tabella verrà modificato.

        public class TodoWithDateController : TableController<TodoItem>

5.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio mobile back-end .NET, quindi scegliere **Pubblica** per pubblicare le modifiche.

### <a name="javascript-backend"></a>Aggiornamento del database per i servizi mobili back-end JavaScript

Per i servizi mobili back-end JavaScript verrà aggiunta una nuova tabella denominata **TodoWithDate**. Per aggiungere la tabella **TodoWithDate** per i servizi mobili back-end JavaScript, attenersi alla procedura seguente.

1.  Accedere al [portale di gestione di Azure][].

2.  Passare alla scheda **Data** del servizio mobile.

3.  Fare clic su **Create** in fondo alla pagina e creare una nuova tabella denominata **TodoWithDate**.

## <a name="test-app"></a>Test dell'app su Servizi mobili

Ora verrà eseguito il test dell'app su Servizi mobili.

1.  Nel portale di gestione di Azure individuare l'applicazione del servizio mobile facendo clic su **Gestisci chiavi** sulla barra dei comandi della scheda **Dashboard**. Copiare la **chiave applicazione**.

2.  In Esplora soluzioni di Visual Studio aprire il file App.xaml.cs nel progetto di esempio client. Modificare l'inizializzazione di **MobileServiceClient** per utilizzare l'URL del servizio mobile e la chiave applicazione:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3.  In Visual Studio premere **F5** per compilare ed eseguire l'app.

4.  Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **Salva** per salvare alcuni nuovi elementi todo. I dati verranno salvati nella tabella di sincronizzazione locale, ma non nel server.

    ![][]

5.  Per visualizzare lo stato corrente del database, accedere al [portale di gestione di Azure][], fare clic su **Servizi Mobili**, quindi scegliere il servizio mobile.

	-   Se si utilizza il back-end JavaScript per il servizio mobile, fare clic sulla scheda **Data**, quindi fare clic sulla tabella **TodoWithDate**. Fare clic su **Browse** per vedere che la tabella è ancora vuota poiché non è stato effettuato il push delle modifiche dall'app al server.
	
		![][1]
	
	-   Se si utilizza il back-end .NET per il servizio mobile, fare clic sulla scheda **Configure**, quindi scegliere il database SQL. Fare clic su **Gestisci** in fondo alla schermata per accedere al portale di gestione di SQL Azure per visualizzare il database eseguendo una query SQL simile alla seguente.
	
			SELECT * FROM todolist.todowithdate
	
		![][2]

1.  Nell'app fare clic su **Push**.

2.  Nel portale di gestione fare clic sulla scheda **Refresh** nella tabella **TodoItem**. I dati immessi nell'app verranno visualizzati.

    ![][1]

3.  Lasciare **Emulator WVGA 512MB** in esecuzione per la sezione successiva, in cui si eseguirà l'app in due emulatori per generare un conflitto.

## <a name="handle-conflict"></a>Aggiornamento dei dati nel back-end per creare un conflitto

In uno scenario reale un conflitto di sincronizzazione si verifica quando un'app effettua il push di aggiornamenti a un record del database, quindi un'altra app tenta di effettuare il push di una modifica allo stesso record usando un campo di versione non aggiornato di tale record. Se un'istanza dell'app tenta di aggiornare lo stesso record, senza effettuare il pull del record aggiornato, si verificherà un conflitto, che verrà rilevato come `MobileServicePreconditionFailedException` nell'app.

In questa sezione si eseguiranno contemporaneamente due istanze dell'app per generare un conflitto.

1.  Se **Emulator WVGA 512MB** non è in esecuzione, premere **CTRL+F5** per riavviarlo.

2.  In Visual Studio, modificare il dispositivo di output in **Emulator WVGA** ed eseguire una seconda istanza dell'app nel nuovo emulatore premendo **F5**.

    ![][2]

3.  Nella seconda istanza dell'app fare clic su **Pull** per sincronizzare l'archivio locale con il database del servizio mobile. Entrambe le istanze dell'app dovrebbero avere gli stessi dati.

    ![][3]

4.  Nella seconda istanza dell'app fare clic sulla casella di controllo per completare uno degli elementi e quindi fare clic su **Push** per effettuare il push delle modifiche al database remoto. Nella schermata seguente l'elemento **Pick up James** è stato completato, per indicare che James è già stato prelevato. La prima istanza dell'app ora contiene un record obsoleto.

    ![][4]

5.  Nella prima istanza dell'app provare a cambiare la data del record obsoleto e fare clic su **Push** per tentare di aggiornare il database remoto con il record obsoleto. Nella schermata di seguito si tenta di pianificare il prelievo di James per il **5/10/2014**.

    ![][5]

6.  Quando si fa clic sul pulsante **Push** per eseguire il commit del cambiamento di data, verrà visualizzata una finestra di dialogo che segnala che è stato rilevato un conflitto. Verrà chiesto di risolvere il conflitto. Per risolvere il conflitto, scegliere una delle opzioni.

    Nello scenario che segue James è già stato prelevato, pertanto non è necessario pianificare di andare a prenderlo il **5/10/2014**. Sarebbe necessario selezionare l'opzione **Use server version** in modo che il record presente nella prima istanza dell'app fosse aggiornato con il record del server.

    ![][6]

## Revisione del codice per la gestione dei conflitti di sincronizzazione

Per poter configurare la funzionalità offline di rilevamento dei conflitti, è necessario includere una colonna relativa alla versione sia nel database locale che nell'oggetto di trasferimento dei dati. La classe `TodoItem` include il membro seguente:

        [Version]
        public string Version { get; set; }

La colonna `__version` è specificata anche nel database locale configurato nel metodo `OnNavigatedTo()`.

Per gestire i conflitti di sincronizzazione offline nel codice, creare una classe che implementi `IMobileServiceSyncHandler`. Passare un oggetto di questo tipo nella chiamata a `InitializeAsync`:

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La classe `SyncHandler` in **MainPage.xaml.cs** implementa `IMobileServiceSyncHandler`. Il metodo `ExecuteTableOperationAsync` viene chiamato quando ciascuna operazione push viene inviata al server. Se viene generata un'eccezione del tipo `MobileServicePreconditionFailedException`, è presente un conflitto tra la versione locale e la versione remota di un elemento.

Per risolvere i conflitti a favore dell'elemento locale, è sufficiente riprovare a eseguire l'operazione. Dopo che un conflitto si è verificato, la versione dell'elemento locale verrà aggiornata in base alla versione del server, quindi la ripetizione dell'operazione sovrascriverà le modifiche del server con le modifiche locali:

    await operation.ExecuteAsync(); 

Per risolvere i conflitti a favore dell'elemento del server, è sufficiente tornare da `ExecuteTableOperationAsync`. La versione locale dell'oggetto verrà eliminata e sostituita con il valore del server.

Per interrompere l'operazione push (conservando però le modifiche in coda), usare il metodo `AbortPush()`:

    operation.AbortPush();

L'operazione push corrente verrà interrotta, ma verranno mantenute tutte le modifiche in sospeso, tra cui l'operazione corrente se `AbortPush` è chiamato da `ExecuteTableOperationAsync`. Alla prossima chiamata di `PushAsync()` queste modifiche verranno inviate al server.

Quando un push viene annullato, `PushAsync` genererà un'eccezione `MobileServicePushFailedException` e la proprietà dell'eccezione `PushResult.Status` avrà il valore `MobileServicePushStatus.CancelledByOperation`.

<!-- Anchors. --> 
<!-- Images --> 
<!-- URLs -->

  [Windows Store C\#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data "Windows Store C#"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data "Windows Phone"
  [Introduzione ai dati offline]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Download del progetto dell'app per Windows Phone]: #download-app
  [Aggiunta di una colonna di scadenza per il database]: #add-column
  [Aggiornamento del database per i servizi mobili back-end .NET]: #dotnet-backend
  [Aggiornamento del database per i servizi mobili JavaScript]: #javascript-backend
  [Test dell'app in un servizio mobile]: #test-app
  [Aggiornamento manuale dei dati nel back-end per creare un conflitto]: #handle-conflict
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
  [esempio di codice per la gestione dei conflitti]: http://go.microsoft.com/fwlink/?LinkId=398257
  []: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
  [SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
  [Come utilizzare le Migrazioni Code First per aggiornare il modello di dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
  [2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
  [3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
  [4]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png
  [5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
  [6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
