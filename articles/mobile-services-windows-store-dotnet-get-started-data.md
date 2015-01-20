<properties urlDisplayName="Get Started with Data" pageTitle="Introduzione ai dati (Windows Store) | Mobile Dev Center" metaKeywords="" description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app di Windows Store." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Introduzione ai dati in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]


<div class="dev-center-tutorial-subselector">
	<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">Back-end .NET</a> | 
	<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="JavaScript backend" class="current">Back-end JavaScript</a>
</div>


In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app di Windows Store. Questa esercitazione consente di scaricare un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, di creare un nuovo servizio mobile e integrarlo con l'app e quindi di accedere al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

>[WACOM.NOTE]Questo argomento illustra come usare Visual Studio 2013 per aggiungere Servizi mobili di Azure a un progetto di Windows Store. È possibile aggiungere lo stesso servizio mobile back-end JavaScript a un progetto di app Windows universale. Per altre informazioni, vedere la [versione di app Windows universale](/it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data) di questa esercitazione. 

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Download del progetto dell'app di Windows Store][Get the Windows Store app] 
2. [Creazione del servizio mobile da Visual Studio]
3. [Aggiunta di una tabella dati per l'archiviazione]
4. [Aggiornamento dell'app per l'uso del servizio mobile]
5. [Test dell'app in Servizi mobili]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-dotnet-get-started-data%2F).
* Visual Studio 2013, che facilita la connessione dell'app di Windows Store a Servizi mobili. 

##<a name="download-app"></a>Download del progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithMobileServices][Developer Code Samples site], che è un progetto di app di Windows Store in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale. 

1. Scaricare la versione in C# dell'app di esempio GetStartedWithMobileServices dal [sito degli esempi di codice di Developer Network]. 

2. In Visual Studio 2013 aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs.

   	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection&lt;TodoItem&gt;** in memoria.

3. Premere **F5** per ricompilare il progetto e avviare l'app.

4. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][0]  

   	Si noti che il testo salvato viene visualizzato nella seconda colonna sotto **Query and update data**.

##<a name="create-service"></a>Creazione di un nuovo servizio mobile da Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7"><li><p>In Esplora soluzioni aprire il file di codice App.xaml.cs e notare il nuovo campo static aggiunto alla classe **App**, simile all'esempio seguente:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Questo codice consente di accedere al nuovo servizio mobile nell'app tramite un'istanza della <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">classe MobileServiceClient</a>. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Questo campo static è disponibile in tutte le pagine dell'app.</p>
</li>
</ol>

##<a name="add-table"></a>Aggiunta di una nuova tabella per l'archiviazione

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[WACOM.NOTE]Le nuove tabelle create includono le colonne Id, __createdAt, __updatedAt, e __version. Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico](http://msdn.microsoft.com/it-it/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Aggiornamento dell'app per l'uso del servizio mobile

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-app"></a>Test dell'app sul nuovo servizio mobile

1. In Visual Studio premere F5 per eseguire l'app.

2. Come in precedenza, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]
  
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5. Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Sfoglia nel portale e fare clic su **Aggiorna**. 

  	Si noti che il valore di complete è cambiato da **false** a **true**.

6. Nel file del progetto MainPage.xaml.cs sostituire il metodo **RefreshTodoItems** esistente con il codice seguente che consente di filtrare gli elementi completati:

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7. Nell'app selezionare un altro elemento dell'elenco e fare clic sul pulsante **Refresh**.

   	Si noti che l'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Uso di script per la convalida e la modifica di dati]
  <br/>Informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Uso del paging per ridefinire le query]
  <br/>Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Informazioni su come usare Servizi mobili con .NET.
  
<!-- Anchors. -->

[Download dell'app di Windows Store]: #download-app
[Creazione del servizio mobile da Visual Studio]: #create-service
[Aggiunta di una tabella dati per l'archiviazione]: #add-table
[Aggiornamento dell'app per l'uso del servizio mobile]: #update-app
[Test dell'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png


<!-- URLs. -->
[Uso di script per la convalida e la modifica di dati]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
[Uso del paging per ridefinire le query]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sito degli esempi di codice di Developer Network]:  http://go.microsoft.com/fwlink/p/?LinkId=328660

[Classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

<!--HONumber=35.2-->
