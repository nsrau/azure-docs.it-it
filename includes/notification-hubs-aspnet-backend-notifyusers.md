## <a name="create-the-webapi-project"></a>Creare il progetto di API Web
Un nuovo back-end WebAPI ASP.NET verrà creato nelle sezioni che seguono e avrà tre scopi principali:

1. **Autenticazione dei client**: in seguito verrà aggiunto un gestore di messaggi per autenticare le richieste client e associare l'utente alla richiesta.
2. **Registrazioni delle notifiche client**: in seguito si aggiungerà un controllo per gestire le nuove registrazioni in modo che un dispositivo cliente riceva le notifiche. Il nome utente autenticato verrà aggiunto automaticamente alla registrazione come [tag](https://msdn.microsoft.com/library/azure/dn530749.aspx).
3. **Invio di notifiche ai client**: in seguito si aggiungerà anche un controller per fornire all'utente un modo per attivare un push sicuro ai dispositivi e ai client associati al tag. 

I passaggi seguenti mostrano come creare un nuovo back-end WebAPI ASP.NET: 

> [!NOTE]
> **Importante**: prima di procedere con l'esercitazione, verificare di aver installato l'ultima versione di Gestione pacchetti NuGet. A questo scopo, avviare Visual Studio. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**. Cercare **Gestione pacchetti NuGet per Visual Studio 2013**e verificare che sia installata la versione 2.8.50313.46 o successiva. In caso contrario, disinstallare e quindi reinstallare Gestione pacchetti NuGet.
> 
> ![][B4]
> 
> [!NOTE]
> Assicurarsi che sia installato Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) per la distribuzione del sito Web.
> 
> 

1. Avviare Visual Studio o Visual Studio Express. Fare clic su **Esplora server** e accedere all'account Azure. Per la creazione delle risorse del sito Web nell'account, in Visual Studio è necessario eseguire l'accesso.
2. In Visual Studio fare clic su **File**, **Nuovo** e quindi **Progetto**, espandere **Modelli** e **Visual C#**, quindi fare clic su **Web** e **Applicazione Web ASP.NET**, digitare il nome **AppBackend** e infine fare clic su **OK**. 
   
    ![][B1]
3. Nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **API Web** e quindi su **OK**.
   
    ![][B2]
4. Nella finestra di dialogo **Configura app Web di Microsoft Azure** scegliere una sottoscrizione e un **piano di servizio app** già creato. È inoltre possibile scegliere **Crea un nuovo piano di servizio app** e crearne uno dalla finestra di dialogo. Per questa esercitazione non è necessario disporre di un database. Dopo aver selezionato il piano di servizio app, fare clic su **OK** per creare il progetto.
   
    ![][B5]

## <a name="authenticating-clients-to-the-webapi-backend"></a>Autenticazione di client al back-end WebAPI
In questa sezione si creerà una nuova classe del gestore di messaggi denominata **AuthenticationTestHandler** per il nuovo back-end. Questa classe è derivata da [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) e viene aggiunta come gestore di messaggi per poter elaborare tutte le richieste in arrivo nel back-end. 

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AppBackend**, scegliere **Aggiungi** e quindi fare clic su **Classe**. Assegnare alla nuova classe il nome **AuthenticationTestHandler.cs** e fare clic su **Aggiungi** per generarla. Questa classe viene usata per semplicità per autenticare gli utenti mediante l' *autenticazione di base* . Tenere presente che l'app può usare qualsiasi schema di autenticazione.
2. In AuthenticationTestHandler.cs aggiungere le istruzioni `using` seguenti:
   
        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Web;
