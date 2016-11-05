1. Aprire il file di progetto mainpage.xaml.cs e aggiungere il frammento di codice seguente alla classe MainPage:
   
        private MobileServiceUser user;
        private async Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileServiceDotNetClient.LoginAsync(MobileServiceAuthenticationProvider.Twitter);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
   
                var dialog = new MessageDialog(message);
                await dialog.ShowAsync();
            }
        }
   
    Verranno creati una variabile membro per archiviare l'utente corrente e un metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Twitter.
   
   > [!NOTE]
   > Se si usa un provider di identità diverso da Twitter, sostituire il valore di <strong>MobileServiceAuthenticationProvider</strong> con il nome del provider.</p> </div>
   > 
   > 
2. Eliminare o impostare come commento l'override del metodo **OnNavigatedTo** esistente e sostituirlo con il metodo seguente che gestisce l'evento **Loaded** per la pagina.
   
        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }
   
       Questo metodo chiama il nuovo metodo **Authenticate**.
3. Sostituire il costruttore MainPage con il codice seguente:
   
        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }
   
       Questo costruttore registra inoltre il gestore per l'evento Loaded.
4. Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato.
   
       Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

<!---HONumber=AcomDC_0211_2016-->