<properties linkid="notification-hubs-how-to-guides-howto-notify-users-aspnet" urlDisplayName="Notify Users" pageTitle="Notify Users of your ASP.NET service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your ASP.NET service by using Notification Hubs" metaCanonical="" services="notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Utilizzo di Hub di notifica per inviare notifiche agli utenti
=============================================================

[Servizi mobili](/it-it/manage/services/notification-hubs/notify-users "Servizi mobili")[ASP.NET](/it-it/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

In questa esercitazione viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche, viene utilizzato un back-end di API Web ASP.NET. Questa esercitazione si basa sull'hub di notifica creato nell'esercitazione precedente **Introduzione ad Hub di notifica**. Il codice di registrazione della notifica viene spostato dal client al servizio back-end. In tal modo la registrazione verrà completata solo dopo la corretta autenticazione del client da parte del servizio e le credenziali dell'hub di notifica non verranno distribuite con l'app client. Il servizio controlla inoltre i tag richiesti durante la registrazione.

In questa esercitazione vengono descritte le operazioni di base seguenti:

-   [Creazione di un'applicazione ASP.NET con autenticazione](#create-application)
-   [Aggiornamento dell'applicazione ASP.NET per la registrazione per le notifiche](#register-notification)
-   [Aggiornamento dell'app per la registrazione dell'accesso e delle richieste](#update-app)

Prerequisiti
------------

-   Visual Studio 2012. Per creare l'applicazione ASP.NET e l'app di Windows Store, è inoltre possibile utilizzare rispettivamente Visual Studio Express 2012 per il Web e Visual Studio Express 2012 per Windows 8.
-   Questa esercitazione si basa sull'app e sull'hub di notifica creati in **Introduzione ad Hub di notifica**. Prima di iniziare questa esercitazione, è necessario completare l'esercitazione **Introduzione ad Hub di notifica** ([Windows Store C\#](/it-it/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/it-it/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/it-it/manage/services/notification-hubs/get-started-notification-hubs-android)).

**Nota**

Il progetto di API Web ASP.NET creato in questa esercitazione viene eseguito nel computer locale. È inoltre possibile pubblicare progetti di API Web ASP.NET in Azure. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un servizio REST per cellulari con l'API Web ASP.NET e Database SQL](/it-it/develop/net/tutorials/rest-service-using-web-api/).

Creazione dell'app ASP.NETCreazione di un'applicazione ASP.NET con autenticazione
---------------------------------------------------------------------------------

In primo luogo verrà creata un'applicazione API Web ASP.NET. Questo servizio back-end verrà utilizzato per autenticare i client, effettuare la registrazione per le notifiche push per conto di un utente autenticato e inviare le notifiche.

1.  In Visual Studio o Visual Studio Express 2012 per il Web, fare clic su **File** quindi scegliere **Nuovo progetto dal menu File, espandere **Modelli**, **Visual C\#**, quindi fare clic su **Web** e **Applicazione Web ASP.NET MVC 4**, immettere il nome *NotificationService* e infine fare clic su **OK**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png)

2.  Nella finestra di dialogo **Nuovo progetto ASP.NET MVC** fare clic su **Vuoto**, quindi su **OK**.

    Verrà quindi creato un progetto ASP.NET MVC.

3.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi **Classe**, digitare `AuthenticationTestHandler` e infine fare clic su **Aggiungi**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png)

    Al progetto verrà aggiunto un file di codice per la classe **AuthenticationTestHandler**.

4.  Aprire il nuovo file di progetto AuthenticationTestHandler.cs e sostituire la definizione della classe con il codice seguente:

         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Net;
         using System.Net.Http;
         using System.Threading;
         using System.Threading.Tasks;
         using System.Security.Principal;
         using System.Text;
         using System.Web;
            
         namespace NotificationService
         {
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
                     } else return Unauthorised();
            
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
         } 

    **Nota sulla sicurezza**

    La classe **AuthenticationTestHandler** non fornisce un'effettiva autenticazione. Viene utilizzata solo per imitare l'autenticazione di base e restituire un principio. Per creare le registrazioni di Hub di notifica, è necessario il nome utente. L'implementazione precedente non è sicura. È necessario implementare un meccanismo di autenticazione sicuro nelle applicazioni e nei servizi di produzione.

5.  Espandere la cartella **App\_Start**, aprire il file WebApiConfig.cs e aggiungere la riga di codice seguente alla fine del metodo **Register**:

         config.MessageHandlers.Add(new AuthenticationTestHandler());

    In questo modo le richieste effettuate all'applicazione ASP.NET dovranno contenere l'intestazione di autorizzazione.

A questo punto, è stata creata l'applicazione di base con uno schema di autenticazione fittizio per fornire un nome utente.

Registrazione per le notificheAggiornamento dell'applicazione ASP.NET per la registrazione per le notifiche
-----------------------------------------------------------------------------------------------------------

Il passaggio successivo consiste nel creare un nuovo controller **Registration** per aggiungere la logica di registrazione per gli hub di notifica all'applicazione ASP.NET.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) fare clic su **Service Bus**, sullo spazio dei nomi, su **Notification Hubs**, quindi selezionare l'hub di notifica e infine fare clic su **Connection Information**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png)

2.  Prendere nota del nome dell'hub di notifica e copiare la stringa di connessione per **DefaultFullSharedAccessSignature**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png)

    La stringa di connessione, unitamente al nome dell'hub di notifica, verranno utilizzati per effettuare la registrazione e inviare le notifiche.

3.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

4.  Nel riquadro sinistro selezionare la categoria **Online**, cercare `WindowsAzure.ServiceBus`, fare clic su **Installa** nel pacchetto di **Bus di servizio di Azure**, quindi accettare il contratto di licenza.

	![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png) 

	L'assembly Microsoft.ServiceBus.dll verrà aggiunto al progetto.

1.  In Esplora soluzioni fare clic sulla cartella **Controller**, quindi su **Aggiungi** e su **Controller**, digitare `RegisterController` in **Nome controller**, scegliere **Empty API controller** e infine fare clic su **Aggiungi**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png)

    Al progetto verrà aggiunta una classe controller. Quando chiamato, tale controller eseguirà la registrazione di un dispositivo con Hub di notifica.

