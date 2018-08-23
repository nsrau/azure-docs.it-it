---
title: Inviare notifiche localizzate alle app Windows con Hub di notifica di Azure | Microsoft Docs
description: Informazioni su come usare Hub di notifica di Azure per inviare notifiche relative alle ultime notizie localizzate.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0e78b00e49b2ef468e693abfdcde8a138313ba63
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920853"
---
# <a name="tutorial-push-localized-notifications-to-windows-apps-by-using-azure-notification-hubs"></a>Esercitazione: eseguire il push di notifiche localizzate alle app Windows con Hub di notifica di Azure
> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come inviare notifiche push localizzate ai dispositivi mobili registrati con il servizio Hub di notifica. Nell'esercitazione si esegue l'aggiornamento delle applicazioni create nell'[Esercitazione: inviare notifiche a dispositivi specifici (piattaforma UWP (Universal Windows Platform))](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) per supportare gli scenari seguenti: 

- L'app Windows Store consente ai dispositivi client di specificare una lingua e di sottoscrivere categorie diverse di ultime notizie.
- L'app back-end trasmette le notifiche usando le funzionalità relative ai **tag** e ai **modelli** di Hub di notifica di Azure.

Dopo aver completato l'esercitazione, l'applicazione per dispositivi mobili consente di registrarsi per le categorie di interesse e anche di specificare una lingua in cui si intende ricevere le notifiche. L'applicazione back-end invia le notifiche localizzate in base alla lingua e dispositivo. 

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Aggiornare l'app di Windows per supportare informazioni sulle impostazioni locali
> * Aggiornare l'app back-end per inviare le notifiche localizzate
> * Testare l'app

## <a name="prerequisites"></a>Prerequisiti
Completare l'[Esercitazione: inviare notifiche a dispositivi specifici (piattaforma UWP (Universal Windows Platform))](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). 

Nell'[Esercitazione: inviare notifiche a dispositivi specifici (piattaforma UWP (Universal Windows Platform))](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) viene generata un'app che ha usato i **tag** per sottoscrivere le notifiche a diverse **categorie**  di notizie. In questa esercitazione viene usata la funzionalità relativa ai **modelli** di Hub di notifica per inviare facilmente notifiche relative alle ultime notizie **localizzate**.

In generale, i modelli rappresentano un modo per specificare il formato in cui un dispositivo specifico deve ricevere una notifica. Il modello definisce lo specifico formato di payload da utilizzare, facendo riferimento alle proprietà del messaggio inviato dal back-end dell'app. In questa esercitazione, l'applicazione back-end invia un messaggio indipendente dalle impostazioni locali che contiene tutte le lingue supportate:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

I dispositivi effettuano la registrazione con un modello che faccia riferimento alla proprietà corretta. Ad esempio, un'app Windows Store che vuole ricevere un semplice avviso popup in inglese effettuerà la registrazione per il modello seguente con eventuali tag corrispondenti:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Per altre informazioni sui modelli, vedere l'articolo [Modelli](notification-hubs-templates-cross-platform-push-messages.md). 

## <a name="update-windows-app-to-support-locale-information"></a>Aggiornare l'app di Windows per supportare informazioni sulle impostazioni locali

1. Aprire la soluzione Visual Studio creata per l'[Esercitazione: inviare notifiche a dispositivi specifici (piattaforma UWP (Universal Windows Platform))](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). 
2. Aggiornare il file **MainPage.xaml** in modo da includere una casella combinata per le impostazioni locali:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Nella classe **Notifications** aggiungere un parametro "locale" ai metodi **StoreCategoriesAndSubscribe** e **SubscribeToCateories**.

    ```csharp   
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Anziché chiamare il metodo *RegisterNativeAsync*, chiamare *RegisterTemplateAsync*. Si registra un formato di notifiche specifico in cui il modello varia a seconda delle impostazioni locali. Poiché può essere necessario registrare più di un modello, ad esempio uno per le notifiche di tipo avviso popup e uno per i riquadri, specificare anche un nome di modello, ad esempio "simpleWNSTemplateExample". È anche necessario assegnare loro un nome per aggiornarli o eliminarli.
   
    Se un dispositivo registra più modelli con lo stesso tag, un messaggio in arrivo destinato a tale tag ha come esito il recapito al dispositivo di più notifiche, una per ogni modello. Questo comportamento risulta utile quando lo stesso messaggio logico deve avere produrre più notifiche visive, ad esempio visualizzare sia una notifica badge che una notifica di tipo avviso popup in un'app di Windows Store.
3. Aggiungere il metodo seguente per recuperare le impostazioni locali archiviate:
   
    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

4. In **MainPage.xaml.cs** aggiornare il gestore degli eventi clic del pulsante recuperando il valore corrente della casella combinata Locale e fornendolo alla chiamata alla classe Notifications, come illustrato di seguito:
   
    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
4. Infine, nel file App.xaml.cs, assicurarsi di aggiornare il metodo `InitNotificationsAsync` per recuperare le impostazioni locali e utilizzarle per la sottoscrizione:

    ```csharp   
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

## <a name="send-localized-notifications-from-your-back-end"></a>Inviare notifiche localizzate dal back-end
Quando si inviano notifiche modello è necessario fornire solo un set di proprietà. In questa esercitazione l'applicazione back-end invia il set di proprietà contenente la versione localizzata delle notizie correnti, ad esempio:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

In questa sezione si aggiorna il progetto di applicazione console nella soluzione. Modificare il metodo `SendTemplateNotificationAsync` nell'app console creata in precedenza con il codice seguente: 

> [!IMPORTANT]
> Specificare il nome e la stringa di connessione con accesso completo per l'hub di notifica nel codice. 


```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Questa semplice chiamata distribuisce la notizia localizzata a **tutti** i dispositivi, indipendentemente dalla piattaforma, in quanto l'Hub di notifica crea il payload nativo corretto e lo distribuisce a tutti i dispositivi che hanno sottoscritto un tag specifico.

## <a name="test-the-app"></a>Testare l'app
1. Eseguire l'applicazione di Universal Windows Store. Attendere fino a visualizzare il messaggio **Registrazione completata**.

    ![Registrazione e applicazione per dispositivi mobili](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
1. Selezionare le **categorie** e le **impostazioni locali**, quindi fare clic su **Subscribe** (Sottoscrivi). L'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica.

    ![Applicazione per dispositivi mobili](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
2.  Viene visualizzato un messaggio di **conferma** per le **sottoscrizioni**. 

    ![Messaggio di sottoscrizione](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)
1. Dopo aver ricevuto un messaggio di conferma, eseguire la **app console** per inviare notifiche per ciascuna categoria e in ogni lingua supportata. Verificare che venga ricevuta solo una notifica per le categorie sottoscritte e che il messaggio sia relativo alle impostazioni locali selezionate. 

    ![Messaggi di notifica](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)
 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come inviare notifiche push a dispositivi specifici che hanno tag associati alle loro registrazioni. Per informazioni sulle procedure per eseguire il push di notifiche a utenti specifici, che possono usare diversi dispositivi, passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a utenti specifici](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)


<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
