<properties 
	pageTitle="Aggiungere Servizi mobili a un'app esistente (Windows Store JavaScript) | Mobile Dev Center" 
	description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app JavaScript per Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>


# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

##Panoramica
In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app di Windows Store. Questa esercitazione consente di scaricare un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, di creare un nuovo servizio mobile e integrarlo con l'app e quindi di accedere al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013, che verrà usato per connettere il progetto di app di Windows Store a Servizi mobili.

##Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithMobileServices][Developer Code Samples site], un progetto di app di Windows Store in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1. Scaricare la versione JavaScript dell'app di esempio GetStartedWithData dal [sito degli esempi di codice di Developer Network]. 

2. In Visual Studio aprire il progetto scaricato, espandere la cartella **js** ed esaminare il file default.js.

   	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **List** in memoria.

3. Premere **F5** per ricompilare il progetto e avviare l'app.

4. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][0]

   	Il testo salvato viene visualizzato nella seconda colonna in **Esegui query e aggiorna dati**.

##<a name="create-service"></a>Creare un nuovo servizio mobile da Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>In Esplora soluzioni espandere le cartelle **services**, **mobile services**, **&lt;your_service>**, aprire il file di script service.js e osservare la nuova variabile globale, che presenta un aspetto simile al seguente:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Questo codice consente di accedere al nuovo servizio mobile nell'app tramite una variabile globale. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Poiché nel file default.html è stato aggiunto un riferimento a questo script, questa variabile è disponibile per tutti i file di script a cui viene fatto riferimento da questa pagina.</p>
</li>
</ol>

##Aggiungere una nuova tabella per l'archiviazione

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##Aggiornare l'app per l'uso del servizio mobile

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../../includes/mobile-services-windows-javascript-update-data-app.md)]

##Testare l'app nel nuovo servizio mobile

1. In Visual Studio premere F5 per eseguire l'app.

2. Come in precedenza, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5. Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Browse nel portale e fare clic su **Refresh**.

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

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di usare dati in Servizi mobili. Successivamente, è consigliabile leggere uno di questi altri argomenti:

* [Aggiungere l'autenticazione all'app](mobile-services-windows-store-javascript-get-started-users.md) <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Aggiungere notifiche push all'app](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript](mobile-services-html-how-to-use-client-library.md) <br/>Ulteriori informazioni su come utilizzare Servizi mobili con HTML e JavaScript.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[sito degli esempi di codice di Developer Network]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=July15_HO1-->