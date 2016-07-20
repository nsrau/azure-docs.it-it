<properties
	pageTitle="Uso di Hub di notifica di Azure per inviare notifiche agli utenti con back-end.NET"
	description="Informazioni su come inviare notifiche push sicure in Azure. Gli esempi di codice sono scritti in C# mediante l'API .NET."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="erikre"
	services="notification-hubs"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="wesmc"/>

#Uso di Hub di notifica di Azure per inviare notifiche agli utenti con back-end.NET

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]


##Panoramica

Il supporto per le notifiche push in Azure consente di accedere a un'infrastruttura push facile da usare, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili. In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Un back-end WebAPI ASP.NET viene usato per autenticare i client. Usando l’utente client autenticato, un tag viene aggiunto automaticamente dal back-end per la registrazione delle notifiche. Questo tag viene usato per l’invio tramite il back-end per generare notifiche per un utente specifico. Per altre informazioni sulla registrazione per le notifiche tramite il back-end di un'app, vedere l'argomento della guida [Registrazione dal back-end dell'app](http://msdn.microsoft.com/library/dn743807.aspx). Questa esercitazione si basa sull'hub di notifica e sul progetto creato nell'esercitazione [Introduzione ad Hub di notifica].

È inoltre propedeutica all'esercitazione [Push sicuro]. Dopo avere eseguito i passaggi indicati in questa esercitazione, è possibile passare all'esercitazione [Push sicuro], che illustra come modificare il codice in questa esercitazione per inviare una notifica push in modo sicuro.





## Prima di iniziare

I commenti e suggerimenti inviati seriamente verranno presi in considerazione. Se si riscontrano difficoltà nel completare questo argomento o si hanno suggerimenti per migliorarne il contenuto, è possibile lasciare un commento nella parte inferiore della pagina.

Il codice completo per questa esercitazione è disponibile su GitHub [qui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers).



##Prerequisiti

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Introduzione ad Hub di notifica]<br/>Creare il proprio hub di notifica, riservare il nome dell'app e registrarsi per ricevere le notifiche in questa esercitazione. In questa esercitazione si presuppone che siano già stati completati questi passaggi. In caso contrario, attenersi alla procedura riportata in [Introduzione ad Hub di notifica (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), con particolare riferimento alle sezioni [Registrare l'app di Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#register-your-app-for-the-windows-store) e [Configurare l'hub di notifica](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). In particolare, assicurarsi di avere immesso i valori **SID pacchetto** e **Chiave privata client** nel portale, nella scheda **Configura** per l'hub di notifica. Questa procedura di configurazione è descritta nella sezione [Configurazione dell'hub di notifica](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Questo passaggio è importante: se le credenziali sul portale non corrispondono a quelle specificate per il nome di app scelto, la notifica push non riuscirà.




> [AZURE.NOTE] Se si usa Servizi mobili come servizio back-end, vedere la [versione per Servizi mobili](../mobile-services/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md) di questa esercitazione.




