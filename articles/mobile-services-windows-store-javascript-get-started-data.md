<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introduzione ai dati in Servizi mobili
======================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")
[Back-end .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Back-end .NET") | [Back-end JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app di Windows Store. In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.
**Nota**

Per completare questa esercitazione, è necessario disporre di Visual Studio 2013, che facilita la connessione dell'app di Windows Store a Servizi mobili. Per completare la stessa procedura di base in Visual Studio 2012, eseguire le operazioni descritte nell'argomento [Introduzione ai dati in Servizi mobili tramite Visual Studio 2012](/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012/).

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app di Windows Store](#download-app)
2.  [Creazione del servizio mobile](#create-service)
3.  [Aggiunta di una tabella dati per l'archiviazione](#add-table)
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili](#update-app)
5.  [Test dell'app in Servizi mobili](#test-app)

**Nota**

Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F).

Download del progettoDownload del progetto GetStartedWithData
-------------------------------------------------------------

Questa esercitazione è basata sull'[app GetStartedWithMobileServices](http://go.microsoft.com/fwlink/p/?LinkId=328660), ovvero un progetto di app di Windows Store disponibile in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare la versione JavaScript dell'app di esempio GetStartedWithData dal [sito degli esempi di codice di Developer Network](http://go.microsoft.com/fwlink/p/?LinkId=328660).

   	![][10]

2.  In Visual Studio 2012 Express per Windows 8 aprire il progetto scaricato, espandere la cartella **js** ed esaminare il file default.js.

   	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **List** in memoria.

3.  Premere **F5** per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][0]  

   	Si noti che il testo salvato viene visualizzato nella seconda colonna sotto **Query and Update Data**.

Creazione del servizio mobileCreazione di un nuovo servizio mobile da Visual Studio
-----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  In Esplora soluzioni espandere le cartelle **services**, **mobile services**, **&lt;your\_service\>**, aprire il file di script service.js e osservare la nuova variabile globale, che presenta un aspetto simile al seguente:

    ``` {}
    var todolistClient = new WindowsAzure.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
    ```

    Questo codice consente di accedere al nuovo servizio mobile nell'app tramite una variabile globale. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Poiché nel file default.html è stato aggiunto un riferimento a questo script, questa variabile è disponibile per tutti i file di script a cui viene fatto riferimento da questa pagina.

Aggiunta di una nuova tabellaAggiunta di una nuova tabella al servizio mobile e aggiornamento dell'app
------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  Nel file default.js inserire i simboli di commento nella riga che definisce la raccolta di elementi esistenti, quindi rimuovere i simboli di commento o aggiungere la riga di codice seguente e sostituire `<yourClient>;` con la variabile aggiunta al file service.js durante la connessione del progetto al servizio mobile:

         var todoTable = <yourClient>.getTable('TodoItem');

   	Questo codice crea un oggetto proxy (**todoTable**) per la nuova tabella di database. 

2.  Sostituire la funzione **InsertTodoItem** con il codice seguente:

         var insertTodoItem = function (todoItem) {
             // Inserts a new row into the database. When the operation completes
             // and Mobile Services has assigned an id, the item is added to the binding list.
             todoTable.insert(todoItem).done(function (item) {
                 todoItems.push(item);
             });
         };

    Questo codice consente di inserire un nuovo elemento nella tabella.

    **Nota**

    Le nuove tabelle vengono create solo con una colonna Id. Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per ulteriori informazioni, vedere [Schema dinamico](http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx).

3.  Sostituire la funzione **RefreshTodoItems** con il codice seguente:

         var refreshTodoItems = function () {
             // This code refreshes the entries in the list by querying the table. 
             todoTable.read().done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };

   	In questo modo verrà impostata l'associazione alla raccolta di elementi in todoTable, che contiene tutti gli oggetti **TodoItem** restituiti dal servizio mobile. 

4.  Sostituire la funzione **UpdateCheckedTodoItem** con il codice seguente:

         var updateCheckedTodoItem = function (todoItem) {
             // This code takes a freshly completed TodoItem and updates the database. 
             todoTable.update(todoItem);
         };

   	In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

Test dell'appTest dell'app nel nuovo servizio mobile
----------------------------------------------------

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Come in precedenza, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

   	![][9]
          
   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5.  Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Browse nel portale e fare clic su **Refresh**.

   	Si noti che il valore di complete è cambiato da **false** a **true**.

1.  Nel file del progetto default.js sostituire la funzione **RefreshTodoItems** esistente con il codice seguente che consente di filtrare gli elementi completati:

         var refreshTodoItems = function () {                     
             // More advanced query that filters out completed items. 
             todoTable.where({ complete: false })
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });            
         };

2.  Nell'app selezionare un altro elemento dell'elenco e fare clic sul pulsante **Refresh**.

   	Si noti che l'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

Passaggi successivi
-------------------

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js)

    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-js)

    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-js)

    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-js)

    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)

    Ulteriori informazioni su come utilizzare Servizi mobili con HTML e JavaScript.

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png
[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png
