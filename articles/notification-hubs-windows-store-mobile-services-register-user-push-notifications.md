<properties  linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="" solutions="" manager="" editor="" />

# Registrazione dell'utente corrente per le notifiche push mediante un servizio mobile

[Windows Store C#](/en-us/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications) [iOS](/en-us/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications)


In questo argomento viene illustrato come richiedere la registrazione per le notifiche push con Hub di notifica di Azure quando la registrazione viene eseguita mediante Servizi mobili di Azure. In questo argomento viene estesa l'esercitazione [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users). Per creare il servizio mobile autenticato è necessario aver già completato i passaggi richiesti in tale esercitazione. Per ulteriori informazioni sullo scenario di notifica agli utenti, vedere [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users).

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto modificato dopo avere completato l'esercitazione [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/).

2.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

 	![](./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png) 

	Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.
 
3.  Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

4.  Selezionare l'app registrata in [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users), fare clic su **Avanti** e quindi su **Associa**.
    
	![][2]

	Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.  
    
    <div  class="dev-callout" markdown="1">

	**Nota**

	La registrazione a Windows Store creata per l'app nell'esercitazione sull'utilizzo di Hub di notifica per inviare notifiche agli utenti verrà riutilizzata per questa app di Servizi Mobili. Questo può impedire la ricezione di notifiche nell'app dell'esercitazione sull'utilizzo di Hub di notifica.

5.  In Esplora soluzioni fare doppio clic sul file di progetto Package.appxmanifest per aprirlo nell'editor di Visual Studio.

6.  Scorrere in basso fino a **Tutti gli asset delle immagini** e fare clic su **Logo badge**. In **Notifiche** impostare **Popup supportati** su **Sì**:
    
	![][3]
    
    L'app dell'esercitazione su Servizi Mobili verrà abilitata per la ricezione di notifiche di tipo avviso popup.

7.  In Visual Studio aprire il file MainPage.xaml.cs e aggiungere il codice seguente, che definisce le classi **NotificationRequest** e **RegistrationResult**:
    
        public class NotificationRequest
        {
            public string channelUri { get; set; }
            public string platform { get; set; }
        }

        public class RegistrationResult
        {
            public string RegistrationId { get; set; }
            public string ExpirationTime { get; set; }
        }
    
    Queste classi conterranno rispettivamente il corpo della richiesta e l'ID di registrazione restituiti quando viene chiamata l'API personalizzata.

8.  Nella classe **MainPage** aggiungere il metodo seguente:
    
        private async System.Threading.Tasks.Task RegisterNotification()
        {
            string message;

            // Get a channel for push notifications.
            var channel =
                await Windows.Networking.PushNotifications
                .PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Create the body of the request.
            var body = new NotificationRequest 
            {
                channelUri = channel.Uri, 
                platform = "win8" 
            }; 

            try
            {
                // Call the custom API POST method with the supplied body.
                var result = await App.MobileService
                    .InvokeApiAsync<NotificationRequest, 
                    RegistrationResult>("register_notifications", body,
                    System.Net.Http.HttpMethod.Post, null);

                // Set the response, which is the ID of the registration.
                message = string.Format("Registration ID: {0}", result.RegistrationId);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    
    Questo metodo crea un canale per le notifiche push e lo invia, insieme al tipo di dispositivo, al metodo dell'API personalizzata che crea una registrazione in Hub di notifica. Questa API personalizzata è stata definita in [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users).

9.  Aggiungere la riga di codice seguente al metodo **OnNavigatedTo**, appena dopo la chiamata al metodo **Authenticate**:
    
         await RegisterNotification();
    

	**Nota**
	In questo modo la registrazione verrà richiesta ogni volta che viene caricata la pagina. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata.

Ora che l'app client è stata aggiornata, tornare a [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users) e aggiornare il servizio mobile per l'invio di notifiche con Hub di notifica.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
[2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
[3]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png

<!-- URLs. -->
[Notify users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
