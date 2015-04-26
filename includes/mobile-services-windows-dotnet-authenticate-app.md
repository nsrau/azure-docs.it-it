
1. Aprire il file del progetto mainpage.xaml.cs e aggiungere l'istruzione using seguente:

        using Windows.UI.Popups;

2. Aggiungere il seguente frammento di codice alla classe MainPage:
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
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

    Verranno creati una variabile membro per archiviare l'utente corrente e un metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Facebook. Se si usa un provider di identità diverso da Facebook, sostituire il valore di **MobileServiceAuthenticationProvider** con il valore del provider.

3. Sostituire l'override del metodo **OnNavigatedTo** esistente con il seguente metodo che chiama il nuovo metodo **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
4. Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato. 

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.


<!--HONumber=42-->
