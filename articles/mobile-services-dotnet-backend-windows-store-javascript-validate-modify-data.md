<properties pageTitle="Usare il back-end .NET per convalidare e modificare i dati (Windows Store) | Mobile Dev Center" description="Informazioni su come convalidare, modificare e aumentare i dati per l'app JavaScript per Windows Store con i servizi mobili di back-end .NET di Azure." services="mobile-services" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="wesmc"/>

# Convalidare e modificare i dati in Servizi mobili mediante il back-end .NET

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Questo argomento descrive come usare il codice in Servizi mobili di Azure back-end .NET per convalidare e modificare dati. Il servizio back-end .NET è un servizio HTTP creato con il framework API Web. Se si ha familiarità con la classe `ApiController` definita nel framework API Web, la classe `TableController` fornita da Servizi mobili risulterà molto intuitiva. `TableController` deriva dalla classe `ApiController` e fornisce funzionalità aggiuntive per l'interfaccia con una tabella di database. Può essere usato per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati, descritte in questa esercitazione. 

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Aggiungere la convalida della lunghezza della stringa]
2. [Aggiornare il client per il supporto della convalida]
3. [Testare la convalida della lunghezza]
4. [Aggiungere un campo timestamp per CompleteDate]
5. [Aggiornare il client per la visualizzazione del timestamp CompleteDate]

Questa esercitazione è basata sulle procedure e sul codice di esempio creato nell'esercitazione precedente, [Introduzione a Servizi mobili] o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/).  

## <a name="string-length-validation"></a>Aggiungere il codice di convalida a Servizi mobili

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Aggiornare il client

Ora che il servizio mobile è configurato per convalidare dati e inviare risposte di errore per una lunghezza di testo non valida, è necessario aggiornare l'app per gestire le risposte di errore risultanti dalla convalida. L'errore verrà rilevato come `IMobileServiceTable<TodoItem].InsertAsync()` dalla chiamata dell'app client.

1. Nella finestra Esplora soluzioni di Visual Studio, passare al progetto client JavaScript ed espandere la cartella **js**. Quindi, aprire il file default.js

2. Nel file default.js sostituire la funzione **insertTodoItem** esistente con la seguente definizione di funzione:


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

## <a name="test-length-validation"></a>Testare la convalida della lunghezza

1. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sul progetto di app client JavaScript e quindi scegliere **Debug**, **Avvia nuova istanza**.

2. Immettere per un nuovo elemento Todo un testo di lunghezza superiore a 10 caratteri, quindi fare clic su **Salva**.

    ![][1]

3. Se si immette testo non valido verrà visualizzato un messaggio simile al seguente.

    ![][2]

## <a name="add-timestamp"></a>Aggiungere un campo timestamp per CompleteDate


[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Aggiornare il client per la visualizzazione del timestamp completeDate

Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi i nuovi dati **completeDate**. 


1. In Esplora soluzioni di Visual Studio, nel progetto client JavaScript, aprire il file default.html. Sostituire l'elemento tag `div` del modello di binding con la definizione seguente. Salvare il file. Verrà aggiunto un tag `div` con la proprietà innerText associata a **completeDate**.
	      
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



2. In default.js rimuovere la funzione della clausola `.Where` nella funzione **refreshTodoItems** esistente in modo da includere gli elementi todoitems completati nei risultati.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };


3. In default.js aggiornare la funzione **updateCheckedTodoItem** come segue in modo che gli elementi vengano aggiornati dopo un aggiornamento e gli elementi completati non vengano rimossi dall'elenco. Salvare il file.	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. Nella finestra Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse su **Soluzione**, quindi scegliere **Ricompila soluzione** per ricompilare sia il client che il servizio back-end .NET. Verificare che entrambi i progetti vengano compilati senza errori.

    ![][3]
	
5. Premere **F5** per eseguire l'app client e il servizio in locale. Aggiungere alcuni nuovi elementi e selezionarne alcuni per contrassegnarli come completati modo da visualizzare l'aggiornamento del timestamp di **CompleteDate**.

    ![][4]

6. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio todolist, quindi scegliere **Pubblica**. Pubblicare il servizio back-end .NET in Microsoft Azure usando il file delle impostazioni di pubblicazione che è stato scaricato dal portale di Azure.

7. Aggiornare il file default.js file per il progetto client rimuovendo i simboli di commento dalla connessione all'indirizzo del servizio mobile. Testare l'app con il servizio back-end .NET ospitato nell'account Azure.




## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Usare il paging per ridefinire le query].

Gli script del server vengono inoltre usati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per altre informazioni, vedere le esercitazioni seguenti:

* [Autorizzazione lato servizio degli utenti]
  <br/>Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET.

<!-- Anchors. -->
[Aggiungere la convalida della lunghezza della stringa]: #string-length-validation
[Aggiornare il client per il supporto della convalida]: #update-client-validation
[Testare la convalida della lunghezza]: #test-length-validation
[Aggiungere un campo timestamp per CompleteDate]: #add-timestamp
[Aggiornare il client per la visualizzazione del timestamp CompleteDate]: #update-client-timestamp
[Passaggi successivi]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Autorizzazione lato servizio degli utenti]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
[Per iniziare]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portale di gestione]: https://manage.windowsazure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
