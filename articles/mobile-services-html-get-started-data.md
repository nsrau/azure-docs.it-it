<properties urlDisplayName="Get Started with Data (HTML5)" pageTitle="Introduzione ai dati (HTML5) | Mobile Developer Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Aggiungere Servizi mobili a un'app esistente

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app HTML. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app. Si effettuerà quindi l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

>[WACOM.NOTE]In questa esercitazione viene descritto come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app HTML e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione <a href="/it-it/develop/mobile/tutorials/get-started-html">Introduzione a Servizi mobili</a>.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Download del progetto dell'app HTML]
2. [Creare il servizio mobile]
3. [Aggiungere una tabella dati per l'archiviazione]
4. [Aggiornare l'app per l'uso di Servizi mobili]
5. [Testare l'app in Servizi mobili]

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div> 

###Requisiti aggiuntivi

È possibile ospitare l'app GetStartedWithData in qualsiasi server Web. Per semplicità, tuttavia, sono stati forniti script che consentono di eseguire l'app in `http://localhost:8000`.
 
+ Per completare questa esercitazione e usare localhost, è necessario che nel computer locale sia in esecuzione uno dei server Web seguenti:

	+  **In Windows**: IIS Express. IIS Express viene installato tramite l'[Installazione guidata piattaforma Web Microsoft].   
	+  **In MacOS X**: Python, che dovrebbe essere già installato.
	+  **In Linux**: Python. È necessario installare la [versione più recente di Python]. 
	
	È possibile usare qualsiasi server Web per ospitare l'app, tuttavia quelli indicati sono i server Web supportati dagli script scaricati.  

+ Un Web browser che supporta HTML5.

<h2><a name="download-app"></a>Scaricare il progetto GetStartedWithData</h2>

Questa esercitazione è basata sull'[GetStartedWithData app] che è un'app HTML5. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale. 

1. [Download dei file del progetto dell'app HTML][App GetStartedWithData].

2. In un editor HTML aprire il progetto scaricato ed esaminare il file app.js.

   	Si noti che gli elementi aggiunti vengono archiviati in un oggetto **Array** (**staticItems**) in memoria. Aggiornare la pagina. I dati scompariranno dalla visualizzazione perché non sono persistenti.

3. Avviare uno dei file di comando seguenti dalla sottocartella **server**.

	+ **launch-windows** (computer Windows) 
	+ **launch-mac.command** (computer Mac OS X)
	+ **launch-linux.sh** (computer Linux)

	<div class="dev-callout"><b>Nota</b>
		<p>In un computer Windows, digitare `R` quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.</p>
	</div>
	
	Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

4. Aprire l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in un Web browser per avviare l'app.

5. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_, in **Enter new task** e quindi fare clic su **Add**.

   	![][0]  

   	Si noti che il testo salvato viene aggiunto alla matrice **staticItems** la pagina viene aggiornata per visualizzare il nuovo elemento.

<h2><a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile</h2>

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella nell'istanza di database SQL associata.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Fare clic sulla scheda **Dati** e quindi su **+Crea**.

   	![][5]

   	Verrà visualizzata la finestra di dialogo **Crea nuova tabella**.

3. In **Nome tabella** digitare _TodoItem_, quindi fare clic sul segno di spunta.

  	![][6]

  	Verrà creata una nuova tabella di archiviazione, **TodoItem**, con le autorizzazioni predefinite impostate. Questo significa che chiunque disponga della chiave dell'applicazione, che viene distribuita con l'app, potrà accedere alla tabella e modificare i dati.

    <div class="dev-callout"> 
	<b>Nota</b> 
	<p>Nella Guida introduttiva di Servizi mobili viene usato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo è possibile evitare conflitti di dati quando più servizi mobili usano lo stesso database.</p> 
	</div>

4. Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

5. Fare clic sulla scheda **Colonne**. Verificare che siano state create automaticamente le colonne predefinite seguenti: 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>Nome colonna</th>
 	<th>Tipo</th>
 	<th>Indice</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>stringa</td>
 	<td>Indicizzata</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>data</td>
 	<td>Indicizzata</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>data</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 

  	Questo è il requisito minimo per le tabelle in Servizi mobili. 

    <div class="dev-callout"><b>Nota</b>
	<p>Se nel servizio mobile è abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.</p>
    </div>

6. Nella scheda **Configura** verificare che `localhost` sia presente nell'elenco **Consentire le richieste da nomi host** in **Condivisione risorse tra le origini (CORS)**. In caso contrario, digitare `localhost` nel campo **Nome host** e quindi fare clic su **Salva**.

  	![][11]

	<div class="dev-callout"><b>Nota</b>
		<p>Se si distribuisce l'app di guida introduttiva in un server Web diverso da localhost, è necessario aggiungere il nome host del server Web all'elenco <strong>Consentire le richieste da nomi host</strong>. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn155871.aspx" target="_blank">Condivisione risorse tra le origini</a>.</p>
	</div>

È ora possibile usare il nuovo servizio mobile come archivio dati per l'app.

<h2><a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati</h2>

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale. 

3. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

4. Fare clic sulla scheda **Dashboard** e prendere nota dell'**URL sito**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

   	![][8]

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

1. Nell'editor Web aprire il file di progetto index.html e aggiungere il codice seguente ai riferimenti allo script per la pagina:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

5. Nell'editor aprire il file app.js, rimuovere i simboli di commento dal codice seguente che definisce la variabile **MobileServiceClient**, infine specificare l'URL e la chiave dell'applicazione indicati nel servizio mobile nel costruttore **MobileServiceClient**, senza modificare l'ordine:

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	Verrà creata una nuova istanza di MobileServiceClient usata per accedere al servizio mobile.

6. Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

		var itemCount = 0;
		var staticItems = [];

	I dati verranno archiviati nel servizio mobile e non in una matrice in memoria.

6. Rimuovere quindi i simboli di commento dalla riga di codice seguente:

        todoItemTable = client.getTable('todoitem');

   	Questo codice crea un oggetto proxy (**todoItemTable**) per l'elemento **TodoItem** del database SQL. 

7. Sostituire il gestore eventi **$('#add-item').submit** con il codice seguente:

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

10. Sostituire il gestore eventi **$(document.body).on('click', '.item-delete')** con il codice seguente:

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	Verrà inviata una richiesta di eliminazione al servizio mobile quando si fa clic sul pulsante **Delete**.

Una volta aggiornata l'app per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

<h2><a name="test-app"></a>Testare l'app nel nuovo servizio mobile</h2>

4. Ricaricare l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in un Web browser per avviare l'app.

    <div class="dev-callout"><b>Nota</b>
	<p>Se è necessario riavviare il server Web, ripetere la procedura illustrata nella prima sezione.</p>
    </div>

2. Come in precedenza, digitare un testo in **Enter new task** e quindi fare clic su **Add**. 

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5. Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Sfoglia nel portale e fare clic su **Aggiorna**. 

  	Si noti che il valore di complete è cambiato da **false** a **true**.

6. Nel file di progetto app.js individuare il metodo **RefreshTodoItems** e sostituire la riga di codice che definisce la `query` con il codice seguente:

   		var query = todoItemTable.where({ complete: false });

7. Caricare di nuovo la pagina e selezionare un altro elemento nell'elenco.

   	Si noti che l'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app HTML di usare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Convalidare e modificare dati mediante script]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Altre informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.
 
Dopo avere completato la serie di esercitazioni relative ai dati, è possibile passare alle esercitazioni che illustrano come autenticare gli utenti dell'app, iniziando da [Introduzione all'autenticazione].

<!-- Anchors. -->
[Download del progetto dell'app HTML]: #download-app
[Creare il servizio mobile]: #create-service
[Aggiungere una tabella dati per l'archiviazione]: #add-table
[Aggiornare l'app per l'uso di Servizi mobili]: #update-app
[Testare l'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[Convalidare e modificare dati mediante script]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-html
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[App GetStartedWithData]:  http://go.microsoft.com/fwlink/?LinkID=286345

[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client

[Condivisione di risorse tra le origini]: http://msdn.microsoft.com/it-it/library/windowsazure/dn155871.aspx

