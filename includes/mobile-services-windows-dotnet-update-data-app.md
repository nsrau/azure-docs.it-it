
1. Nel file MainPage.xaml.cs aggiungere o rimuovere i simboli di commento dalle istruzioni using seguenti: 

		using Microsoft.WindowsAzure.MobileServices;

2. Sostituire la definizione della classe TodoItem con il codice seguente: 

	    public class TodoItem
	    {
	        public string Id { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
	        public string Text { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
	        public bool Complete { get; set; }
	    }
	
	L'**attributo JsonProperty** viene usato per definire il mapping tra nomi di proprietà nel tipo di client e i nomi di colonna nella tabella dati sottostante.

	>[AZURE.NOTE] In un progetto di app Windows universale, la classe TodoItem è definita nel file di codice separato nella cartella DataModel condivisa.

3. In MainPage.xaml.cs, rimuovere i simboli di commento o eliminare la riga in cui viene definita la raccolta di elementi esistenti, quindi rimuovere il commento o aggiungere le righe seguenti e sostituire _&lt;yourClient&gt;_ con il campo  `MobileServiceClient` aggiunto al file App.xaml.cs durante la connessione del progetto al servizio mobile: 

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	Questo codice consente di creare una raccolta di associazione compatibile con Servizi mobili (elementi) e una classe proxy per la tabella del database (todoTable). 

4. Nel metodo **InsertTodoItem** rimuovere la riga di codice per l'impostazione della proprietà **TodoItem.Id**, aggiungere il modificatore **async** al metodo e rimuovere i simboli di commento dalla riga di codice seguente: 

		await todoTable.InsertAsync(todoItem);


	Questo codice consente di inserire un nuovo elemento nella tabella. 

5. Sostituire il metodo **RefreshTodoItems** con il codice seguente: 

		private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

	In questo modo verrà impostata l'associazione alla raccolta di elementi in  `todoTable`, che contiene tutti gli oggetti **TodoItem** restituiti dal servizio mobile. Se si verifica un problema durante l'esecuzione della query, viene visualizzata una finestra di messaggio in cui sono mostrati gli errori. 

6. Nel metodo **UpdateCheckedTodoItem** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente: 

		await todoTable.UpdateAsync(item);

	In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile. 

Una volta aggiornata l'app per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.<!--HONumber=42-->
