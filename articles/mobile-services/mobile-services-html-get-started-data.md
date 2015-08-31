<properties 
	pageTitle="Aggiungere Servizi mobili a un'app esistente (HTML 5) | Microsoft Azure" 
	description="Informazioni su come iniziare a usare Servizi mobili in un'app HTML esistente." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="08/16/2015" 
	ms.author="glenga"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Panoramica 

Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app HTML. Questa esercitazione consente di scaricare un'app che archivia dati in memoria, creare un nuovo servizio mobile, integrarlo con l'app e quindi accedere al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

>[AZURE.NOTE]Questa esercitazione è stata ideata per illustrare come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app HTML e ripropone quindi molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili].

> [AZURE.IMPORTANT]Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2documentation%2Farticles%2Fmobile-services-html-get-started-data).

###Requisiti aggiuntivi

È possibile ospitare l'app GetStartedWithData in qualsiasi server Web. Per semplicità, tuttavia, sono stati forniti script che consentono di eseguire l'app in `http://localhost:8000`.
 
+ Per completare questa esercitazione e utilizzare localhost, è necessario che nel computer locale sia in esecuzione uno dei server Web seguenti:

	+  **In Windows**: IIS Express. IIS Express viene installato tramite l'[Installazione guidata piattaforma Web Microsoft].   
	+  **In MacOS X**: Python, che dovrebbe essere già installato.
	+  **In Linux**: Python. È necessario installare la [versione più recente di Python]. 
	
	È possibile utilizzare qualsiasi server Web per ospitare l'app, tuttavia quelli indicati sono i server Web supportati dagli script scaricati.

+ Un Web browser che supporta HTML5.

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData] che è un'app HTML5. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1. [Download dei file di progetto dell'app HTML][GetStartedWithData app].

2. In un editor HTML aprire il progetto scaricato ed esaminare il file app.js.

   	Si noti che gli elementi aggiunti vengono archiviati in un oggetto **Array** in memoria (**staticItems**). Aggiornare la pagina. I dati scompariranno dalla visualizzazione perché non sono persistenti.

3. Avviare uno dei file di comando seguenti dalla sottocartella **server**.

	+ **launch-windows** (computer Windows) 
	+ **launch-mac.command** (computer Mac OS X)
	+ **launch-linux.sh** (computer Linux)

	> [AZURE.NOTE]In un computer Windows digitare `R` quando PowerShell chiede di confermare l'esecuzione dello script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script
	
	Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

4. Aprire l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in un Web browser per avviare l'app.

5. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, in **Enter new task** e quindi fare clic su **Add**.

   	![][0]

   	Si noti che il testo salvato viene aggiunto alla matrice **staticItems** la pagina viene aggiornata per visualizzare il nuovo elemento.

##<a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella nell'istanza di database SQL associata.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Fare clic sulla scheda **Dati** e quindi su **+Crea**.

   	Verrà visualizzata la finestra di dialogo **Crea nuova tabella**.

3. In **Nome tabella** digitare _TodoItem_, quindi fare clic sul segno di spunta.

  	Verrà creata una nuova tabella di archiviazione, **TodoItem**, con le autorizzazioni predefinite impostate. Questo significa che chiunque disponga della chiave dell'applicazione, che viene distribuita con l'app, potrà accedere alla tabella e modificare i dati. La tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo è possibile evitare conflitti di dati quando più servizi mobili utilizzano lo stesso database.

4. Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

	>[AZURE.NOTE]Le nuove tabelle create includono le colonne Id, \_\_createdAt, \_\_updatedAt, e \_\_version. Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per ulteriori informazioni, vedere [Schema dinamico](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

6. Nella scheda **Configura** verificare che `localhost` sia già indicato nell'elenco **Consentire le richieste da nomi host** in **Condivisione risorse tra le origini (CORS)**. In caso contrario, digitare `localhost` nel campo **Nome host** e quindi fare clic su **Salva**.


	> [AZURE.IMPORTANT]Se si distribuisce l'app di guida introduttiva in un server Web diverso da localhost, è necessario aggiungere il nome host del server Web all'elenco **Consentire le richieste da nomi host**. Per altre informazioni, vedere [Utilizzare la condivisione di risorse tra origini](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

È ora possibile utilizzare il nuovo servizio mobile come archivio dati per l'app.

##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

3. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

4. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **URL servizio mobile**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

1. Nell'editor Web aprire il file di progetto index.html e aggiungere il codice seguente ai riferimenti allo script per la pagina:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

5. Nell'editor aprire il file app.js, rimuovere i simboli di commento dal codice seguente che definisce la variabile **MobileServiceClient**, infine specificare l'URL e la chiave dell'applicazione indicati nel servizio mobile nel costruttore **MobileServiceClient**, senza modificare l'ordine.

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	Verrà creata una nuova istanza di MobileServiceClient utilizzata per accedere al servizio mobile.

6. Impostare come commento le righe di codice seguenti:

		var itemCount = 0;
		var staticItems = [];

	I dati verranno archiviati nel servizio mobile e non in una matrice in memoria.

6. Rimuovere quindi i simboli di commento dalla riga di codice seguente:

        todoItemTable = client.getTable('todoitem');

   	Questo codice crea un oggetto proxy (**todoItemTable**) per l'elemento **TodoItem** del database SQL.

7. Sostituire il gestore di eventi **$('\#add-item').submit** con il codice seguente:

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	Questo codice consente di inserire un nuovo elemento nella tabella.

8. Sostituire il metodo **refreshTodoItems** con il codice seguente:

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}
	   

   Verrà inviata al servizio mobile una query che restituisce tutti gli elementi. I risultati vengono scorsi e i dati vengono visualizzati nella pagina.

9. Sostituire i gestori di eventi **$(document.body).on('change', '.item-text')** e **$(document.body).on('change', '.item-complete')** con il codice seguente:
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	Verrà inviato un aggiornamento dell'elemento al servizio mobile quando viene modificato il testo o selezionata la casella.

10. Sostituire il gestore di eventi **$(document.body).on('click', '.item-delete')** con il codice seguente:

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	Verrà inviata una richiesta di eliminazione al servizio mobile quando si fa clic sul pulsante **Delete**.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

##<a name="test-app"></a>Testare l'app nel nuovo servizio mobile

4. Ricaricare l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in un Web browser per avviare l'app.

    > [AZURE.NOTE]Se è necessario riavviare il server Web, ripetere la procedura illustrata nella prima sezione.

2. Come in precedenza, digitare un testo in **Enter new task** e quindi fare clic su **Add**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5. Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Browse nel portale e fare clic su **Refresh**.

  	Si noti che il valore di complete è cambiato da **false** a **true**.

6. Nel file di progetto app.js individuare il metodo **RefreshTodoItems** e sostituire la riga di codice che definisce `query` con il codice seguente:

   		var query = todoItemTable.where({ complete: false });

7. Caricare di nuovo la pagina e selezionare un altro elemento nell'elenco.

   	Si noti che l'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app HTML di usare dati in Servizi mobili. Successivamente, è possibile passare alle esercitazioni che illustrano come autenticare gli utenti dell'app iniziando da [Aggiungere l'autenticazione all'app]. Se si usa un progetto di applicazione Cordova o PhoneGap, altre informazioni sulle notifiche push sono disponibili nell'articolo relativo alle [notifiche push alle app di Cordova con Microsoft Azure](https://msdn.microsoft.com/magazine/dn879353.aspx).

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-html-get-started.md
[Aggiungere l'autenticazione all'app]: mobile-services-html-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[portale di gestione]: https://manage.windowsazure.com/
[GetStartedWithData app]: http://go.microsoft.com/fwlink/?LinkID=286345
[app GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx

 

<!---HONumber=August15_HO8-->