
Nell'esempio precedente è stato illustrato un accesso standard, che richiede al client di contattare sia il provider di identità sia il servizio mobile ogni volta che l'app viene avviata. Non solo questo metodo è inefficiente, ma si potrebbero riscontrare problemi relativi all'uso qualora molti clienti provassero ad avviare l'app contemporaneamente. Un miglior approccio consiste nel memorizzare nella cache il token di autorizzazione restituito dai Servizi mobili e provare a usare questo prima di usare un accesso basato su provider.

>[AZURE.NOTE]È possibile memorizzare nella cache il token rilasciato dai Servizi mobili indipendentemente dal fatto che si usi l'autenticazione gestita dal client o gestita dal servizio. In questa esercitazione viene usata l'autenticazione gestita dal servizio.

1. Nel file di progetto default.js sostituire la funzione **Login** esistente con il codice seguente:

        var credential = null;
        var vault = new Windows.Security.Credentials.PasswordVault();

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    // Create a credential for the returned user.
                    credential = new Windows.Security.Credentials
                        .PasswordCredential("Facebook", results.userId,
                        results.mobileServiceAuthenticationToken);
                    vault.add(credential);
                    userId = results.userId;

                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }

2. Sostituire la funzione **authenticate** esistente con il codice seguente:

        var authenticate = function () {
            // Try to get a stored credential from the PasswordVault.                
            try{
                credential = vault.findAllByResource("Facebook").getAt(0);
            }
            catch (error) {
                // This is expected when there's no stored credential.
            }
            
            if (credential) {
                // Set the user from the returned credential.   
                credential.retrievePassword();
                client.currentUser = {
                    "userId": credential.userName,
                    "mobileServiceAuthenticationToken": credential.password
                };

                // Try to return an item now to determine if the cached credential has expired.
                todoTable.take(1).read()
                            .done(function () {
                                refreshTodoItems();
                            }, function (error) {
                                if (error.request.status === 401) {
                                    login(credential, vault).then(function () {
                                        if (!credential) {

                                            // Authentication failed, try again.
                                            authenticate();
                                        }
                                    });
                                }                                   
                            });
            } else {

                login().then(function () {
                    if (!credential) {
                        // Authentication failed, try again.
                        authenticate();
                    }
                });
            }
        }

	In questa versione di **authenticate** l'app tenta di usare le credenziali archiviate in **PasswordVault** per accedere al servizio mobile. Viene inviata una semplice query per verificare che il token archiviato non sia scaduto. Quando viene restituito 401, viene effettuato un normale tentativo di accesso basato su provider. Un accesso normale viene eseguito anche quando non sono disponibili credenziali archiviate.

3. Riavviare l'app due volte.

	Si noti che al primo avvio viene di nuovo richiesto l'accesso con il provider. Al secondo riavvio, invece, verranno usate le credenziali memorizzate nella cache e l'accesso sarà ignorato.

<!---HONumber=Oct15_HO3-->