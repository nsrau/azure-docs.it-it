

1. Aprire il file di progetto default.js e nell'overload del metodo **app.OnActivated** sostituire l'ultima chiamata al metodo **refreshTodoItems** con il codice seguente: 

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

	Se si usa un provider di identità diverso da Facebook, sostituire il valore passato al metodo <strong>login</strong> riportato in precedenza con uno dei seguenti: _microsoftaccount_, _twitter_, _google_ o _windowsazureactivedirectory_.

    >[AZURE.NOTE]Se le informazioni del pacchetto dell'app di Windows Store sono state registrate con Servizi mobili, è necessario chiamare il metodo <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> specificando il valore <strong>true</strong> per il parametro <em>useSingleSignOn</em>. In caso contrario, gli utenti dovranno comunque specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso.

2. Nel progetto dell'app di Windows Store, aprire il file di progetto default.html e aggiungere il seguente elemento **button** esattamente prima dell'elemento che definisce il pulsante **save**:

      	<button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3. Premere F5 per eseguire l'app e accedervi con il provider di identità desiderato.

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

<!---HONumber=July15_HO4-->