2.  Aprire il nuovo file di progetto RegisterController.cs e aggiungere le istruzioni **using** seguenti.

         using Microsoft.ServiceBus.Notifications;
         using Newtonsoft.Json.Linq;
         using System.Threading.Tasks;
         using System.Web;

3.  Aggiungere il codice seguente nella nuova classe RegisterController:

         // Define the Notification Hubs client.
         private NotificationHubClient hubClient;

         // Create the client in the constructor.
         public RegisterController()
         {
             var cn = "<FULL_SAS_CONNECTION_STRING>";
             hubClient = NotificationHubClient
                 .CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
         }

4.  Aggiornare il codice nel costruttore in modo da sostituire *`<NOTIFICATION_HUB_NAME>`* e *`<FULL_SAS_CONNECTION_STRING>`* con i valori per l'hub di notifica, quindi fare clic su **Salva**.

    **Nota**

    Assicurarsi di utilizzare **DefaultFullSharedAccessSignature** per *`<FULL_SAS_CONNECTION_STRING>`*. Questa attestazione consentirà all'API Web di creare e aggiornare le registrazioni.

5.  Aggiungere il codice del metodo Post seguente nella classe RegisterController:

         public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
         {
             // Get the registration info that we need from the request. 
             var platform = registrationCall["platform"].ToString();
             var installationId = registrationCall["instId"].ToString();
             var channelUri = registrationCall["channelUri"] != null 
         
                 registrationCall["channelUri"].ToString() : null;
             var deviceToken = registrationCall["deviceToken"] != null 
         
                 registrationCall["deviceToken"].ToString() : null;       
             var userName = HttpContext.Current.User.Identity.Name;

             // Get registrations for the current installation ID.
             var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

             bool updated = false;
             bool firstRegistration = true;
             RegistrationDescription registration = null;

             // Check for existing registrations.
             foreach (var registrationDescription in regsForInstId)
             {
                 if (firstRegistration)
                 {

    		 // Update the tags.

                     registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                     // We need to handle each platform separately.
                     switch (platform)
                     {
                         case "windows":
                             var winReg = registrationDescription as WindowsRegistrationDescription;
                             winReg.ChannelUri = new Uri(channelUri);
                             registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                             break;
                         case "ios":
                             var iosReg = registrationDescription as AppleRegistrationDescription;
                             iosReg.DeviceToken = deviceToken;
                             registration = await hubClient.UpdateRegistrationAsync(iosReg);
                             break;
                     }
                     updated = true;
                     firstRegistration = false;
                 }
                 else
                 {
                     // We shouldn't have any extra registrations; delete if we do.
                     await hubClient.DeleteRegistrationAsync(registrationDescription);
                 }
             }

             // Create a new registration.
             if (!updated)
             {
                 switch (platform)
                 {
                     case "windows":
                         registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                             new string[] { installationId, userName });
                         break;
                     case "ios":
                         registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                             new string[] { installationId, userName });
                         break;
                 }
             }

             // Send out a test notification.
             sendNotification(string.Format("Test notification for {0}", userName), userName);

             return registration;
         }

    Questo codice viene chiamato da una richiesta POST e ottiene le informazioni su piattaforma e ID dispositivo dal corpo del messaggio. Tali dati, unitamente all'ID installazione dell'intestazione della richiesta e all'ID utente dell'utente collegato, verranno utilizzate per aggiornare una registrazione. Se non esiste nessuna registrazione, ne verrà creata una nuova. Questa registrazione viene contrassegnata con l'ID utente e l'ID installazione.

