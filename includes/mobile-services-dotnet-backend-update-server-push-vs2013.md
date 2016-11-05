Nella seguente procedura viene illustrato come creare un'[ApiController](http://go.microsoft.com/fwlink/p/?LinkId=512673) personalizzato che invia le notifiche push all'app. È possibile implementare questo stesso codice in un [TableController](http://msdn.microsoft.com/library/azure/dn643359.aspx) o in qualsiasi altro punto dei propri servizi di back-end.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla cartella Controllers relativa al progetto di servizio mobile, espandere **Add**, quindi fare clic su **New Scaffolded Item**.
   
    Verrà visualizzata la finestra di dialogo Add Scaffold.
2. Espandere **Servizi mobili di Azure**, fare clic su **Controller personalizzato di Servizi mobili di Microsoft Azure**, quindi fare clic su **Aggiungi**, specificare **Nome controller** `NotifyAllUsersController` e fare di nuovo clic su **Aggiungi**.
   
    ![Finestra di dialogo Add Scaffold dell'API Web](./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png)
   
    Verrà creata una nuova classe di controller vuota denominata **NotifyAllUsersController**.
3. Nel nuovo file di progetto NotifyAllUsersController.cs aggiungere le istruzioni **using** seguenti:
   
        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;
4. Aggiungere il codice seguente per implementare il metodo POST sul controller:
   
        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
                // that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version="1.0" encoding="utf-8"?>" +
                    "<toast><visual><binding template="ToastText01">" + 
                    "<text id="1">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());
   
                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;
   
                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }
   
   > [!NOTE]
   > Poiché questo metodo POST può essere chiamato da qualsiasi client che dispone della chiave dell'applicazione, si tratta di una procedura poco sicura. Per proteggere l'endpoint, applicare l'attributo `[AuthorizeLevel(AuthorizationLevel.User)]` al metodo o alla classe per richiedere l'autenticazione.
   > 
   > 

<!---HONumber=Oct15_HO3-->