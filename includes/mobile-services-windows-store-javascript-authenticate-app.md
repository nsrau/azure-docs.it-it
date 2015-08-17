

1. Aprire il file di progetto default.js e nell'overload del metodo **app.OnActivated** sostituire l'ultima chiamata al metodo **refreshTodoItems** con il codice seguente: 
	
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }            

        var authenticate = function () {
            login().then(function () {
                if (userId === null) {

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    Verranno creati una variabile membro per archiviare l'utente corrente e un metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Facebook. Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo <strong>login</strong> riportato in precedenza con uno dei seguenti: _microsoftaccount_, _twitter_, _google_ o _windowsazureactivedirectory_.

    >[AZURE.NOTE]Se le informazioni del pacchetto dell'app di Windows Store sono state registrate con Servizi mobili, è necessario chiamare il metodo <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> specificando il valore <strong>true</strong> per il parametro <em>useSingleSignOn</em>. In caso contrario, gli utenti dovranno comunque specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso.

2. Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato.

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

<!---HONumber=August15_HO6-->