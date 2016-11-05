
1. In Esplora soluzioni di Visual Studio espandere la cartella App\_Start e aprire il file di progetto WebApiConfig.cs.
2. Aggiungere la riga di codice seguente al metodo Register dopo la definizione **ConfigOptions**:
   
        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;
   
    In questo modo l'autenticazione utente viene applicata prima della registrazione delle notifiche push.
3. Fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi**, quindi fare clic su **Classe**.
4. Assegnare un nome alla nuova classe vuota `PushRegistrationHandler` e quindi fare clic su **Aggiungi**.
5. Nella parte superiore della tabella codici aggiungere l'istruzione **using** seguente:
   
        using System.Threading.Tasks; 
        using System.Web.Http; 
        using System.Web.Http.Controllers; 
        using Microsoft.WindowsAzure.Mobile.Service; 
        using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
        using Microsoft.WindowsAzure.Mobile.Service.Security; 
6. Sostituire la classe **PushRegistrationHandler** esistente con il codice seguente:
   
        public class PushRegistrationHandler : INotificationHandler
        {
            public Task Register(ApiServices services, HttpRequestContext context,
            NotificationRegistration registration)
            {
                try
                {
                    // Perform a check here for user ID tags, which are not allowed.
                    if(!ValidateTags(registration))
                    {
                        throw new InvalidOperationException(
                            "You cannot supply a tag that is a user ID.");                    
                    }
   
                    // Get the logged-in user.
                    var currentUser = context.Principal as ServiceUser;
   
                    // Add a new tag that is the user ID.
                    registration.Tags.Add(currentUser.Id);
   
                    services.Log.Info("Registered tag for userId: " + currentUser.Id);
                }
                catch(Exception ex)
                {
                    services.Log.Error(ex.ToString());
                }
                    return Task.FromResult(true);
            }
   
            private bool ValidateTags(NotificationRegistration registration)
            {
                // Create a regex to search for disallowed tags.
                System.Text.RegularExpressions.Regex searchTerm =
                new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
                    System.Text.RegularExpressions.RegexOptions.IgnoreCase);
   
                foreach (string tag in registration.Tags)
                {
                    if (searchTerm.IsMatch(tag))
                    {
                        return false;
                    }
                }
                return true;
            }
   
            public Task Unregister(ApiServices services, HttpRequestContext context, 
                string deviceId)
            {
                // This is where you can hook into registration deletion.
                return Task.FromResult(true);
            }
        }
   
    Il metodo **Register** viene chiamato durante la registrazione. In questo modo è possibile aggiungere alla registrazione un tag corrispondente all'ID dell'utente connesso. I tag specificati vengono convalidati per evitare che un utente si registri con l'ID di un altro utente. Le notifiche inviate saranno ricevute dall'utente su questo e su qualsiasi altro dispositivo registrato dall'utente stesso.
7. Espandere la cartella Controller, aprire il file di progetto TodoItemController.cs, individuare il metodo **PostTodoItem** e sostituire la riga di codice che chiama **SendAsync** con il codice seguente:
   
        // Get the logged-in user.
        var currentUser = this.User as ServiceUser;
   
        // Use a tag to only send the notification to the logged-in user.
        var result = await Services.Push.SendAsync(message, currentUser.Id);
8. Ripubblicare il progetto di servizio mobile.

A questo punto il servizio usa il tag di ID utente per inviare una notifica push (con il testo dell'elemento inserito) a tutte le registrazioni di app di Windows Store create dall'utente connesso.

<!---HONumber=Oct15_HO3-->