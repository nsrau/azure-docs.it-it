---
title: Inviare notifiche a utenti specifici con Hub di notifica di Azure | Microsoft Docs
description: Informazioni sull'invio di notifiche a utenti specifici con le applicazioni della piattaforma UWP (Universal Windows Platform).
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.author: dimazaid
ms.openlocfilehash: ee0e78402515a733731e6faf21b09886674cda51
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452414"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Esercitazione: Inviare notifiche a utenti specifici con Hub di notifica di Azure
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Un back-end WebAPI ASP.NET viene usato per autenticare i client. Quando il back-end autentica un utente dell'applicazione client, aggiunge automaticamente un tag per la registrazione della notifica. Il back-end usa questo tag per inviare notifiche all'utente specifico. 

> [!NOTE]
> Il codice completo per questa esercitazione è disponibile in [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare il progetto WebAPI
> * Autenticare client con il back-end WebAPI
> * Eseguire la registrazione per le notifiche tramite il back-end WebAPI
> * Inviare notifiche dal back-end WebAPI
> * Pubblicare il nuovo back-end WebAPI
> * Aggiornare il codice per il progetto client
> * Test dell'applicazione


## <a name="prerequisites"></a>prerequisiti
Questa esercitazione si basa sull'hub di notifica e sul progetto di Visual Studio che è stato creato nell'[Esercitazione: Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Pertanto, completarla prima di avviare questa esercitazione. 

> [!NOTE]
> Se si usano app per dispositivi mobili nel servizio app di Azure, vedere la [versione per App per dispositivi mobili](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) di questa esercitazione.


&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Aggiornare il codice per il progetto client
In questa sezione, si aggiornerà il codice nel progetto completato per l'[Esercitazione: Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Il progetto deve essere già associato a Windows Store. Deve inoltre essere configurato per usare l'hub di notifica. In questa sezione si aggiungerà il codice per chiamare il nuovo back-end WebAPI e si userà tale codice per la registrazione e l'invio di notifiche.

1. In Visual Studio, aprire la soluzione creata per l'[Esercitazione: Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **WindowsApp** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Sul lato sinistro fare clic su **Online**.
4. Nella **casella di ricerca** digitare **Client Http**.
5. Nell'elenco dei risultati fare clic su **System.Net.Http**, quindi su **Installa**. Completare l'installazione.
6. Di nuovo nella **casella di ricerca** di NuGet digitare **Json.net**. Installare il pacchetto **Newtonsoft.json**, quindi chiudere la finestra di Gestione pacchetti NuGet.
8. In Esplora soluzioni, nel progetto **WindowsApp**, fare doppio clic su **MainPage.xaml** per aprirlo nell'editor di Visual Studio.
9. Nel codice XML **MainPage.xaml**, sostituire la sezione `<Grid>` con il codice seguente: questo codice aggiunge una casella di testo Nome utente e Password con cui l'utente esegue l'autenticazione. Consente inoltre di aggiungere caselle di testo per il messaggio di notifica e il tag del nome utente che deve ricevere la notifica:

    ```xml   
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
    ```
11. In Esplora soluzioni aprire il file **MainPage.xaml.cs** per i progetti **(Windows 8.1)** e **(Windows Phone 8.1)**. Aggiungere le istruzioni `using` seguenti all'inizio del file:

    ```csharp    
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```
12. In **MainPage.xaml.cs**, per i progetti **WindowsApp**, aggiungere il membro seguente alla classe `MainPage`. Assicurarsi di sostituire `<Enter Your Backend Endpoint>` con l'endpoint back-end effettivo ottenuto in precedenza. Ad esempio, `http://mybackend.azurewebsites.net`.
    
    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```
13. Aggiungere il codice seguente alla classe MainPage in **MainPage.xaml.cs** per i progetti **(Windows 8.1)** e **(Windows Phone 8.1)**.
    
    Il metodo `PushClick` è il gestore di clic per il pulsante **Send Push** . Chiama il back-end per attivare una notifica a tutti i dispositivi con un tag di nome utente corrispondente al parametro `to_tag` . Il messaggio di notifica viene inviato come contenuto JSON nel corpo della richiesta.
    
    Il metodo `LoginAndRegisterClick` è il gestore di clic per il pulsante **Login and register**. Tale metodo memorizza il token di autenticazione di base (rappresenta qualsiasi token usato dallo schema di autenticazione) nell'archivio locale, quindi usa `RegisterClient` per la registrazione per le notifiche tramite il back-end.

    ```csharp
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
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
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
            // The device handle used is different depending on the device and PNS. 
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
    ```
1. Aprire il file **App.xaml.cs**. Individuare la chiamata a `InitNotificationsAsync()` in the `OnLaunched()` . Impostare come commento o eliminare la chiamata a `InitNotificationsAsync()`. Il gestore del pulsante inizializzerà le registrazioni delle notifiche.

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```
1. Fare clic con il pulsante destro del mouse sul progetto **WindowsApp**, scegliere **Aggiungi** e quindi **Classe**. Assegnare alla classe il nome **RegisterClient.cs**, quindi fare clic su **OK** per generare la classe.
   
   Questa classe conclude le chiamate REST necessarie per contattare il back-end dell'app allo scopo di effettuare la registrazione per le notifiche push. Archivia inoltre in locale i *registrationId* creati dall'hub di notifica, come illustrato in [Registrazione dal back-end dell'app](http://msdn.microsoft.com/library/dn743807.aspx). Usa un token di autorizzazione memorizzato nell'archivio locale quando si fa clic sul pulsante **Esegui accesso e registrazione**.
2. Aggiungere le seguenti istruzioni `using` all'inizio del file RegisterClient.cs:

    ```csharp   
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```
3. Aggiungere il codice seguente all'interno della definizione di classe `RegisterClient` .
   
    ```csharp
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

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
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
    ```
4. Salvare tutte le modifiche.

## <a name="test-the-application"></a>Testare l'applicazione
1. Avviare l'applicazione in entrambe le versioni di Windows.
2. Immettere un **Nome utente** e una **Password** come illustrato nella schermata riportata di seguito. È consigliabile immettere un nome utente e una password diversi dal nome utente e dalla password immessi in Windows Phone.
3. Fare clic su **Log in and register** e verificare nella relativa finestra di dialogo di avere effettuato l'accesso. Questo codice abilita anche il pulsante **Send Push**.
   
    ![][14]
5. Quindi nel campo relativo al **tag del nome utente del destinatario** immettere il nome utente registrato. Immettere un messaggio di notifica e fare clic su **Send Push**.
6. Solo i dispositivi registrati con il tag del nome utente del destinatario riceveranno il messaggio di notifica.
   
    ![][15]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come inviare notifiche push a utenti specifici che hanno tag associati alle loro registrazioni. Per informazioni sulle procedure per effettuare il push di notifiche in base alla posizione, passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche basate sulla posizione](notification-hubs-push-bing-spartial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
