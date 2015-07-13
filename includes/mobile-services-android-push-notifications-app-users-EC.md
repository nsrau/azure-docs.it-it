
È ora necessario modificare il momento della registrazione per le notifiche per assicurarsi che l'utente sia autenticato prima che si provi a eseguire la registrazione.


1. In Eclipse aprire Package Explorer, aprire il file ToDoActivity.java e trovare il metodo `onCreate`. Spostare il codice seguente dal metodo `onCreate` all'inizio del metodo `createTable`.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

     Il metodo `createTable` viene chiamato al momento del completamento del metodo `authenticate`. L'intero metodo `createTable` dovrebbe essere simile al seguente.

        private void createTable() {
        
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        
            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
            
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
            
            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
            
            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }	

<!---HONumber=62-->