3. In AuthenticationTestHandler.cs sostituire la definizione di classe `AuthenticationTestHandler` con il codice seguente: 
   
    Questo gestore autorizza la richiesta quando le tre seguenti condizioni sono tutte vere:
   
   * La richiesta include un'intestazione *Autorizzazione* . 
   * La richiesta usa l'autenticazione *di base* . 
   * La stringa del nome utente corrisponde alla stringa della password.
     
     In caso contrario, la richiesta verrà rifiutata. Non si tratta di un vero approccio di autenticazione e autorizzazione. È un esempio molto semplice per questa esercitazione.
     
     Se il messaggio di richiesta viene autenticato e autorizzato da `AuthenticationTestHandler`, l'utente di autenticazione di base verrà associato alla richiesta corrente in [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Le informazioni utente in HttpContext verranno usate da un altro controller (RegisterController) in un secondo momento per aggiungere un [tag](https://msdn.microsoft.com/library/azure/dn530749.aspx) per la richiesta di registrazione della notifica.
     
       public class AuthenticationTestHandler : DelegatingHandler   {
     
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
                   else return Unauthorized();
               }
               else return Unauthorized();
     
               return base.SendAsync(request, cancellationToken);
           }
     
           private bool verifyUserAndPwd(string user, string password)
           {
               // This is not a real authentication scheme.
               return user == password;
           }
     
           private Task<HttpResponseMessage> Unauthorized()
           {
               var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
               var tsc = new TaskCompletionSource<HttpResponseMessage>();
               tsc.SetResult(response);
               return tsc.Task;
           }
       }
     
     > [!NOTE]
     > **Nota sulla sicurezza**: la classe `AuthenticationTestHandler` non fornisce un'effettiva autenticazione. Viene usata solo per imitare l'autenticazione di base e non è sicura. È necessario implementare un meccanismo di autenticazione sicuro nelle applicazioni e nei servizi di produzione.                
     > 
     > 
4. Aggiungere il codice seguente alla fine del metodo `Register` nella classe **App_Start/WebApiConfig.cs** per registrare il gestore di messaggi:
   
        config.MessageHandlers.Add(new AuthenticationTestHandler());
5. Salvare le modifiche.

## <a name="registering-for-notifications-using-the-webapi-backend"></a>Registrazione per le notifiche tramite il back-end WebAPI
In questa sezione si aggiungerà un nuovo controller al back-end WebAPI per gestire le richieste per la registrazione di un utente e un dispositivo per le notifiche tramite la libreria client per gli hub di notifica. Il controller aggiungerà un tag user per l'utente che è stato autenticato e collegato a HttpContext da `AuthenticationTestHandler`. Il tag avrà il formato della stringa, `"username:<actual username>"`.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **AppBackend** e quindi scegliere **Gestisci pacchetti NuGet**.
2. Sul lato sinistro fare clic su **Online** e cercare **Microsoft.Azure.NotificationHubs** nella casella di **ricerca**.
3. Nell'elenco risultati fare clic su **Hub di notifica di Microsoft Azure** e quindi su **Installa**. Completare l'installazione e chiudere la finestra di Gestione pacchetti NuGet.
   
    Verrà aggiunto un riferimento all’SDK dell’Hub di notifica di Azure mediante il <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacchetto NuGet Microsoft.Azure.NotificationHubs</a>.
4. Ora verrà creato un nuovo file della classe che rappresenta la connessione con l'hub di notifica usato per inviare le notifiche. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Modelli**, scegliere **Aggiungi** e quindi fare clic su **Classe**. Assegnare alla nuova classe il nome **Notifications.cs** e quindi fare clic su **Aggiungi** per generarla. 
   
    ![][B6]
5. In Notifications.cs aggiungere l'istruzione `using` seguente all'inizio del file:
   
        using Microsoft.Azure.NotificationHubs;
