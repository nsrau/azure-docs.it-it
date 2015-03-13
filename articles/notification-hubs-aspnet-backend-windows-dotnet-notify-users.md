<properties 
	pageTitle="Uso di Hub di notifica di Azure per inviare notifiche agli utenti" 
	description="Informazioni su come inviare notifiche push sicure in Azure. Gli esempi di codice sono scritti in C# mediante l'API .NET." 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	services="notification-hubs" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

#Uso di Hub di notifica di Azure per inviare notifiche agli utenti

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/it-it/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Il supporto per le notifiche push in Azure consente di accedere a un'infrastruttura push facile da usare, multipiattaforma e con scalabilità orizzontale, che semplifica considerevolmente l'implementazione delle notifiche push sia per le applicazioni consumer sia per quelle aziendali per piattaforme mobili. Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche viene usato un back-end WebAPI ASP.NET, come illustrato nell'argomento [Registrazione dal back-end dell'app](http://msdn.microsoft.com/library/dn743807.aspx). Questa esercitazione si basa sull'hub di notifica creato nell'esercitazione **Introduzione ad Hub di notifica**.

È inoltre propedeutica all'esercitazione **Push sicuro**. Dopo avere eseguito i passaggi indicati nell'esercitazione **Notifiche agli utenti**, è possibile passare all'esercitazione **Push sicuro**, che illustra come modificare il codice di **Notifiche agli utenti** per inviare una notifica push in modo sicuro. 

> [AZURE.NOTE] Questa esercitazione presuppone che l'utente abbia creato e configurato l'hub di notifica come descritto in [Introduzione ad Hub di notifica (Windows Store)](http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-get-started/).
> Se si usa Servizi mobili come servizio back-end, vedere la [versione per Servizi mobili](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/).
>Si noti anche che nell'esercitazione verrà creata un'app di Windows Phone Store 8.1. Lo stesso codice può essere usato per app di Windows Store e app di Windows universali. Tutte queste app devono usare credenziali Windows (non Windows Phone).

## Creare e configurare l'hub di notifica

Prima di iniziare questa esercitazione, è necessario riservare un nome di applicazione, creare un hub di notifica di Azure e collegarlo all'applicazione. Attenersi alla procedura riportata in [Introduzione ad Hub di notifica (Windows Store)](http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-get-started/), con particolare riferimento alle sezioni [Registrare l'app di Windows Store](http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register) e [Configurare l'hub di notifica](http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub). In particolare, assicurarsi di avere immesso i valori **SID pacchetto** e **Chiave privata client** del portale, nella scheda **Configura** per l'hub di notifica. Questa procedura di configurazione è descritta nella sezione [Configurare l'hub di notifica](http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub). Questo passaggio è importante: se le credenziali sul portale non corrispondono a quelle specificate per il nome di app scelto, la notifica push non riuscirà.

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Creare il progetto dell'app di Windows Phone

Il passaggio successivo consiste nella creazione dell'applicazione per Windows Phone. Per aggiungere il progetto alla soluzione corrente, seguire questa procedura:

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo di primo livello della soluzione (in questo caso **Solution NotifyUsers**), scegliere **Aggiungi**, quindi fare clic su **Nuovo progetto**.

2. Espandere **Applicazioni Windows Store**, fare clic su **Applicazioni Windows Phone** e quindi su **Applicazione vuota (Windows Phone)**.

	![][9]

3. Nella casella **Nome** digitare **NotifyUserWindowsPhone**, quindi fare clic su **OK** per generare il progetto.

 
4. Associare l'applicazione a Windows Phone Store: in Esplora soluzioni fare clic con il pulsante destro del mouse su **NotifyUserWindowsPhone (Windows Phone 8.1)**, quindi scegliere **Store** e infine fare clic su **Associa applicazione a Store**.

	![][10]
 
5. Seguire le istruzioni nella procedura guidata per eseguire l'accesso e associare l'app a Store.

	![][11]
	
	> [AZURE.NOTE] Prendere nota del nome dell'applicazione scelto durante questa procedura. L'hub di notifica deve essere configurato nel portale usando le credenziali ottenute da [Windows Dev Center](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) per lo specifico nome di app riservato. Questa procedura di configurazione è descritta in [Configurare l'hub di notifica](http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub). Questo passaggio è importante: se le credenziali sul portale non corrispondono a quelle specificate per il nome di app scelto, la notifica push non riuscirà.

6. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **NotifyUserWindowsPhone (Windows Phone 8.1)**, quindi scegliere **Gestisci pacchetti NuGet**.

7. Sul lato sinistro fare clic su **Online**.

8. Nella casella di **ricerca** digitare **Client Http**.

9. Nell'elenco dei risultati fare clic su **Librerie client HTTP Microsoft** e quindi su **Installa**. Completare l'installazione.

10. Nella casella di **ricerca** di NuGet digitare **Json.net**. Installare il pacchetto **Json.NET**, quindi chiudere la finestra di Gestione pacchetti NuGet.

11. In Esplora soluzioni, nel progetto **NotifyUserWindowsPhone (Windows Phone 8.1)**, fare doppio clic su **MainPage.xaml** per aprirlo nell'editor di Visual Studio.

12. Nel codice XML di **MainPage.xaml** sostituire la sezione `<Grid>` con il seguente codice:

		<Grid>
	        <Grid.RowDefinitions>
	            <RowDefinition Height="Auto"/>
	            <RowDefinition Height="*"/>
	        </Grid.RowDefinitions>

	        <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

        	<StackPanel Grid.Row="1" VerticalAlignment="Center">
        	    <Grid>
        	        <Grid.RowDefinitions>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="*"/>
        	        </Grid.RowDefinitions>
            	    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
            	    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
            	    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
            	    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>
	
            	    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

            	    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
            	</Grid>
        	</StackPanel>
    	</Grid>


13. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **NotifyUserWindowsPhone (Windows Phone 8.1)**, quindi scegliere **Aggiungi** e fare clic su **Classe**. Assegnare alla classe il nome **RegisterClient.cs**, quindi fare clic su **OK** per generare la classe. Questo componente implementa le chiamate REST necessarie per contattare il back-end dell'app allo scopo di effettuare la registrazione per le notifiche push. Archivia inoltre in locale gli  *registrationIds* creati dall'hub di notifica, come illustrato in [Registrazione dal back-end dell'app](http://msdn.microsoft.com/library/dn743807.aspx). Si noti che usa un token di autorizzazione memorizzato nell'archivio locale quando si fa clic sul pulsante **Esegui accesso e registrazione**.

14. Aggiungere il seguente codice all'interno della definizione della classe `RegisterClient`. Sostituire `{backend endpoint}` con l'endpoint back-end ottenuto nella sezione precedente:

		private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
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
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }


15. Aggiungere le seguenti istruzioni  `using` all'inizio del file RegisterClient.cs:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		
16. Aggiungere il codice per i pulsanti in MainPage.xaml.cs. Il callback per **Esegui accesso e registrazione** memorizza il token di autenticazione di base nell'archivio locale (si noti che rappresenta qualsiasi token usato dallo schema di autenticazione), quindi usa  `RegisterClient` per chiamare il back-end. Il callback per **AppBackend** chiama il back-end per attivare una notifica sicura a tutti i dispositivi dell'utente. 

	Aggiungere il seguente codice a MainPage.xaml.cs dopo il metodo  `OnNavigatedTo()`. Sostituire `{backend endpoint}` con l'endpoint back-end ottenuto nella sezione precedente:

		private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();
            
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;
            
            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }


17. Aggiungere le seguenti istruzioni  `using` all'inizio del file MainPage.xaml.cs:

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

## Esecuzione dell'applicazione

Per eseguire l'applicazione, seguire questa procedura:

1. In Visual Studio eseguire l'app per Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)**. Verrà eseguito l'emulatore di Windows Phone e l'app verrà caricata automaticamente.

2. Nell'interfaccia utente dell'app **NotifyUserWindowsPhone** immettere un nome utente e una password. Può trattarsi di qualsiasi stringa, ma devono avere lo stesso valore.

3. Nell'interfaccia utente dell'app **NotifyUserWindowsPhone** fare clic su **Esegui accesso e registrazione**. Fare quindi clic su **Send push**.


[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png

<!--HONumber=45--> 
