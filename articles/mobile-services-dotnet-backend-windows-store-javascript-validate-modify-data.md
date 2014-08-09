<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Javascript Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

Convalida e modifica dei dati in Servizi mobili mediante il back-end .NET
=========================================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows Store C#") [Windows Store JavaScript](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows Store JavaScript") [Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare il codice in Servizi mobili di Azure back-end .NET per convalidare e modificare dati. Il servizio back-end .NET è un servizio HTTP creato con il framework API Web. Se si ha familiarità con la classe `ApiController` definita nel framework API Web, la classe `TableController` fornita da Servizi mobili risulterà molto intuitiva. `TableController` deriva dalla classe `ApiController` e fornisce funzionalità aggiuntive per l'interfaccia con una tabella di database. Può essere utilizzato per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati, descritte in questa esercitazione.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa](#string-length-validation)
2.  [Aggiornamento del client per il supporto della convalida](#update-client-validation)
3.  [Test della convalida della lunghezza](#test-length-validation)
4.  [Aggiunta di un campo timestamp per CompleteDate](#add-timestamp)
5.  [Aggiornamento del client per la visualizzazione del timestamp CompleteDate](#update-client-timestamp)

Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/) o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/) o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/).

Aggiunta del codice di convalida a Servizi mobili
-------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

Aggiornamento del client
------------------------

Ora che il servizio mobile è configurato per convalidare dati e inviare risposte di errore per una lunghezza di testo non valida, è necessario aggiornare l'app per gestire le risposte di errore risultanti dalla convalida. L'errore verrà rilevato dalla chiamata dell'app client a `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  Nella finestra Esplora soluzioni di Visual Studio, passare al progetto client JavaScript ed espandere la cartella **js**. Quindi, aprire il file default.js

2.  Nel file default.js sostituire la funzione **insertTodoItem** esistente con la seguente definizione di funzione:

         var insertTodoItem = function (todoItem) {
             // This code inserts a new TodoItem into the database. When the operation completes
             // and Mobile Services has assigned an id, the item is added to the Binding List
             todoTable.insert(todoItem)
                 .then(function (item) {
                   todoItems.push(item);
                 },
                 function (error) {
                   var msgDialog =
                     new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                     error.request.statusText + "(" + error.request.status + ")");
                   msgDialog.showAsync();
                 });
         };

    Questa versione della funzione include la gestione degli errori e visualizza un messaggio `MessageDialog` con il messaggio di errore proveniente dalla risposta, dal testo dello stato e dal codice dello stato.

Test della convalida della lunghezza
------------------------------------

1.  In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sul progetto di app client JavaScript e quindi scegliere **Debug**, **Avvia nuova istanza**.

2.  Immettere per un nuovo elemento Todo un testo di lunghezza superiore a 10 caratteri, quindi fare clic su **Salva**.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png)

3.  Se si immette testo non valido verrà visualizzato un messaggio simile al seguente.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png)

Aggiunta di un campo timestamp per CompleteDate
-----------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

Aggiornamento del client per la visualizzazione del timestamp CompleteDate
--------------------------------------------------------------------------

Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi i nuovi dati **CompleteDate**.

1.  In Esplora soluzioni di Visual Studio, nel progetto client JavaScript, aprire il file default.html. Sostituire l'elemento tag `div` del template di binding con la definizione seguente. Salvare il file. Verrà aggiunto un tag `div` con la proprietà innerText associata a **completeDate**.

         <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
           <div style="display: -ms-grid; -ms-grid-columns: 3">
             <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
               dataContext: this" />
             <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
               data-win-bind="innerText: text">
             </div>
             <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
               data-win-bind="innerText: completeDate">
             </div>
           </div>
         </div>

2.  In default.js rimuovere la funzione della clausola `.Where` nella funzione **refreshTodoItems** esistente in modo da includere gli elementi todo completati nei risultati.

             var refreshTodoItems = function () {
                 // This code refreshes the entries in the list view be querying the TodoItems table.
                 // The query excludes completed TodoItems
                 todoTable.read()
                     .done(function (results) {
                         todoItems = new WinJS.Binding.List(results);
                         listItems.winControl.itemDataSource = todoItems.dataSource;
                     });
             };

3.  In default.js aggiornare la funzione **updateCheckedTodoItem** come segue in modo che gli elementi vengano aggiornati dopo un aggiornamento e gli elementi completati non vengano rimossi dall'elenco. Salvare il file.

             var updateCheckedTodoItem = function (todoItem) {
                 // This code takes a freshly completed TodoItem and updates the database. 
                 todoTable.update(todoItem).done(function () {
                     refreshTodoItems();
                 });
             };

4.  Nella finestra Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse su **Soluzione**, quindi scegliere **Ricompila soluzione** per ricompilare sia il client sia il servizio back-end .NET. Verificare che entrambi i progetti vengano compilati senza errori.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png)

5.  Premere **F5** per eseguire l'app client e il servizio in locale. Aggiungere alcuni nuovi elementi e selezionarne alcuni per contrassegnarli come completati modo da visualizzare l'aggiornamento del timestamp di **CompleteDate**.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png)

6.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio todolist, quindi scegliere **Pubblica**. Pubblicare il servizio back-end .NET in Microsoft Azure utilizzando il file delle impostazioni di pubblicazione che è stato scaricato dal portale di Azure.

7.  Aggiornare il file default.js file per il progetto client rimuovendo i simboli di commento dalla connessione all'indirizzo del servizio mobile. Testare l'app sul servizio back-end .NET ospitato nell'account Azure.

Passaggi successivi
-------------------

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet).

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione lato servizio degli utenti](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/)
    <br/>LInformazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push](it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/)
    <br/>LInformazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    <br/>LUlteriori informazioni su come utilizzare Servizi mobili con .NET.


<!-- Anchors. -->
[Add string length validation]: #string-length-validation
[Update the client to support validation]: #update-client-validation
[Test length validation]: #test-length-validation
[Add a timestamp field for CompleteDate]: #add-timestamp
[Update the client to display the CompleteDate]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Service-side authorization of users]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
[Getting Started]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Get started with data]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Get started with authentication]: it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Get started with push notifications]: it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Management Portal]: https://manage.windowsazure.com/
[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
