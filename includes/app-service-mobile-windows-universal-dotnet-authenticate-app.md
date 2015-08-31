
1. Aprire il file di progetto condiviso MainPage.cs e aggiungere il frammento di codice seguente alla classe MainPage:
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

		// Define a method that performs the authentication process
		// using a Facebook sign-in. 
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
					// Change 'MobileService' to the name of your MobileServiceClient instance.
					// Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    L'utente viene autenticato nel codice tramite un account di accesso di Facebook. Se si usa un provider di identità diverso da Facebook, sostituire il valore di **MobileServiceAuthenticationProvider** con il nome del provider.

3. Impostare come commento o eliminare la chiamata al metodo **RefreshTodoItems** nell'override del metodo **OnNavigatedTo** esistente.

	Ciò impedisce il caricamento dei dati prima dell'autenticazione dell'utente. A questo punto aggiungere un pulsante di **accesso** all'app che attiva l'autenticazione.

4. Aggiungere il seguente frammento di codice alla classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile app.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
5. Nel progetto dell'app di Windows Store, aprire il file di progetto MainPage.xaml e aggiungere il seguente elemento **Button** giusto prima dell'elemento che definisce il pulsante **Save**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Nel progetto di app Windows Phone Store, aggiungere l'elemento **Button** seguente in **ContentPanel** dopo l'elemento **TextBox**:

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
        	Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Aprire il file di progetto App.xaml.cs e aggiungere il codice seguente:

        protected override void OnActivated(IActivatedEventArgs args)
        {
			// Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
				// Completes the sign-in process started by LoginAsync.
				// Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

	Se il metodo **OnActivated** è già presente, aggiungere solamente il blocco di codice `#if...#endif`.

9. Premere il tasto F5 per eseguire l'app di Windows Store, fare clic sul pulsante **Sign in** e accedere all'app con il provider di identità desiderato.

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query nell'app mobile e aggiornare i dati.

10. Fare clic con il pulsante destro del mouse sul progetto dell'app di Windows Phone Store, selezionare **Imposta come progetto di avvio**, quindi ripetere il passaggio precedente per verificare la corretta esecuzione dell'app di Windows Phone Store.

<!---HONumber=August15_HO8-->