6. Sostituire la definizione di classe `Notifications` con il codice seguente e assicurarsi di sostituire i due segnaposto con la stringa di connessione (con accesso completo) per l'hub di notifica e con il nome dell'hub (disponibile nel [portale di Azure classico](http://manage.windowsazure.com)):
   
        public class Notifications
        {
            public static Notifications Instance = new Notifications();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }
7. Verrà quindi creato un nuovo controller denominato **RegisterController**. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi** e quindi fare clic su **Controller**. Fare clic sull'elemento **Controller Web API 2 - Vuoto** e quindi su **Aggiungi**. Assegnare alla nuova classe il nome **RegisterController** e quindi fare di nuovo clic su **Aggiungi** per generare il controller.
   
    ![][B7]
   
    ![][B8]
8. In RegisterController.cs aggiungere le istruzioni `using` seguenti:
   
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
9. Aggiungere il codice seguente all'interno della definizione di classe `RegisterController` . Si noti che in questo codice viene aggiunto un tag user per l'utente associato a HttpContext. L'utente è stato autenticato e associato a HttpContext dal filtro messaggi aggiunto, `AuthenticationTestHandler`. È anche possibile aggiungere controlli facoltativi per verificare che l'utente disponga dei diritti per la registrazione per i tag richiesti.
   
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
            string newRegistrationId = null;
   
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
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
   
            if (newRegistrationId == null) 
                newRegistrationId = await hub.CreateRegistrationIdAsync();
   
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
10. Salvare le modifiche.

## <a name="sending-notifications-from-the-webapi-backend"></a>Invio di notifiche dal back-end WebAPI
In questa sezione si aggiungerà un nuovo controller che espone un modo per consentire ai dispositivi client di inviare una notifica in base al tag username usando la libreria di gestione del servizio Hub di notifica di Azure nel back-end WebAPI ASP.NET.

1. Creare un altro nuovo controller denominato **NotificationsController**. Crearlo seguendo la stessa procedura usata per creare **RegisterController** nella sezione precedente.
2. In NotificationsController.cs aggiungere le istruzioni `using` seguenti:
   
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
3. Aggiungere il codice seguente alla classe **NotificationsController** .
   
    Questo codice invia un tipo di notifica basato sul parametro `pns` (Platform Notification Service). Il valore `to_tag` viene usato per impostare il tag *username* nel messaggio. Questo tag deve corrispondere a un tag username di una registrazione dell'hub di notifica attiva. Il messaggio di notifica viene estratto dal corpo della richiesta POST ed è formattato per il PNS di destinazione. 
   
    A seconda del PNS (Platform Notification Service) usato dai dispositivi supportati per ricevere le notifiche, sono supportate notifiche diverse con formati diversi. Ad esempio nei dispositivi Windows è possibile usare una [notifica di tipo avviso popup con WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) che non è supportata direttamente da un altro PNS. Il back-end deve pertanto formattare la notifica come una notifica supportata per il PNS dei dispositivi che si intende supportare. Usare quindi l'API di invio appropriata per la [classe NotificationHubClient](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)
   
        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;
   
            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;
   
            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }
   
            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }
   
            return Request.CreateResponse(ret);
        }
4. Premere **F5** per eseguire l'applicazione e verificare l'accuratezza del lavoro fino a questo punto. L'app dovrebbe avviare un Web browser e visualizzare la home page di ASP.NET. 

## <a name="publish-the-new-webapi-backend"></a>Pubblicare il nuovo back-end WebAPId
1. L'app verrà ora distribuita in un sito Web Azure in modo da renderla accessibile da tutti i dispositivi. Fare clic con il pulsante destro del mouse sul progetto **AppBackend** e scegliere **Pubblica**.
2. Selezionare **App Web di Microsoft Azure** come destinazione di pubblicazione.
   
    ![][B15]
3. Accedere con l'account Azure e selezionare un'app Web nuova o esistente.
   
    ![][B16]
4. Prendere nota della proprietà **URL di destinazione** nella scheda **Connessione**. Si farà riferimento a quest'URL come *endpoint back-end* più avanti in questa esercitazione. Fare clic su **Pubblica**.
   
    ![][B18]

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG


<!--HONumber=Nov16_HO3-->


