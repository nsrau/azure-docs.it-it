---
title: Inviare notifiche a dispositivi specifici (piattaforma UWP (Universal Windows Platform)) | Microsoft Docs
description: Usare Hub di notifica di Azure con tag nella registrazione per inviare le ultime notizie a un'app della piattaforma UWP (Universal Windows Platform).
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 9151870836b1a616a79e54275ed185a425c11f0c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385609"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Esercitazione: Inviare notifiche push a specifici dispositivi che eseguono applicazioni della piattaforma UWP (Universal Windows Platform)

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come usare Hub di notifica di Azure per trasmettere notifiche relative alle ultime notizie. L'esercitazione riguarda applicazioni di Windows Store o di Windows Phone 8.1 (non Silverlight). Se la destinazione è Windows Phone 8.1 Silverlight, vedere [Inviare notifiche push a specifici dispositivi Windows Phone con Hub di notifica di Azure](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a specifici dispositivi Windows che eseguono un'applicazione della piattaforma UWP (Universal Windows Platform). Dopo aver completato l'esercitazione, è possibile registrarsi alle categorie di notizie a cui si è interessati. Si riceveranno notifiche push solo per tali categorie.

Per abilitare scenari di trasmissione, includere uno o più *tag* durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi registrati per il tag le riceveranno. Per altre informazioni sui tag, vedere [Routing ed espressioni tag](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> I progetti creati con Windows Store e Windows Phone versione 8.1 e precedenti non sono supportati in Visual Studio 2019. Per altre informazioni, vedere [Selezione della piattaforma e compatibilità di Visual Studio 2019](/visualstudio/releases/2019/compatibility).

In questa esercitazione si eseguiranno le attività seguenti:

> [!div class="checklist"]
> * Aggiungere la selezione delle categorie all'app per dispositivi mobili
> * Registrazione per le notifiche
> * Inviare notifiche con tag
> * Eseguire l'app e generare notifiche

## <a name="prerequisites"></a>Prerequisiti

Completare l'[Esercitazione: Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure][get-started] prima di iniziare questa esercitazione.  

## <a name="add-category-selection-to-the-app"></a>Aggiungere la selezione delle categorie all'app

Il primo passaggio consiste nell'aggiunta degli elementi dell'interfaccia utente alla pagina principale esistente per permettere agli utenti di selezionare le categorie per le quali registrarsi. Le categorie selezionate vengono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione del dispositivo nell'hub di notifica, con le categorie selezionate come tag.

1. Aprire il file di progetto *MainPage.xaml* e quindi copiare il codice seguente nell'elemento `Grid`:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Classe**. In **Aggiungi nuovo elemento** assegnare alla classe il nome *Notifications* e selezionare **Aggiungi**. Se necessario, aggiungere il modificatore `public` alla definizione della classe.

1. Aggiungere le istruzioni `using` seguenti al nuovo file:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Copiare il codice seguente nella nuova classe `Notifications`:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Questa classe usa l'archiviazione locale per archiviare le categorie di notizie che devono essere ricevute dal dispositivo. Invece di chiamare il metodo `RegisterNativeAsync`, chiamare `RegisterTemplateAsync` per effettuare la registrazione per le categorie usando la registrazione di modelli.

    Se si vogliono registrare più modelli, specificare un nome di modello, ad esempio *simpleWNSTemplateExample*. È necessario denominare i modelli in modo da poterli aggiornare o eliminare. Si potrebbe scegliere di registrare più modelli per averne uno per gli avvisi popup e un altro per i riquadri.

    >[!NOTE]
    > Con Hub di notifica, un dispositivo può registrare più modelli con lo stesso tag. In questo caso, un messaggio in arrivo destinato a tale tag determina il recapito di più notifiche al dispositivo, una per ogni modello. Questo processo consente di visualizzare lo stesso messaggio in più notifiche visive, ad esempio sia come notifica che come avviso popup in un'app di Windows Store.

    Per altre informazioni, vedere [Modelli](notification-hubs-templates-cross-platform-push-messages.md).

1. Nel file di progetto *App.xaml.cs* aggiungere la proprietà seguente alla classe `App`:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Usare questa proprietà per creare e accedere a un'istanza di `Notifications`.

    Nel codice sostituire i segnaposto `<hub name>` e `<connection string with listen access>` con il nome dell'hub di notifica e la stringa di connessione per **DefaultListenSharedAccessSignature**, ottenuti in precedenza.

   > [!NOTE]
   > Poiché le credenziali distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso *in ascolto* con l'app client. L'accesso in ascolto permette all'app di registrarsi per le notifiche, ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completo viene usata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.

1. Nel file *MainPage.xaml.cs* aggiungere la riga seguente:

    ```csharp
    using Windows.UI.Popups;
    ```

1. Nel file *MainPage.xaml.cs* aggiungere il metodo seguente:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Questo metodo crea un elenco di categorie e usa la classe `Notifications` per archiviare l'elenco nell'archiviazione locale. Registra inoltre i tag corrispondenti con l'hub di notifica. Se le categorie cambiano, la registrazione viene ricreata con le nuove categorie.

L'app può ora archiviare un insieme di categorie nell'archiviazione locale del dispositivo. L'app si registra con l'hub di notifica ogni volta che gli utenti modificano la selezione delle categorie.

## <a name="register-for-notifications"></a>Registrazione per le notifiche

In questa sezione si effettuerà la registrazione con l'hub di notifica all'avvio usando le categorie archiviate nell'archiviazione locale.

> [!NOTE]
> Poiché l'URI di canale assegnato dal servizio di notifica Windows può cambiare in qualsiasi momento, è consigliabile ripetere spesso la registrazione per le notifiche per evitare errori di notifica. In questo esempio viene effettuata la registrazione per le notifiche a ogni avvio dell'app. Per le app che vengono eseguite di frequente, ad esempio più di una volta al giorno, probabilmente è possibile ignorare la registrazione per conservare la larghezza di banda nei casi in cui è trascorso meno di un giorno dalla registrazione precedente.

1. Per usare la classe `notifications` per una sottoscrizione basata su categorie, aprire il file *App.xaml.cs* e quindi aggiornare il metodo `InitNotificationsAsync`.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Questo processo fa sì che quando viene avviata, l'app recupera le categorie dall'archiviazione locale e quindi richiede la registrazione per tali categorie. Il metodo `InitNotificationsAsync` è stato creato nell'ambito dell'esercitazione [Inviare notifiche alle app della piattaforma UWP (Universal Windows Platform) con Hub di notifica di Azure][get-started].
2. Nel file di progetto *MainPage.xaml.cs* aggiungere il codice seguente nel metodo `OnNavigatedTo`:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Questo codice aggiorna la pagina principale in base allo stato delle categorie salvate in precedenza.

L'app è ora completa. Può archiviare un insieme di categorie nello spazio di archiviazione locale del dispositivo. Quando gli utenti cambiano la selezione di categorie, quelle salvate vengono usate per la registrazione nell'hub di notifica. Nella sezione seguente verrà definito un back-end per l'invio di notifiche delle categorie all'app.

## <a name="run-the-uwp-app"></a>Eseguire l'app UWP

1. In Visual Studio premere F5 per compilare e avviare l'app. L'interfaccia utente dell'app fornisce un insieme di interruttori che permettono di scegliere le categorie per le quali registrarsi.

   ![App delle ultime notizie](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Abilitare uno o più interruttori delle categorie e quindi selezionare **Sottoscrivi**.

   L'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica. L'app visualizza le categorie registrate in una finestra di dialogo.

    ![Interruttori delle categorie e pulsante di sottoscrizione](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Creare un'app console per inviare notifiche con tag

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Eseguire l'app console per inviare notifiche con tag

Eseguire l'app creata nella sezione precedente. Le notifiche per le categorie selezionate vengono visualizzate come notifiche di tipo avviso popup.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come trasmettere le ultime notizie divise per categoria. L'applicazione back-end effettua il push delle notifiche con tag ai dispositivi registrati per ricevere le notifiche per tale tag. Per informazioni su come inviare notifiche push a specifici utenti indipendentemente dal dispositivo che usano, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
> [Effettuare il push di notifiche localizzate alle app Windows con Hub di notifica di Azure](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
