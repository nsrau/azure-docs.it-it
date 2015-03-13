

1. Nel file di script default.js situato proprio sotto la riga di codice che definisce l'elenco todoItems, aggiungere la seguente definizione di funzione:
 
        // Add a filter that adds a header to prevent caching. This makes sure that the 
		// latest data is returned when the 'Refresh; button is clicked.        
        var noCachingFilter = function (request, next, callback) {
            if (request.type === 'GET' && !request.headers['If-Modified-Since']) {
                request.headers['If-Modified-Since'] = 'Mon, 27 Mar 1972 00:00:00 GMT';
            }
            next(request, callback);
        };

	In questo modo si definisce una funzione di filtro che aggiunge l'intestazione  `If-Modified-Since` per impedire la memorizzazione nella cache sul client.
 
2. Successivamente, rimuovere il commento o aggiungere la seguente riga di codice e sostituire `<yourClient>` con la variabile aggiunta al file service.js al momento della connessione del progetto al servizio mobile:

		var todoTable = <yourClient>.withFilter(noCachingFilter).getTable('TodoItem');

   	This code creates a proxy object (**todoTable**) for the new database table, using the caching filter. 

3. Replace the **InsertTodoItem** function with the following code:

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	Questo codice consente di inserire un nuovo elemento nella tabella.

3. Sostituire la funzione **RefreshTodoItems** con il codice seguente:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	In questo modo verrà impostata l'associazione alla raccolta di elementi in todoTable, che contiene tutti gli oggetti **TodoItem** restituiti dal servizio mobile. 

4. Sostituire la funzione **UpdateCheckedTodoItem** con il codice seguente:
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile.

Una volta aggiornata l'app per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.
\<!--HONumber=42-->
