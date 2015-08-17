
È quindi necessario modificare la modalità di registrazione delle notifiche push, per assicurarsi che l'utente sia autenticato prima del tentativo di registrazione. Gli aggiornamenti dell'app client dipendono dalla modalità di implementazione delle notifiche push.

###Uso della procedura guidata Aggiungi notifica push in Visual Studio 2013 Update 2 o versione successiva

Con questo metodo la procedura guidata genera un nuovo file push.register.cs nel progetto.

1. In Esplora soluzioni di Visual Studio aprire il file di progetto app.xaml.cs e nel gestore eventi **OnLaunched** impostare come commento o eliminare la chiamata al metodo **UploadChannel**. 

2. Aprire il file di progetto push.register.cs e sostituire il metodo **UploadChannel** con il codice seguente:

		public async static void UploadChannel()
		{
		    var channel = 
		        await Windows.Networking.PushNotifications.PushNotificationChannelManager
		        .CreatePushNotificationChannelForApplicationAsync();
		
		    try
		    {
		        // Create a native push notification registration.
		        await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);		        
		
		    }
		    catch (Exception exception)
		    {
		        HandleRegisterException(exception);
		    }
		}

	In questo modo si garantisce che la registrazione venga effettuata usando la stessa istanza di client con le credenziali utente autenticate. In caso contrario la registrazione avrà esito negativo con un errore Non autorizzato (401).

3. Aprire il file di progetto MainPage.cs condiviso e sostituire il gestore **ButtonLogin\_Click** con il codice seguente:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
			todolistPush.UploadChannel();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }

	Ciò garantisce che l'autenticazione si verifichi prima che venga tentata la registrazione push.

4. 	Nel codice precedente è necessario sostituire il nome della classe push generato (`todolistPush`) con il nome della classe generato dalla procedura guidata, in genere nel formato <code><em>servizio\_mobile</em>Push</code>.

###Notifiche push abilitate manualmente		

Con questo metodo il codice di registrazione è stato aggiunto direttamente dall'esercitazione nel file di progetto app.xaml.cs.

1. In Esplora soluzioni di Visual Studio aprire il file di progetto app.xaml.cs e nel gestore eventi **OnLaunched** impostare come commento o eliminare la chiamata al metodo **InitNotificationsAsync**. 
 
2. Modificare l'accessibilità del metodo **InitNotificationsAsync** da `private` a `public` e aggiungere il modificatore `static`.

3. Aprire il file di progetto MainPage.cs condiviso e sostituire il gestore **ButtonLogin\_Click** con il codice seguente:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
			App.InitNotificationsAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
	
	Ciò garantisce che l'autenticazione si verifichi prima che venga tentata la registrazione push.

<!---HONumber=August15_HO6-->