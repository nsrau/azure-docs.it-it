
##<a name="update-app"></a>Aggiornare l'app per chiamare l'API personalizzata

1. In Visual Studio aprire il file MainPage.xaml nel progetto di guida introduttiva, individuare l'elemento **Button** denominato `ButtonRefresh` e sostituirlo con il codice XAML seguente: 

		<StackPanel Orientation="Horizontal">
	        <Button Margin="72,0,0,0" Name="ButtonRefresh" 
	                Click="ButtonRefresh_Click">Refresh</Button>
	        <Button Margin="12,0,0,0" Name="ButtonCompleteAll" 
	                Click="ButtonCompleteAll_Click">Complete All</Button>
	    </StackPanel>

	Verrà aggiunto un nuovo pulsante nella pagina.

2. Aprire il file di codice MainPage.xaml.cs e aggiungere il codice di definizione di classe seguente:

	    public class MarkAllResult
	    {
	        public int Count { get; set; }
	    }

	Questa classe viene usata per contenere il valore del conteggio di righe restituito dall'API personalizzata.

3. Individuare il metodo **RefreshTodoItems** nella classe **MainPage** e assicurarsi che l'elemento `query` sia definito con il metodo **Where** seguente:

        .Where(todoItem => todoItem.Complete == false)

	Gli elementi verranno filtrati in modo che quelli completati non vengano restituiti dalla query.

3. Nella classe **MainPage** aggiungere il metodo seguente:

		private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
		{
		    string message;
		    try
		    {
		        // Asynchronously call the custom API using the POST method. 
		        var result = await App.MobileService
		            .InvokeApiAsync<MarkAllResult>("completeAll", 
		            System.Net.Http.HttpMethod.Post, null);
		        message =  result.Count + " item(s) marked as complete.";
		        RefreshTodoItems();
		    }
		    catch (MobileServiceInvalidOperationException ex)
		    {
		        message = ex.Message;                
		    }
		
		    var dialog = new MessageDialog(message);
		    dialog.Commands.Add(new UICommand("OK"));
		    await dialog.ShowAsync();
		}

	Questo metodo gestisce l'evento **Click** per il nuovo pulsante. Nel client viene chiamato il metodo [InvokeApiAsync](http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx), che invia una richiesta POST alla nuova API personalizzata. Il risultato restituito dall'API personalizzata viene visualizzato in una finestra di dialogo con messaggio, insieme a eventuali errori.

## <a name="test-app"></a>Testare l'app

1. In Visual Studio premere **F5** per ricompilare il progetto e avviare l'app.

2. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

3. Ripetere il passaggio precedente fino ad aggiungere diversi elementi todo all'elenco.

4. Fare clic sul pulsante **Complete All**.

  	![](./media/mobile-services-windows-store-dotnet-call-custom-api/mobile-custom-api-windows-store-completed.png)

	Verrà visualizzata una finestra di dialogo con messaggio indicante il numero di elementi contrassegnati come completati, quindi la query filtrata verrà eseguita di nuovo cancellando tutti gli elementi dall'elenco.

<!---HONumber=58_postMigration-->