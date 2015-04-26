
È ora necessario modificare la modalità di registrazione delle notifiche push per assicurarsi che l'utente sia autenticato prima che si provi a eseguire la registrazione. 

1. In Visual Studio, in Esplora soluzioni aprire il file di progetto app.xaml.cs e nel gestore eventi **Application_Launching** rimuovere i simboli di commento o eliminare la chiamata al metodo **AcquirePushChannel**. 
 
2. Modificare l'accessibilità del metodo **AcquirePushChannel** da  `private` a  `public` e aggiungere il modificatore  `static`. 

3. Aprire il file di progetto MainPage.xaml.cs e sostituire l'override del metodo **OnNavigatedTo** con il seguente:

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }


<!--HONumber=42-->
