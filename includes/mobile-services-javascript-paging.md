

1. In Visual Studio aprire il progetto modificato durante l'esercitazione **Introduzione ai dati**.

2. Premere **F5** per eseguire l'app, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

3. Ripetere il passaggio precedente almeno tre volte, in modo da avere più di tre elementi archiviati nella tabella TodoItem.

2. Nel file default.js sostituire il metodo **RefreshTodoItems** con il codice seguente:

        var refreshTodoItems = function () {
            // Define a filtered query that returns the top 3 items.
            todoTable.where({ complete: false })
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

  	Questa query, se eseguita durante l'associazione dati, restituisce i primi tre elementi non contrassegnati come completati.

3. Premere **F5** per eseguire l'app.

  	Si noti che sono visualizzati solo i primi tre risultati della tabella TodoItem.

4. (Facoltativo) È possibile visualizzare l'URI della richiesta inviata al servizio mobile utilizzando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser [Fiddler].

   	Si noti che il metodo **take(3)** è stato convertito nell'opzione di query **$top=3** nell'URI della query.

5. Aggiornare nuovamente il metodo **RefreshTodoItems** con il codice seguente:
            
        var refreshTodoItems = function () {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            todoTable.where({ complete: false })
                .skip(3)
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

   	Questa query ignora i primi tre risultati e restituisce i tre risultati successivi. Si tratta, in effetti, della seconda "pagina" di dati le cui dimensioni corrispondono a tre voci.

    > [AZURE.NOTE]Nell'esercitazione, lo scenario è stato semplificato con il passaggio di valori di paging hardcoded ai metodi **Take** e **Skip**. In un'app reale è possibile usare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive. È inoltre possibile chiamare il metodo **includeTotalCount** per ottenere il conteggio totale degli elementi disponibili sul server, insieme ai dati di paging.

6. (Facoltativo) Anche in questo caso, è possibile visualizzare l'URI della richiesta inviata al servizio mobile.

   	Si noti che il metodo **skip(3)** è stato convertito nell'opzione di query **$skip=3** nell'URI della query.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412

<!---HONumber=Oct15_HO3-->