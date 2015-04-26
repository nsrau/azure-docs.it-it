<properties 
	pageTitle="Introduzione ai dati (Xamarin.iOS) - Servizi mobili di Azure" 
	description="Informazioni su come archiviare e accedere ai dati dall'app Servizi mobili di Azure per Xamarin.iOS." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>

# Aggiunta di Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app di Xamarin.iOS. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

> [AZURE.NOTE] In questa esercitazione viene descritto come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app di Xamarin.iOS e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se questa è la prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili](/it-it/develop/mobile/tutorials/get-started-xamarin-ios).

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Download del progetto dell'app di Xamarin.iOS][GitHub] 
2. [Creazione del servizio mobile]
3. [Aggiunta di una tabella dati per l'archiviazione]
4. [Aggiornamento dell'app per l'uso di Servizi mobili]
5. [Test dell'app in Servizi mobili]

Per completare questa esercitazione, è necessario disporre del [componente Servizi mobili di Azure], [XCode 5.0][Installare Xcode], [Xamarin.iOS] e di iOS 5.0 o versione successiva.

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F"%20target="_blank).

<h2><a name="download-app"></a>Download del progetto GetStartedWithData</h2>

Questa esercitazione è basata sull'[app GetStartedWithData][GitHub], che è un'app per Xamarin.iOS. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per Xamarin.iOS, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale. 

1. Scaricare l'[app GetStartedWithData][GitHub]. 

2. In Xamarin Studio aprire il progetto scaricato ed esaminare la classe **TodoService**.

   	Si noti che sono presenti più commenti **// TODO** che specificano le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

3. Nel menu **Run** scegliere **Start Without Debugging** per avviare l'app.

4. Nell'app digitare un testo nella casella e quindi fare clic sul pulsante **+**.

   	![][0]  

   	Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

<h2><a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile</h2>

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella nell'istanza di database SQL associata.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Fare clic sulla scheda **Dati** e quindi su **+Crea**.

   	![][5]

   	Verrà visualizzata la finestra di dialogo **Crea nuova tabella**.

3. In **Nome tabella** digitare _TodoItem_ e quindi fare clic sul pulsante con il segno di spunta.

  	![][6]

  	Verrà creata una nuova tabella di archiviazione **TodoItem** con le autorizzazioni predefinite impostate. Questo significa che qualsiasi utente dell'app potrà accedere alla tabella e modificare i dati. 

    > [AZURE.NOTE] Nella Guida introduttiva di Servizi mobili viene usato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo è possibile evitare conflitti di dati quando più servizi mobili usano lo stesso database.

4. Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

5. Fare clic sulla scheda **Colonne** e verificare che sia presente una sola colonna **id**, che viene creata automaticamente.

	  Questo è il requisito minimo per le tabelle in Servizi mobili. 

    > [AZURE.NOTE] Se nel servizio mobile è abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.

È ora possibile usare il nuovo servizio mobile come archivio dati per l'app.

<h2><a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati</h2>

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale. 

1. Se **Servizi mobili di Azure** non è già elencato nella cartella dei componenti, per accedervi fare clic con il pulsante destro del mouse su **Componenti**, scegliere **Altri componenti** e quindi cercare **Servizi mobili di Azure**.

2. Nella visualizzazione Solution di Xamarin Studio aprire la classe **TodoService** e rimuovere i simboli di commento dall'istruzione `using` seguente:

        using Microsoft.WindowsAzure.MobileServices;
               
3. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

4. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **URL sito**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

   	![][8]

5. Nella classe **Costanti** rimuovere i simboli di commento dalle costanti seguenti:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. Sostituire **AppUrl** e **AppKey** nelle costanti precedenti con i valori recuperati nel portale di gestione.

7. Nella classe **TodoService** rimuovere i simboli di commento dalla riga di codice seguente:

        private MobileServiceClient client;

   	Verrà creata una proprietà che rappresenta il client di Servizi mobili che si connette al servizio

8. Nella classe **TodoService** rimuovere i simboli di commento dalla riga di codice seguente:

        private IMobileServiceTable<TodoItem> todoTable;  

   	Verrà creata una rappresentazione della proprietà per la tabella di Servizi mobili.

9. Rimuovere i simboli di commento dalle righe seguenti nel costruttore **TodoService**:

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
		todoTable = client.GetTable<TodoItem>(); 

    Verranno create un'istanza del client di Servizi mobili e un'istanza della tabella TodoItem.

10. Rimuovere i simboli di commento dalle righe seguenti nel metodo **RefreshDataAsync** in **TodoService**

		// TODO:: Uncomment these lines to use Mobile Services
    try 
        {
			// This code refreshes the entries in the list view by querying the TodoItems table.
			// The query excludes completed TodoItems
			Items = await todoTable
				.Where (todoItem => todoItem.Complete == false).ToListAsync();
		} 
        catch (MobileServiceInvalidOperationException e) 
        {
			Console.Error.WriteLine (@"ERROR {0}", e.Message);
			return null;
		}
        
    Verrà creata una query che restituisce tutte le attività non ancora completate.

11. Individuare il metodo **InsertTodoItemAsync** e rimuovere i simboli di commento dalla riga seguente:

		await todoTable.InsertAsync(todoItem);
		
    Il codice invia una richiesta insert al servizio mobile.

13. Individuare il metodo **CompleteItemAsync** e rimuovere i simboli di commento dalla riga seguente:

		await todoTable.UpdateAsync(item);
		
   	Il codice rimuove gli elementi TodoItems dopo che sono stati contrassegnati come completati. 

Una volta aggiornata l'app per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

<h2><a name="test-app"></a>Testare l'app nel nuovo servizio mobile</h2>

1. In Xamarin Studio selezionare un emulatore o un dispositivo in cui eseguire la distribuzione da una delle caselle combinate principali, quindi passare al menu **Run** e selezionare **Start WithoutDebugging** per avviare l'app.

   	Verrà eseguito il client di Servizi mobili di Azure, compilato con Xamarin.iOS, e verrà eseguita una query sugli elementi dal servizio mobile.

2. Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Xamarin.iOS è terminata.

## Scaricare l'esempio completato
Scaricare il [progetto di esempio completato]. Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure. 

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di utilizzare dati in Servizi mobili. 

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Uso di script per la convalida e la modifica di dati]
  <br/>Informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Uso del paging per ridefinire le query]
  <br/>Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per iOS seguenti:

* [Introduzione all'autenticazione] 
	<br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

<!-- Anchors. -->

[Download dell'app di Windows Store]: #download-app
[Creazione del servizio mobile]: #create-service
[Aggiunta di una tabella dati per l'archiviazione]: #add-table
[Aggiornamento dell'app per l'uso di Servizi mobili]: #update-app
[Test dell'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[Uso di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Uso del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Archivio GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/

[progetto di esempio completato]: http://go.microsoft.com/fwlink/p/?LinkId=331302



<!--HONumber=42-->
