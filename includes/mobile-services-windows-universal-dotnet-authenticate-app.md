1.  Aprire il file del progetto MainPage.cs condiviso e aggiungere l'istruzione using seguente:

        using Windows.UI.Popups;

2.  Aggiungere il seguente frammento di codice alla classe MainPage:

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

    Questo utente viene autenticato tramite un account di accesso di Facebook. Se si utilizza un provider di identità diverso da Facebook, sostituire il valore di **MobileServiceAuthenticationProvider** con il nome del provider.

3.  Impostare come commento o eliminare la chiamata al metodo **RefreshTodoItems** nell'override del metodo **OnNavigatedTo** esistente.

    Ciò impedisce il caricamento dei dati prima dell'autenticazione dell'utente.

    > [WACOM.NOTE]Per eseguire correttamente l'autenticazione da un'app di Windows Phone Store 8.1, è necessario chiamare LoginAsync dopo che è il metodo **OnNavigated** è stato chiamato e dopo che l'evento **Loaded** è stato generato. In questa esercitazione tale operazione viene svolta tramite l'aggiunta di un pulsante **Sign in** all'app.

4.  Aggiungere il seguente frammento di codice alla classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            RefreshTodoItems();
        }

5.  Nel progetto dell'app di Windows Store, aprire il file di progetto MainPage.xaml e aggiungere il seguente elemento **Button** giusto prima dell'elemento che definisce il pulsante **Save**:

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6.  Ripetere il passaggio precedente per il progetto dell'app di Windows Phone Store, questa volta aggiungendo l'elemento **Button** in **TitlePanel**, dopo l'elemento **TextBlock**.

7.  Aprire il file del progetto App.xaml.cs condiviso e aggiungere l'istruzione using seguente, se non è già presente:

        using Microsoft.WindowsAzure.MobileServices;  

8.  Nel file di progetto App.xaml.cs aggiungere il codice seguente:

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

9.  Premere il tasto F5 per eseguire l'app di Windows Store, fare clic sul pulsante **Sign in** e accedere all'app con il provider di identità desiderato.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

10. Fare clic con il pulsante destro del mouse sul progetto dell'app di Windows Phone Store, selezionare **Imposta come progetto di avvio**, quindi ripetere il passaggio precedente per verificare la corretta esecuzione dell'app di Windows Phone Store.


