## Creare il progetto di API Web

Il primo passaggio consiste nel creare un progetto di API Web ASP.NET. Si tratta del back-end che verrà usato per autenticare i client e generare le notifiche.

> [AZURE.NOTE] **Importante**: prima di procedere con l'esercitazione, verificare di aver installato l'ultima versione di Gestione pacchetti NuGet. A questo scopo, avviare Visual Studio. Nel menu **Strumenti** fare clic su **Estensioni e aggiornamenti**. Cercare **Gestione pacchetti NuGet per Visual Studio 2013** e verificare che sia installata la versione 2.8.50313.46 o successiva. In caso contrario, disinstallare e quindi reinstallare Gestione pacchetti NuGet.
>
> ![][ ]

1.  Avviare Visual Studio con privilegi elevati (eseguire come amministratore).
2.  In Visual Studio o Visual Studio Express fare clic su **File**, su **Nuovo** e quindi su **Progetto**, espandere **Modelli**, **Visual C\#**, quindi fare clic su **Web** e **Applicazione Web ASP.NET**, digitare il nome **AppBackend** e quindi fare clic su **OK**.

    ![][1]

3.  Nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **API Web** e quindi su **OK**.

    ![][2]

4.  Nella finestra di dialogo di configurazione del sito Web Azure selezionare una sottoscrizione, un'area e un database da usare per il progetto. Fare clic su **OK** per creare il progetto.

    ![][3]

5.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AppBackend**, quindi scegliere **Gestisci pacchetti NuGet**.

6.  Sul lato sinistro fare clic su **Online**.

7.  Nella casella di ricerca digitare **servicebus**.

8.  Nell'elenco risultati fare clic su **Windows Azure Service Bus** e quindi su **Installa**. Completare l'installazione e chiudere la finestra di Gestione pacchetti NuGet.

    ![][4]

9.  In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Modelli**, quindi scegliere **Aggiungi** e infine **Classe**. Assegnare alla nuova classe il nome **Notifications.cs**. Fare clic su **Aggiungi** per generare la classe. Questo modulo rappresenta le diverse notifiche sicure che verranno inviate. In un'implementazione completa, le notifiche vengono archiviate in un database. In questo caso, per semplicità verranno archiviate in memoria.

    ![][5]

10. Aggiungere codice a Notifications.cs, sostituendo la definizione della classe `Notifications` con quanto segue:

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Aggiungere l'istruzione `using` seguente all'inizio del file:

        using Microsoft.ServiceBus.Notifications;

12. Nel metodo `Notifications()` sostituire i due segnaposto nella riga di codice seguente con la stringa di connessione (con accesso completo) all'hub di notifica e il nome dell'hub. È possibile ottenere questi valori nel [portale di gestione di Azure][portale di gestione di Azure]:

        Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");

13. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AppBackend**, quindi scegliere **Aggiungi** e infine **Classe**. Assegnare alla nuova classe il nome **AuthenticationTestHandler.cs**. Fare clic su **Aggiungi** per generare la classe. Questa classe viene usata per autenticare gli utenti mediante l'*Autenticazione di base*. Tenere presente che l'app può usare qualsiasi schema di autenticazione.

14. Aggiungere codice ad AuthenticationTestHandler.cs, sostituendo la definizione della classe `AuthenticationTestHandler` con quanto segue:

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

    > [AZURE.NOTE] **Nota sulla sicurezza**: la classe `AuthenticationTestHandler` non fornisce un'effettiva autenticazione. Viene utilizzata solo per imitare l'autenticazione di base e restituire un principio. Per creare le registrazioni di Hub di notifica, è necessario il nome utente. L'implementazione precedente non è sicura. È necessario implementare un meccanismo di autenticazione sicuro nelle applicazioni e nei servizi di produzione.

15. Aggiungere le istruzioni `using` seguenti all'inizio del file AuthenticationTestHandler.cs:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;               

16. Aggiungere il codice seguente alla fine del metodo `Register` nella classe **App\_Start/WebApiConfig.cs**:

        config.MessageHandlers.Add(new AuthenticationTestHandler());

17. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi**, quindi **Controller**. Fare clic sull'elemento **Web API 2 Controller -- Empty** e quindi fare clic su **Aggiungi**.

    ![][6]

18. Assegnare alla nuova classe il nome **RegisterController** e fare di nuovo clic su **Aggiungi** per generare il controller.

    ![][7]

19. Aggiungere il codice seguente all'interno della definizione della classe `RegisterController`:

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

20. Aggiungere le istruzioni `using` seguenti all'inizio del file RegisterController.cs:

        using Microsoft.ServiceBus.Notifications;
        using SecurePush.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

21. Notare che in questo codice è stato aggiunto il tag per l'utente autenticato dal gestore. È anche possibile aggiungere controlli facoltativi per verificare che l'utente disponga dei diritti per la registrazione per i tag richiesti.

22. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi**, quindi **Controller**. Fare clic sull'elemento **Web API 2 Controller -- Empty** e quindi fare clic su **Aggiungi**. Assegnare alla nuova classe il nome **NotificationsController** e fare di nuovo clic su **Aggiungi** per generare il controller. Questo componente espone un modo sicuro per il recupero della notifica da parte del dispositivo. Inoltre, ai fini di questa esercitazione, fornisce all'utente un modo per attivare un push sicuro ai propri dispositivi. Notare che a Hub di notifica verrà inviata una notifica non elaborata, che contiene solo l'ID della notifica senza alcun messaggio.

23. Aggiungere il codice seguente all'interno della definizione della classe **NotificationsController**:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);

        }

24. Aggiungere le istruzioni `using` seguenti all'inizio del file NotificationsController.cs:

        using SecurePush.Models;
        using System.Threading.Tasks;
        using System.Web;

25. Premere **F5** per eseguire l'applicazione e verificare l'accuratezza del lavoro fino a questo punto. L'app dovrebbe avviare un Web browser e visualizzare la home page di ASP.NET.

26. L'app verrà ora distribuita in un sito Web Azure in modo da renderla accessibile da tutti i dispositivi. Fare clic con il pulsante destro del mouse sul progetto **AppBackend** e scegliere **Pubblica**.

27. Selezionare un sito Web Azure come destinazione di pubblicazione.

    ![][8]

28. Accedere con l'account Azure e selezionare un sito Web nuovo o esistente.

    ![][9]

29. Prendere nota della proprietà **URL di destinazione** nella scheda **Connessione**. Si farà riferimento a quest'URL come *endpoint back-end* più avanti in questa esercitazione. Fare clic su **Pubblica**.

    ![][10]

  [ ]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
  [1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
  [2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
  [3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
  [4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
  [5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
  [7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
  [8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
  [9]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
  [10]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