6.  Aggiungere il metodo sendNotification seguente:

        // Basic implementation that sends a not ification to Windows Store and iOS app clients.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Create notifications for both Windows Store and iOS platforms.
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                    notificationText + "</text></binding></visual></toast>";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Send a notification to the logged-in user on both platforms.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // This is expected when an APNS registration doesn't exist.
                Console.WriteLine(ex.Message);
            }
        }

    Questo metodo viene chiamato per inviare una notifica subito dopo il completamento della registrazione.

In seguito, verrà aggiornata l'app client creata al completamento dell'esercitazione **Introduzione ad Hub di notifica**.

Aggiornamento dell'appAggiornamento dell'app per la registrazione dell'accesso e delle richieste
------------------------------------------------------------------------------------------------

Con l'app creata al completamento dell'esercitazione **Introduzione ad Hub di notifica** la registrazione deve essere effettuata direttamente dall'hub di notifica. Questo codice di registrazione verrà quindi rimosso dall'app client e sostituito con una chiamata alla nuova API Register nell'applicazione API Web ASP.NET.

1.  Premere F5 per avviare l'applicazione ASP.NET e provare a caricare la pagina predefinita.

    Quando viene visualizzato il browser, prendere nota del nome host del sito richiesto. Tale URL radice sarà necessario durante l'aggiornamento dell'app client.

    **Nota**

    Quando si utilizza il server Web IIS locale o il Server di sviluppo Visual Studio, è inoltre necessario specificare il numero di porta. Si noti che viene restituito un errore 404 perché nell'applicazione non è stata implementata una pagina predefinita.

2.  Attenersi alla procedura riportata in una delle versioni seguenti della pagina relativa all'**utilizzo di API Web ASP.NET per registrare l'utente corrente per le notifiche push**, a seconda della piattaforma client in uso:

    -   [Versione per Windows Store C\#](/it-it/manage/services/notification-hubs/register-users-aspnet-dotnet)
    -   [Versione per iOS](/it-it/manage/services/notification-hubs/howto-register-user-with-aspnet-ios)

3.  Eseguire l'app aggiornata, accedere al servizio con la stessa stringa per nome utente e password, quindi verificare che venga visualizzato l'ID di registrazione assegnato alla notifica.

    Si riceverà inoltre una notifica push.

    **Nota**

    Se per una piattaforma per la quale è richiesto l'invio di una notifica non è disponibile nessuna registrazione, verrà generato un errore che, in questo caso, può essere ignorato. Per informazioni su come utilizzare i modelli per evitare questa situazione, vedere [Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse](/it-it/manage/services/notification-hubs/notify-users-xplat-aspnet).

4.  (Facoltativo) Distribuire l'app client in un secondo dispositivo, quindi eseguirla e inserire testo.

    Verrà visualizzata una notifica su ogni dispositivo.

Passaggi successivi
-------------------

Dopo avere completato questa esercitazione, provare a eseguire quelle seguenti:

-   **Utilizzo di Hub di notifica per inviare le ultime notizie ([Windows Store C\#](/it-it/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/it-it/manage/services/notification-hubs/breaking-news-dotnet))**
    In questa esercitazione specifica della piattaforma viene illustrato come utilizzare tag per consentire agli utenti di sottoscrivere i tipi di notifiche a cui sono interessati.

-   **[Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse](/it-it/manage/services/notification-hubs/notify-users-xplat-aspnet)**
    Questa esercitazione costituisce un'estensione di quella **Utilizzo di Hub di notifica per inviare notifiche agli utenti** e include informazioni relative all'utilizzo di modelli specifici delle piattaforme per la registrazione per le notifiche. In questo modo sarà possibile inviare notifiche da un unico metodo nel codice lato server.

Per ulteriori informazioni su Hub di notifica, vedere [Hub di notifica di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj927170.aspx).

