<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Javascript Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Convalida e modifica dei dati in Servizi mobili mediante il back-end .NET

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#] [Windows Store JavaScript][Windows Store JavaScript] [Windows Phone][Windows Phone][iOS][iOS] [Android][Android] [HTML][HTML][Xamarin.iOS][Xamarin.iOS] [Xamarin.Android][Xamarin.Android]

</div>

<div class="dev-center-tutorial-subselector">

[Back-end .NET][Back-end .NET] | [Back-end JavaScript][Back-end JavaScript]

</div>

Questo argomento illustra come usare il codice in Servizi mobili di Azure back-end .NET per convalidare e modificare dati. Il servizio back-end .NET è un servizio HTTP creato con il framework API Web. Se si ha familiarità con la classe `ApiController` definita nel framework API Web, la classe `TableController` fornita da Servizi mobili risulterà molto intuitiva. `TableController` deriva dalla classe `ApiController` e fornisce funzionalità aggiuntive per l'interfaccia con una tabella di database. Può essere utilizzato per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati, descritte in questa esercitazione.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa][Aggiunta della convalida della lunghezza della stringa]
2.  [Aggiornamento del client per il supporto della convalida][Aggiornamento del client per il supporto della convalida]
3.  [Test della convalida della lunghezza][Test della convalida della lunghezza]
4.  [Aggiunta di un campo timestamp per CompleteDate][Aggiunta di un campo timestamp per CompleteDate]
5.  [Aggiornamento del client per la visualizzazione del timestamp CompleteDate][Aggiornamento del client per la visualizzazione del timestamp CompleteDate]

Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati].

## <a name="string-length-validation"></a>Aggiunta del codice di convalida a Servizi mobili

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

## <a name="update-client-validation"></a>Aggiornamento del client

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

## <a name="test-length-validation"></a>Test della convalida della lunghezza

1.  In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sul progetto di app client JavaScript e quindi scegliere **Debug**, **Avvia nuova istanza**.

2.  Immettere per un nuovo elemento Todo un testo di lunghezza superiore a 10 caratteri, quindi fare clic su **Salva**.

    ![][0]

3.  Se si immette testo non valido verrà visualizzato un messaggio simile al seguente.

    ![][1]

## <a name="add-timestamp"></a>Aggiunta di un campo timestamp per CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Aggiornamento del client per la visualizzazione del timestamp CompleteDate

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

2.  In default.js rimuovere la funzione della clausola `.Where` nella funzione **refreshTodoItems** esistente in modo da includere gli elementi todoitems completati nei risultati.

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

    ![][2]

5.  Premere **F5** per eseguire l'app client e il servizio in locale. Aggiungere alcuni nuovi elementi e selezionarne alcuni per contrassegnarli come completati modo da visualizzare l'aggiornamento del timestamp di **CompleteDate**.

    ![][3]

6.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio todolist, quindi scegliere **Pubblica**. Pubblicare il servizio back-end .NET in Microsoft Azure utilizzando il file delle impostazioni di pubblicazione che è stato scaricato dal portale di Azure.

7.  Aggiornare il file default.js file per il progetto client rimuovendo i simboli di commento dalla connessione all'indirizzo del servizio mobile. Testare l'app sul servizio back-end .NET ospitato nell'account Azure.

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query].

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione lato servizio degli utenti][Autorizzazione lato servizio degli utenti]
    Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.



  [Windows Store C#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone"
  [iOS]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "JavaScript backend"
  [Aggiunta della convalida della lunghezza della stringa]: #string-length-validation
  [Aggiornamento del client per il supporto della convalida]: #update-client-validation
  [Test della convalida della lunghezza]: #test-length-validation
  [Aggiunta di un campo timestamp per CompleteDate]: #add-timestamp
  [Aggiornamento del client per la visualizzazione del timestamp CompleteDate]: #update-client-timestamp
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Autorizzazione lato servizio degli utenti]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
