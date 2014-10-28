<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introduzione ai dati in Servizi mobili tramite Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows Store C#">Windows Store C#</a><a href="/it-it/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/it-it/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/it-it/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/it-it/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app di Windows Store. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

<div class="dev-callout"><b>Nota</b>
<p>In questa esercitazione verr&agrave; aggiunta la funzionalit&agrave; Servizi mobili a un'app di Windows Store creata in Visual Studio 2012. In Visual Studio 2013 sono disponibili nuove funzionalit&agrave; che semplificano la connessione delle app di Windows Store a Servizi mobili. Per ulteriori informazioni, vedere <a href="/it-it/develop/mobile/tutorials/get-started-with-data-js/">Introduzione ai dati in Servizi mobili</a>.</p>
</div>

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app di Windows Store][Download del progetto dell'app di Windows Store]
2.  [Creazione del servizio mobile][Creazione del servizio mobile]
3.  [Aggiunta di una tabella dati per l'archiviazione][Aggiunta di una tabella dati per l'archiviazione]
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili][Aggiornamento dell'app per l'utilizzo di Servizi mobili]
5.  [Test dell'app in Servizi mobili][Test dell'app in Servizi mobili]

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario un account Azure. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Download del progetto</span>Download del progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][app GetStartedWithData] che è un'app di Windows Store. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare la versione JavaScript dell'app di esempio GetStartedWithData dal [sito degli esempi di codice di Developer Network][app GetStartedWithData].

    ![][]

2.  In Visual Studio 2012 Express per Windows 8 aprire il progetto scaricato, espandere la cartella **js** ed esaminare il file default.js.

    Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **List** in memoria.

3.  Premere **F5** per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][1]

    Si noti che il testo salvato viene visualizzato nella seconda colonna sotto **Query and update data**.

## <a name="create-service"></a><span class="short-header">Creazione del servizio mobile</span>Creazione di un nuovo servizio mobile nel portale di gestione

[WACOM.INCLUDE [mobile-services-create-new-service-data][mobile-services-create-new-service-data]]

## <a name="add-table"></a><span class="short-header">Aggiunta di una nuova tabella</span>Aggiunta di una nuova tabella al servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][mobile-services-create-new-service-data-2]]

## <a name="update-app"></a><span class="short-header">Aggiornamento dell'app</span>Aggiornamento dell'app per l'utilizzo del servizio mobile per l'accesso ai dati

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, cercare `WindowsAzure.MobileServices.WinJS`, fare clic su **Installa** nel pacchetto di **Servizi mobili di Azure per WinJS**, quindi accettare il contratto di licenza.

    ![][2]

    La libreria client per i servizi di Servizi mobili verrà aggiunta al progetto.

3.  Nel file di progetto default.html aggiungere il riferimento allo script seguente nell'intestazione della pagina:

        <script type="text/javascript" src="/js/MobileServices.js"></script>

4.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

5.  Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

    ![][3]

    Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

6.  In Visual Studio aprire il file default.js, rimuovere i simboli di commento dal codice seguente che definisce la variabile **client**, quindi specificare, nell'ordine, l'URL e la chiave dell'applicazione indicati nel servizio mobile nel costruttore **MobileServiceClient**.

            var client = new WindowsAzure.MobileServiceClient(
                "AppUrl",
                "appKey"
            );

    Verrà creata una nuova istanza di MobileServiceClient utilizzata per accedere al servizio mobile.

7.  Rimuovere quindi i simboli di commento dalla riga di codice seguente:

        var todoTable = client.getTable('TodoItem');

    Questo codice crea un oggetto proxy (**todoTable**) per la nuova tabella di database.

8.  Sostituire la funzione **InsertTodoItem** con il codice seguente:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

    Questo codice consente di inserire un nuovo elemento nella tabella.

9.  Sostituire la funzione **RefreshTodoItems** con il codice seguente:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the TodoItems table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };      

    Ciò consente di impostare il binding sulla raccolta di elementi in todoTable, che contiene tutti gli elementi completati restituiti dal servizio mobile.

10. Sostituire la funzione **UpdateCheckedTodoItem** con il codice seguente:

        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

    In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

## <a name="test-app"></a><span class="short-header">Test dell'app</span>Test dell'app nel nuovo servizio mobile

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Come in precedenza, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione][portale di gestione] fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    ![][4]

    Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5.  Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Browse nel portale e fare clic su **Refresh**.

    Si noti che il valore di complete è cambiato da **false** a **true**.

6.  Nel file del progetto default.js sostituire la funzione **RefreshTodoItems** esistente con il codice seguente che consente di filtrare gli elementi completati:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  Nell'app selezionare un altro elemento dell'elenco e fare clic sul pulsante **Refresh**.

    Si noti che l'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati][Utilizzo di script per la convalida e la modifica di dati]
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query]
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "Windows Store C#"
  [Windows Store JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-data-js-vs2012 "Windows Store JavaScript"
  [Windows Phone]: /it-it/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone"
  [iOS]: /it-it/develop/mobile/tutorials/get-started-with-data-ios "iOS"
  [Android]: /it-it/develop/mobile/tutorials/get-started-with-data-android "Android"
  [HTML]: /it-it/develop/mobile/tutorials/get-started-with-data-html "HTML"
  [Xamarin.iOS]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android"
  [Introduzione ai dati in Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-with-data-js/
  [Download del progetto dell'app di Windows Store]: #download-app
  [Creazione del servizio mobile]: #create-service
  [Aggiunta di una tabella dati per l'archiviazione]: #add-table
  [Aggiornamento dell'app per l'utilizzo di Servizi mobili]: #update-app
  [Test dell'app in Servizi mobili]: #test-app
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F
  [app GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkId=262308
  []: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
  [portale di gestione]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-js
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-js
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-js-vs2012
