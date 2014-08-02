<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="" solutions="" manager="" editor="" />

Registrazione dell'utente corrente per le notifiche push mediante ASP.NET
=========================================================================

[Windows Store C\#](/en-us/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/ "Windows Store C#")[iOS](/en-us/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/ "iOS")

In questo argomento viene illustrato come chiedere la registrazione per le notifiche push con Hub di notifica di Azure quando la registrazione viene eseguita mediante l'API Web ASP.NET. In questo argomento viene estesa l'esercitazione [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet). Per creare il servizio mobile autenticato è necessario aver già completato i passaggi richiesti in tale esercitazione. Per ulteriori informazioni sullo scenario di notifica agli utenti, vedere [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet).

1.  In Visual Studio 2012 aprire il file app.xaml.cs nel progetto creato durante l'esercitazione [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet) compresa tra i prerequisiti.

2.  Individuare il metodo **InitNotificationsAsync** e impostare come commento il codice nel metodo.

    La registrazione per le notifiche verrà eseguita utilizzando l'API Web ASP.NET.

3.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

4.  Nel riquadro sinistro selezionare la categoria **Online**, cercare `json.net`, fare clic su **Installa** nel pacchetto **Json.NET**, quindi accettare il contratto di licenza.

   	![](./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png)

   	L'assembly di terze parti Newtonsoft.Json.dll verrà aggiunto al progetto.

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Aggiungi** e quindi **Classe**, digitare `LocalStorageManager` e infine fare clic su **Aggiungi**.

    ![](./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png)

    Il file di codice per la classe **LocalStorageManager** verrà aggiunto al progetto.

2.  Aprire il nuovo file di progetto LocalStorageManager.cs e aggiungere l'istruzione **using** seguente:

         using Windows.Storage;

3.  Sostituire la definizione della classe **LocalStorageManager** con il codice seguente:

         class LocalStorageManager
         {
             private static ApplicationDataContainer GetLocalStorageContainer()
             {
                 if (!ApplicationData.Current.LocalSettings
                     .Containers.ContainsKey("InstallationContainer"))
                 {
                     ApplicationData.Current.LocalSettings
                         .CreateContainer("InstallationContainer",                                                            
                         ApplicationDataCreateDisposition.Always);
                 }
                 return ApplicationData.Current.LocalSettings
                     .Containers["InstallationContainer"];
             }

             public static string GetInstallationId()
             {
                 var container = GetLocalStorageContainer();
                 if (!container.Values.ContainsKey("InstallationId"))
                 {
                     container.Values["InstallationId"] = Guid.NewGuid().ToString();
                 }
                 return (string)container.Values["InstallationId"];
             }
         }

    Questo codice consente di creare e archiviare un ID di installazione specifico del dispositivo, che viene utilizzato come tag durante la creazione di notifiche. Se esiste già un ID di installazione, verrà utilizzato quest'ultimo.

4.  Aprire il file di progetto MainPage.xaml e sostituire l'elemento radice **Grid** con il codice XAML seguente:

         <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
             <Grid Margin="120, 58, 120, 80" >
                 <Grid.RowDefinitions>
                     <RowDefinition />
                     <RowDefinition/>
                     <RowDefinition/>
                     <RowDefinition/>
                     <RowDefinition/>
                 </Grid.RowDefinitions>
                 <Grid.ColumnDefinitions>
                     <ColumnDefinition MaxWidth="200"/>
                     <ColumnDefinition/>
                 </Grid.ColumnDefinitions>
                 <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                                TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                                FontSize="42"  VerticalAlignment="Top"/>
                 <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                                FontSize="24" VerticalAlignment="Center" />
                 <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                                VerticalAlignment="Center"/>
                 <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                                VerticalAlignment="Center"/>
                 <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                              Width="300" Height="40" HorizontalAlignment="Left"/>
                 <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                                VerticalAlignment="Center" />
                 <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                  Width="300" Height="40" HorizontalAlignment="Left"/>
                 <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                             Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
             </Grid>
         </Grid>

    Questo codice consente di creare un'interfaccia utente per la raccolta di nome utente e password e per la visualizzazione dell'ID di installazione.

5.  Di nuovo nel file MainPage.xaml.cs aggiungere le direttive **using** seguenti:

         using Newtonsoft.Json;
         using System.Net.Http;
         using System.Text;
         using Windows.Networking.PushNotifications;
         using Windows.UI.Popups;

6.  Aggiungere la definizione seguente alla classe **MainPage**:

         private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    Sostituire *`<SERVICE_ROOT_URL>`* con l'URI radice dell'API Web creata in **Utilizzo di Hub di notifica per inviare notifiche agli utenti**.

7.  Aggiungere il metodo seguente alla classe **MainPage**:

         private async void Login_Click(object sender, RoutedEventArgs e)
         {
             // Get the info that we need to request registration.
             var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
             var channel = await PushNotificationChannelManager
                 .CreatePushNotificationChannelForApplicationAsync();
             var user = User.Text;
             var pwd = Password.Password;

             // Define a registration to pass in the body of the POST request.
             var registration = new Dictionary<string, string>()
                                    {{"platform", "windows"},
                                    {"instId", installationId.ToString()},
                                    {"channelUri", channel.Uri}};

             // Create a client to send the HTTP registration request.
             var client = new HttpClient();
             var request =
                 new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));
                
             // Add the Authorization header with the basic login credentials.
             var auth = "Basic " +
                 Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
             request.Headers.Add("Authorization", auth);
             request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                 Encoding.UTF8, "application/json");

             string message;

             try
             {
                 // Send the registration request.
                 HttpResponseMessage response = await client.SendAsync(request);

                 // Get and display the response, either the registration ID
                 // or an error message.
                 message = await response.Content.ReadAsStringAsync();
                 message = string.Format("Registration ID: {0}", message);
             }
             catch (Exception ex)
             {
                 message = ex.Message;
             }

             // Display a message dialog.
             var dialog = new MessageDialog(message);
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }

    Questo metodo ottiene un ID di installazione e un canale per le notifiche push e li invia, insieme al tipo di dispositivo, al metodo API Web di autenticazione che crea una registrazione in Hub di notifica. Questa API Web è stata definita in [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet).

Ora che l'app client è stata aggiornata, tornare a [Utilizzo di Hub di notifica per inviare notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet) e aggiornare il servizio mobile per l'invio di notifiche con Hub di notifica.

