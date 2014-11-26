## <a name="update-app"></a>Aggiornamento dell'app per la chiamata all'API personalizzata

1.  In Visual Studio aprire il file default.html nel progetto di guida introduttiva, individuare l'elemento **button** denominato `buttonRefresh` e subito dopo aggiungere il nuovo elemento seguente:

        <button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

    Verrà aggiunto un nuovo pulsante nella pagina.

2.  Aprire il file di codice default.js nella cartella di progetto `js`, individuare la funzione **refreshTodoItems** e assicurarsi che contenga il codice seguente:

        todoTable.where({ complete: false })
           .read()
           .done(function (results) {
               todoItems = new WinJS.Binding.List(results);
               listItems.winControl.itemDataSource = todoItems.dataSource;
           });            

    Gli elementi verranno filtrati in modo che quelli completati non vengano restituiti dalla query.

3.  Dopo la funzione **RefreshTodoItems** aggiungere il codice seguente:

        var completeAllTodoItems = function () {
            var okCommand = new Windows.UI.Popups.UICommand("OK");

            // Asynchronously call the custom API using the POST method. 
            mobileService.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.commands.append(okCommand);
                dialog.showAsync().done(function () {
                    refreshTodoItems();
                });
            }, function (error) {
                var dialog = new Windows.UI.Popups
                    .MessageDialog(error.message);
                dialog.commands.append(okCommand);
                dialog.showAsync().done();
            });
        };

        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });

    Questo metodo gestisce l'evento **Click** per il nuovo pulsante. Nel client viene chiamato il metodo **InvokeApiAsync**, che invia una richiesta POST alla nuova API personalizzata. Il risultato restituito dall'API personalizzata viene visualizzato in una finestra di dialogo con messaggio, insieme a eventuali errori.

## <a name="test-app"></a>Test dell'app

1.  In Visual Studio premere **F5** per ricompilare il progetto e avviare l'app.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

3.  Ripetere il passaggio precedente fino ad aggiungere diversi elementi todo all'elenco.

4.  Fare clic sul pulsante **Complete All**.

    ![][0]

    Verrà visualizzata una finestra di dialogo con messaggio indicante il numero di elementi contrassegnati come completati, quindi la query filtrata verrà eseguita di nuovo cancellando tutti gli elementi dall'elenco.

  [0]: ./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png
