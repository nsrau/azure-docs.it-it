

1. Successivamente, rimuovere il commento o aggiungere la riga di codice seguente e sostituire `<yourClient>` con la variabile aggiunta al file service.js al momento della connessione del progetto al servizio mobile:

		var todoTable = <yourClient>.getTable('TodoItem');

   	Questo codice crea un oggetto proxy (**todoTable**) per la nuova tabella di database, usando il filtro per la memorizzazione nella cache.

2. Sostituire la funzione **InsertTodoItem** con il codice seguente:

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

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

<!--HONumber=54-->