[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Aggiornare il codice per il progetto client

In questa sezione viene aggiornato il codice nel progetto completato per l’esercitazione [Introduzione ad Hub di notifica]. Il codice dovrebbe essere già associato allo store e configurato per l'hub di notifica. In questa sezione si aggiungerà il codice per chiamare il nuovo back-end WebAPI e si userà tale codice per la registrazione e l'invio di notifiche.

1. In Visual Studio, aprire la soluzione creata per l’esercitazione [Introduzione ad Hub di notifica].

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **(Windows 8.1)**, quindi scegliere **Gestisci pacchetti NuGet**.

3. Sul lato sinistro fare clic su **Online**.

4. Nella **casella di ricerca** digitare **Client Http**.

5. Nell'elenco risultati fare clic su **Librerie client HTTP Microsoft** e quindi su **Installa**. Completare l'installazione.

6. Di nuovo nella **casella di ricerca** di NuGet digitare **Json.net**. Installare il pacchetto **Json.NET**, quindi chiudere la finestra di Gestione pacchetti NuGet.

7. Ripetere i passaggi precedenti per il progetto **(Windows Phone 8.1)** per installare il pacchetto NuGet **JSON.NET** per il progetto Windows Phone.


8. In Esplora soluzioni, nel progetto **(Windows 8.1)** fare doppio clic su **MainPage.xaml** per aprirlo nell'editor di Visual Studio.

9. Nel codice XML **MainPage.xaml** sostituire la sezione `<Grid>` con il codice seguente: Questo codice consente di aggiungere una casella di testo con nome utente e password con cui l'utente eseguirà l'autenticazione. Consente inoltre di aggiungere caselle di testo per il messaggio di notifica e il tag del nome utente che deve ricevere la notifica:

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>


10. In Esplora soluzioni, nel progetto **(Windows Phone 8.1)**, aprire **MainPage.xaml** e sostituire la sezione `<Grid>` di Windows Phone 8.1 con lo stesso codice riportato in precedenza. L’interfaccia dovrebbe essere simile a quanto illustrato nella figura seguente.

	![][13]

11. In Esplora soluzioni aprire il file **MainPage.xaml.cs** per i progetti **(Windows 8.1)** e **(Windows Phone 8.1)**. Aggiungere le istruzioni `using` seguenti all'inizio del file:

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;
		using System.Threading.Tasks;

12. In **MainPage.xaml.cs** per i progetti **(Windows 8.1)** e **(Windows Phone 8.1)**, aggiungere il seguente membro alla classe `MainPage`. Assicurarsi di sostituire `<Enter Your Backend Endpoint>` con l'endpoint back-end effettivo ottenuto in precedenza: Ad esempio: `http://mybackend.azurewebsites.net`.

        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";



13. Aggiungere il codice seguente alla classe MainPage in **MainPage.xaml.cs** per i progetti **(Windows 8.1)** e **(Windows Phone 8.1)**.

	Il metodo `PushClick` è il gestore di clic per il pulsante **Send Push**. Chiama il back-end per attivare una notifica a tutti i dispositivi con un tag di nome utente corrispondente al parametro `to_tag`. Il messaggio di notifica viene inviato come contenuto JSON nel corpo della richiesta.

	Il metodo `LoginAndRegisterClick` è il gestore di clic per il pulsante **Log in and register**. Tale metodo memorizza il token di autenticazione di base nell'archivio locale (si noti che rappresenta qualsiasi token usato dallo schema di autenticazione), quindi usa `RegisterClient` per la registrazione per le notifiche tramite il back-end.


        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent(""" + message + """,
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
				// The device handle used will be different depending on the device and PNS. 
				// Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



14. In Esplora soluzioni, nel progetto **Condiviso**, aprire il file **App.xaml.cs**. Individuare la chiamata a `InitNotificationsAsync()` nel gestore eventi `OnLaunched()`. Impostare come commento o eliminare la chiamata a `InitNotificationsAsync()`. Il gestore del pulsante aggiunto in precedenza inizializzerà le registrazioni delle notifiche.


        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


15. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **Condiviso**, quindi scegliere **Aggiungi** e infine **Classe**. Assegnare alla classe il nome **RegisterClient.cs**, quindi fare clic su **OK** per generare la classe.

	Questa classe conclude le chiamate REST necessarie per contattare il back-end dell'app allo scopo di effettuare la registrazione per le notifiche push. Archivia inoltre in locale i *registrationId* creati dall'hub di notifica, come illustrato in [Registrazione dal back-end dell'app](http://msdn.microsoft.com/library/dn743807.aspx). Si noti che usa un token di autorizzazione memorizzato nell'archivio locale quando si fa clic sul pulsante **Esegui accesso e registrazione**.


16. Aggiungere le seguenti istruzioni `using` all'inizio del file RegisterClient.cs:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		using System.Threading.Tasks;
		using System.Linq;

17. Aggiungere il codice seguente all'interno della definizione di classe `RegisterClient`.

		private string POST_URL;

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public RegisterClient(string backendEndpoint)
        {
            POST_URL = backendEndpoint + "/api/register";
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
				throw new System.Net.WebException(statusCode.ToString());
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
						throw new System.Net.WebException(response.StatusCode.ToString());
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

18. Salvare tutte le modifiche.


## Test dell'applicazione

1. Avviare l'applicazione in Windows 8.1 e Windows Phone 8.1. Per Windows Phone 8.1 è possibile eseguire l'istanza nell'emulatore o in un dispositivo reale.

2. Nell'istanza di Windows 8.1 dell'app, immettere un **Nome utente** e una **Password** come illustrato nella schermata riportata di seguito. È consigliabile immettere un nome utente e una password diversi dal nome utente e dalla password immessi in Windows Phone.


3. Fare clic su **Log in and register** e verificare nella relativa finestra di dialogo di avere effettuato l'accesso. In questo modo anche il pulsante **Send Push** verrà abilitato.

    ![][14]

4. Nell'istanza di Windows Phone 8.1, immettere la stringa di un nome utente nei campi **Nome utente** e **Password** quini fare clic su **Log in and register**.
5. Quindi nel campo relativo al **tag del nome utente del destinatario** immettere il nome utente registrato in Windows 8.1. Immettere un messaggio di notifica e fare clic su **Send Push**.

    ![][16]

6. Solo i dispositivi registrati con il tag del nome utente del destinatario riceveranno il messaggio di notifica.

	![][15]

## Passaggi successivi

* Se si desidera segmentare gli utenti per gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie].
* Per ulteriori informazioni sull'uso di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica].



[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Introduzione ad Hub di notifica]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Push sicuro]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md
[Utilizzo di Hub di notifica per inviare le ultime notizie]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[linee guida su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx

<!---HONumber=AcomDC_0706_2016-->