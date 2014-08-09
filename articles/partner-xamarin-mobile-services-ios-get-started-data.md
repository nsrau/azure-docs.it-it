<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure Xamarin.iOS data, Azure mobile services data, " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="" />

Introduzione ai dati in Servizi mobili
======================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app per Xamarin.iOS. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

**Nota**

In questa esercitazione viene descritto come è possibile utilizzare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per Xamarin.iOS e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-xamarin-ios).

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app per Xamarin.iOS](http://go.microsoft.com/fwlink/p/?LinkId=331302)
2.  [Creazione del servizio mobile](#create-service)
3.  [Aggiunta di una tabella dati per l'archiviazione](#add-table)
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili](#update-app)
5.  [Test dell'app in Servizi mobili](#test-app)

Per completare questa esercitazione, è necessario disporre del [componente Servizi mobili di Azure](http://components.xamarin.com/view/azure-mobile-services/), [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532), [Xamarin.iOS] e di iOS 5.0 o versione successiva.

**Nota**

Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F).

Download del progettoDownload del progetto GetStartedWithData
-------------------------------------------------------------

Questa esercitazione è basata sull'[app GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=331302) che è un'app per Xamarin.iOs. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per Xamarin.iOS, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare l'[app GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=331302).

2.  In Xamarin Studio aprire il progetto scaricato ed esaminare la classe **TodoService**.

    Si noti che sono presenti svariati commenti `**// TODO** in cui vengono specificate le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

3.  Nel menu **Run** fare clic su **Start Without Debugging** per avviare l'app.

4.  Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

    ![][0]  

    Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

Creazione del servizio mobileCreazione di un nuovo servizio mobile nel portale di gestione
------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Aggiunta di una nuova tabellaAggiunta di una nuova tabella al servizio mobile
-----------------------------------------------------------------------------

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella nell'istanza di database SQL associata.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Fare clic sulla scheda **Data** e quindi su **+Create**.

    ![][5]

    Verrà visualizzata la finestra di dialogo **Create new table**.

3.  In **Table name** digitare *TodoItem*, quindi fare clic sul segno di spunta.

	![](./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png) 
	Verrà creata una nuova tabella di archiviazione **TodoItem** con le autorizzazioni predefinite impostate. Questo significa che qualsiasi utente dell'app potrà accedere alla tabella e modificare i dati.

    <div class="dev-callout"> 
    <b>Nota</b> 
    <p>Nella Guida introduttiva di Servizi mobili viene utilizzato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo è possibile evitare conflitti di dati quando più servizi mobili utilizzano lo stesso database.</p> 
    </div>

1.  Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

2.  Fare clic sulla scheda **Columns** e verificare che sia presente una sola colonna **id**, che viene creata automaticamente.

    Questo è il requisito minimo per le tabelle in Servizi mobili.

    **Nota**

    Se nel servizio mobile è abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.

È ora possibile utilizzare il nuovo servizio mobile come archivio dati per l'app.

Aggiornamento dell'appAggiornamento dell'app per l'utilizzo del servizio mobile per l'accesso ai dati
-----------------------------------------------------------------------------------------------------

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  Se **Servizi mobili di Azure** non è già elencato nella cartella dei componenti, per accedervi fare clic con il pulsante destro del mouse su **Components**, scegliere **Get More Components** e quindi cercare **Servizi mobili di Azure**.

2.  Nella visualizzazione Solution di Xamarin Studio aprire la classe **TodoService** e rimuovere i simboli di commento dall'istruzione `using` seguente:

         using Microsoft.WindowsAzure.MobileServices;

3.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

4.  Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

    ![][8]

5.  Nella classe **Constants** rimuovere i simboli di commento dalle costanti seguenti:

         public const string ApplicationURL = @"AppUrl";
         public const string ApplicationKey = @"AppKey";

6.  Sostituire **AppUrl** e **AppKey** nelle costanti precedenti con i valori recuperati dal portale di gestione.

7.  Nella classe **TodoService** rimuovere i simboli di commento dalla riga di codice seguente:

         private MobileServiceClient client;

    Verrà creata una proprietà che rappresenta il client di Servizi mobili che si connette al servizio

8.  Nella classe **TodoService** rimuovere i simboli di commento dalla riga di codice seguente:

         private IMobileServiceTable<TodoItem> todoTable;  

    Verrà creata una rappresentazione della proprietà per la tabella di Servizi mobili.

9.  Rimuovere i simboli di commento dalle righe seguenti nel costruttore **TodoService**:

         // TODO:: Uncomment these lines to use Mobile Services
         client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
         todoTable = client.GetTable<TodoItem>(); 

    Verranno create un'istanza del client di Servizi mobili e un'istanza della tabella TodoItem.

10. Rimuovere i simboli di commento dalle righe seguenti nel metodo **RefreshDataAsync** in **TodoService**

        // TODO:: Uncomment these lines to use Mobile Services

    	try { // This code refreshes the entries in the list view by querying the TodoItems table. // The query excludes completed TodoItems Items = await todoTable .Where (todoItem =\> todoItem.Complete == false).ToListAsync(); } catch (MobileServiceInvalidOperationException e) { Console.Error.WriteLine (@"ERROR {0}", e.Message); return null; }

    Verrà creata una query che restituisce tutte le attività non ancora completate.

11. Individuare il metodo **InsertTodoItemAsync** e rimuovere i simboli di commento dalla riga seguente:

        await todoTable.InsertAsync(todoItem);

    Il codice invia una richiesta insert al servizio mobile.

12. Individuare il metodo **CompleteItemAsync** e rimuovere i simboli di commento dalla riga seguente:

        await todoTable.UpdateAsync(item);

	Il codice rimuove gli elementi TodoItems dopo che sono stati contrassegnati come completati.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

Test dell'appTest dell'app nel nuovo servizio mobile
----------------------------------------------------

1.  In Xamarin Studio selezionare un emulatore o un dispositivo in cui eseguire la distribuzione da una delle caselle combinate principali, quindi passare al menu **Run** e selezionare **Start WithoutDebugging** per avviare l'app.

    Verrà eseguito il client di Servizi mobili di Azure, compilato con Xamarin.iOS, e verrà eseguita una query sugli elementi dal servizio mobile.

2.  Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    ![][9]
          
    Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Xamarin.iOS è terminata.

Download dell'esempio completato
--------------------------------

Scaricare il [progetto di esempio completato](http://go.microsoft.com/fwlink/p/?LinkId=331302). Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

Passaggi successivi
-------------------

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di utilizzare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios) <br/>
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios) <br/>
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per iOS seguenti:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios) <br/>
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios) <br/>
    Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
