## Creare il progetto di API Web

Seguire questi passaggi per creare un nuovo back-end WebAPI ASP.NET per autenticare i client e generare le notifiche o modificare un back-end esistente da progetti precedenti o dall'esercitazione [Inviare notifiche push agli utenti autenticati](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/).

> [AZURE.NOTE] **Importante**: prima di procedere con l'esercitazione, verificare di aver installato l'ultima versione di Gestione pacchetti NuGet. A questo scopo, avviare Visual Studio. Nel menu **Strumenti** fare clic su **Estensioni e aggiornamenti**. Cercare **Gestione pacchetti NuGet per Visual Studio 2013** e verificare che sia installata la versione 2.8.50313.46 o successiva. In caso contrario, disinstallare e quindi reinstallare Gestione pacchetti NuGet.
> 
> ![][4]

> [AZURE.NOTE] Assicurarsi che sia installato Visual Studio [Azure SDK](http://azure.microsoft.com/downloads/) per la distribuzione del sito Web.

1. Avviare Visual Studio o Visual Studio Express.
2. In Visual Studio fare clic su **File**, su **Nuovo** e quindi su **Progetto**, espandere **Modelli**, **Visual C#**, quindi fare clic su **Web** e **Applicazione Web ASP.NET**, digitare il nome **AppBackend** e fare clic su **OK**. 
	
	![][1]

3. Nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **API Web**, quindi su **OK**.

	![][2]

4. Nella finestra di dialogo di **configurazione del sito Web Azure** selezionare una sottoscrizione, un'area e un database da usare per il progetto. Fare clic su **OK** per creare il progetto.

	![][5]

5. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AppBackend**, quindi scegliere **Gestisci pacchetti NuGet**.

6. Nella parte sinistra fare clic su **Online** e cercare **servicebus** usando la casella **Cerca**.

7. Nell'elenco risultati fare clic su **Bus di servizio di Microsoft Azure** e quindi su **Installa**. Completare l'installazione e chiudere la finestra di Gestione pacchetti NuGet.

	![][14]

8. Verrà ora creata una nuova classe **Notifications.cs**. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Modelli**, quindi scegliere **Aggiungi** e **Classe**. Dopo avere rinominato la nuova classe in **Notifications.cs**, fare clic su **Aggiungi** per generare la classe. Questo modulo rappresenta le diverse notifiche sicure che verranno inviate. In un'implementazione completa, le notifiche vengono archiviate in un database. Per semplicità, in questa esercitazione verrà archiviata in memoria.

	![][6]

9. In Notifications.cs aggiungere l'istruzione `using` all'inizio del file:

        using Microsoft.ServiceBus.Notifications;

10. Sostituire la definizione della classe `Notifications` con il seguente codice e assicurarsi di sostituire i due segnaposto con la stringa di connessione (con accesso completo) per l'hub di notifica e con il nome dell'hub (disponibile nel [portale di gestione di Azure](http://manage.windowsazure.com)):

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Verrà quindi creata una nuova classe **AuthenticationTestHandler.cs**. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AppBackend**, quindi scegliere **Aggiungi** e infine **Classe**. Assegnare alla nuova classe il nome **AuthenticationTestHandler.cs**, quindi fare clic su **Aggiungi** per generarla. Questa classe viene usata per autenticare gli utenti mediante l' *Basic Authentication*. Tenere presente che l'app può usare qualsiasi schema di autenticazione.

12. In AuthenticationTestHandler.cs aggiungere le seguenti istruzioni `using`:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;

13. In AuthenticationTestHandler.cs sostituire la classe `AuthenticationTestHandler` con il seguente codice:

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorised();
	            }
	            else return Unauthorised();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorised()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Nota sulla sicurezza**: la classe `AuthenticationTestHandler` non fornisce un'effettiva autenticazione. Viene usata solo per imitare l'autenticazione di base e non è sicura. È necessario implementare un meccanismo di autenticazione sicuro nelle applicazioni e nei servizi di produzione.				

14. Aggiungere il seguente codice alla fine del metodo `Register` nella classe **App_Start/WebApiConfig.cs**:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. Verrà quindi creato un nuovo controller **RegisterController**. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi**, quindi **Controller**. Fare clic sull'elemento **Web API 2 Controller -- Empty** e quindi su **Aggiungi**. Assegnare alla nuova classe il nome **RegisterController** e fare di nuovo clic su **Aggiungi** per generare il controller.

	![][7]

	![][8]

16. In RegisterController.cs aggiungere le seguenti istruzioni `using`:

        using Microsoft.ServiceBus.Notifications;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

17. Aggiungere il seguente codice all'interno della definizione della classe `RegisterController`. Notare che in questo codice è stato aggiunto il tag per l'utente autenticato dal gestore. È anche possibile aggiungere controlli facoltativi per verificare che l'utente disponga dei diritti per la registrazione per i tag richiesti.

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

18. Creare un nuovo controller **NotificationsController**, seguendo la procedura per la creazione di **RegisterController**. Questo componente espone un metodo per il recupero sicuro della notifica da parte del dispositivo e fornisce un metodo che consente all'utente di avviare un'operazione di push sicuro ai dispositivi. Notare che a Hub di notifica verrà inviata una notifica non elaborata, che contiene solo l'ID della notifica senza alcun messaggio.

19. In NotificationsController.cs aggiungere le seguenti istruzioni `using`:

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

20. Aggiungere il seguente codice all'interno della definizione della classe **NotificationsController** e assicurarsi di impostare come commento i frammenti di codice relativi alle piattaforme che non si stanno usando.

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;


            // windows
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);


            // apns
            var alert = "{\"aps\":{\"alert\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);


            // gcm
            var notif = "{ \"data\" : {\"msg\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);


            return Request.CreateResponse(HttpStatusCode.OK);
        }

21. Premere **F5** per eseguire l'applicazione e verificare l'accuratezza del lavoro fino a questo punto. L'app dovrebbe avviare un Web browser e visualizzare la home page di ASP.NET. 

22. L'app verrà ora distribuita in un sito Web Azure in modo da renderla accessibile da tutti i dispositivi. Fare clic con il pulsante destro del mouse sul progetto **AppBackend** e scegliere **Pubblica**.

23. Selezionare un sito Web Azure come destinazione di pubblicazione.

    ![][B15]

24. Accedere con l'account Azure e selezionare un sito Web nuovo o esistente.

    ![][B16]

25. Prendere nota della proprietà **URL di destinazione** nella scheda **Connessione**. Si farà riferimento a questo URL come *backend endpoint* più avanti in questa esercitazione. Fare clic su **Pubblica**.

    ![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!--HONumber=45--> 
