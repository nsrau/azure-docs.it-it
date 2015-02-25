<properties pageTitle="Introduzione ai dati (Windows Store JavaScript) | Mobile Dev Center" description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app JavaScript per Windows Store." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>	

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"/>


# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend">Back-end .NET</a> | 
	<a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript backend" class="current">Back-end JavaScript</a>
</div>


Questo argomento descrive come usare Servizi mobili di Azure per sfruttare i dati in un'app di Windows Store. In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

>[AZURE.NOTE]Questo argomento descrive come usare Visual Studio 2013 per aggiungere Servizi mobili di Azure a un progetto di Windows Store. È possibile aggiungere lo stesso servizio mobile back-end JavaScript a un progetto di app Windows universale. Per altre informazioni, vedere la [versione per l'app di Windows universale](/it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data) di questa esercitazione.

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Scaricare il progetto di app di Windows Store][Scaricare l'app di Windows Store] 
2. [Creazione del servizio mobile]
3. [Aggiunta di una tabella dati per l'archiviazione]
4. [Aggiornamento dell'app per l'uso di Servizi mobili]
5. [Testare l'app in Servizi mobili]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013, che facilita la connessione dell'app di Windows Store a Servizi mobili. Per completare la stessa procedura di base in Visual Studio 2012, eseguire le operazioni descritte nell'argomento <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012">Introduzione ai dati in Servizi mobili tramite Visual Studio 2012</a>. 

<h2><a name="download-app"></a>Scaricare il progetto GetStartedWithData</h2>

Questa esercitazione è basata sull'[app GetStartedWithMobileServices][sito di esempi di codice per sviluppatori], ovvero un progetto di app di Windows Store disponibile in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.  

1. Scaricare la versione JavaScript dell'app di esempio GetStartedWithData dal [sito degli esempi di codice di Developer Network]. 

2. In Visual Studio 2012 Express per Windows 8 aprire il progetto scaricato, espandere la cartella **js** ed esaminare il file default.js.

   	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **List** in memoria.

3. Premere **F5** per ricompilare il progetto e avviare l'app.

4. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][0]  

   	Si noti che il testo salvato viene visualizzato nella seconda colonna sotto **Query and update data**.

##<a name="create-service"></a>Creare un nuovo servizio mobile da Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>In Esplora soluzioni espandere le cartelle **services**, **mobile services**, **&lt;servizio&gt;**, aprire il file di script service.js e osservare la nuova variabile globale, che è simile alla seguente:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Questo codice consente di accedere al nuovo servizio mobile nell'app tramite una variabile globale. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Poiché nel file default.html è stato aggiunto un riferimento a questo script, questa variabile è disponibile per tutti i file di script a cui viene fatto riferimento da questa pagina.</p>
</li>
</ol>

##<a name="add-table"></a>Aggiungere una nuova tabella per l'archiviazione dati

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Le nuove tabelle create includono le colonne Id, __createdAt, __updatedAt, e __version. Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico](http://msdn.microsoft.com/it-it/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-app"></a>Testare l'app nel nuovo servizio mobile

1. In Visual Studio premere F5 per eseguire l'app.

2. Come in precedenza, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5. Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Sfoglia nel portale e fare clic su **Aggiorna**. 

  	Si noti che il valore di complete è cambiato da **false** a **true**.

6. Nel file del progetto default.js sostituire la funzione **RefreshTodoItems** esistente con il codice seguente che consente di filtrare gli elementi completati:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. Nell'app selezionare un altro elemento dell'elenco e fare clic sul pulsante **Refresh**.

   	Si noti che l'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di usare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Usare script per la convalida e la modifica di dati]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]
  <br/>Altre informazioni su come usare Servizi mobili con HTML e JavaScript.

<!-- Anchors. -->

[Download dell'app di Windows Store]: #download-app
[Creazione del servizio mobile]: #create-service
[Aggiunta di una tabella dati per l'archiviazione]: #add-table
[Aggiornamento dell'app per l'uso di Servizi mobili]: #update-app
[Testare l'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Usare script per la convalida e la modifica di dati]: /it-it/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Usare il paging per ridefinire le query]: /it-it/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Sito degli esempi di codice di Developer Network]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library/


<!--HONumber=42-->
