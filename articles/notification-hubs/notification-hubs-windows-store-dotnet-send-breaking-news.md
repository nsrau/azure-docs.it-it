<properties
	pageTitle="Usare Hub di notifica per inviare le ultime notizie (Windows Universal)"
	description="Usare Hub di notifica di Azure con i tag nella registrazione per inviare le ultime notizie a un'app Windows universale."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>


<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="wesmc"/>

# Uso di Hub di notifica per inviare le ultime notizie


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Panoramica

Questo argomento descrive come usare Hub di notifica di Azure per trasmettere le notifiche relative alle ultime notizie a un'app di Windows Store o Windows Phone 8.1 (non Silverlight). Se si sviluppano app per Windows Phone 8.1 Silverlight, fare riferimento alla versione [Windows Phone](notification-hubs-ios-send-breaking-news.md). Al termine dell'esercitazione, si sarà appreso a effettuare la registrazione alle categorie di ultime notizie desiderate e ricevere le notifiche push solo da tali categorie. Questo scenario è un modello comune per molte app che prevedono l'invio di notifiche a gruppi di utenti che hanno in precedenza manifestato il proprio interesse verso tali app, ad esempio lettori di feed RSS, app per fan di musica e così via.

È possibile abilitare gli scenari di trasmissione includendo uno o più _tag_ durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno effettuato la registrazione al tag riceveranno la notifica. Poiché i tag sono costituiti da stringhe, non è necessario eseguire il provisioning anticipatamente. Per ulteriori informazioni sui tag, vedere l'articolo relativo alle [linee guida per gli hub di notifica].

##Prerequisiti

Questo argomento si basa sull'app creata nell'esercitazione [Introduzione ad Hub di notifica][get-started]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ad Hub di notifica][get-started].

##Aggiungere la selezione delle categorie all'app

Il primo passaggio prevede l'aggiunta degli elementi dell'interfaccia utente alla pagina principale esistente per consentire all'utente di selezionare le categorie per le quali registrarsi. Le categorie selezionate da un utente sono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione nell'hub di notifica con le categorie selezionate come tag.

1. Aprire il file di progetto MainPage.xaml, quindi copiare il codice seguente nell'elemento **Grid**:

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


2. Fare clic con il pulsante destro del mouse sul progetto **Shared** e aggiungere una nuova classe denominata **Notifications**, aggiungere il modificatore **public** alla definizione della classe, quindi aggiungere le istruzioni **using** seguenti al nuovo file di codice:

		using Windows.Networking.PushNotifications;
		using Microsoft.WindowsAzure.Messaging;
		using Windows.Storage;
		using System.Threading.Tasks;

3. Copiare il codice seguente nella nuova classe **Notifications**:

		private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            await SubscribeToCategories(categories);
        }

		public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                IEnumerable<string> categories = RetrieveCategories();
            }

            await hub.RegisterNativeAsync(channel.Uri, categories);
        }

    Questa classe utilizza l'archiviazione locale per archiviare le categorie di notizie che il dispositivo deve ricevere. Contiene inoltre i metodi per effettuare la registrazione per queste categorie.


4. Nel file di progetto App.xaml.cs aggiungere la proprietà seguente alla classe **App**:

		public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

	Questa proprietà viene utilizzata per creare un'istanza **Notifications** e accedervi.

	Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with listen access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuta in precedenza.

	> [AZURE.NOTE]Poiché le credenziali che sono distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso Listen con l'app client. L'accesso Listen consente all'app di registrarsi per le notifiche ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completo viene usata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.

5. In MainPage.xaml.cs aggiungere la riga seguente:

		using Windows.UI.Popups;

6. Aggiungere quindi il metodo seguente nel file di progetto MainPage.xaml.cs:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories));
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

	Questo metodo crea un elenco di categorie e utilizza la classe **Notifications** per archiviare l'elenco nell'archiviazione locale e registrare i tag corrispondenti nell'hub di notifica. Se le categorie vengono modificate, la registrazione viene ricreata con le nuove categorie.

L'app può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie.

##Registrazione per le notifiche

Questa procedura consente di effettuare la registrazione con l'hub di notifica all'avvio usando le categorie archiviate nella risorsa di archiviazione locale.

> [AZURE.NOTE]Poiché l'URI di canale assegnato dal servizio di notifica Windows può cambiare in qualsiasi momento, è necessario ripetere di frequente la registrazione per le notifiche per evitare errori di notifica. In questo esempio viene effettuata la registrazione per le notifiche a ogni avvio dell'app. Per le app che vengono eseguite con una frequenza maggiore di una volta al giorno, è possibile ignorare la registrazione per conservare la larghezza di banda qualora sia trascorso meno di un giorno dalla registrazione precedente.

1. Aprire il file App.xaml.cs file e aggiungere il modificatore **async** al metodo **OnLaunched**.

2. Nel metodo **OnLaunched** individuare e sostituire la chiamata esistente al metodo **InitNotificationsAsync** con la riga di codice seguente:

		await notifications.SubscribeToCategories();

	In questo modo, ogni volta che l'app viene avviata vengono recuperate le categorie dall'archiviazione locale e viene richiesta una registrazione per queste categorie. Il metodo **InitNotificationsAsync** è stato creato nell'ambito dell'esercitazione [Introduzione ad Hub di notifica], ma non è necessario in questo argomento.

3. Nel file di progetto MainPage.xaml.cs aggiungere il codice seguente nel metodo *OnNavigatedTo*:

		var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;

	La pagina principale viene aggiornata in base allo stato delle categorie salvate in precedenza.

Ora l'app è completa e può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie. Verrà ora definito un back-end per l'invio delle notifiche delle categorie all'app.

##Inviare notifiche dal back-end

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##Eseguire l'app e generare notifiche

1. In Visual Studio premere F5 per compilare e avviare l'app.

	![][1]

	Si noti che l'interfaccia utente dell'app fornisce un set di interruttori che consentono di scegliere le categorie per le quali effettuare la sottoscrizione.

2. Abilitare uno o più interruttori di categorie e quindi fare clic su **Subscribe**.

	L'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica. Le categorie registrate vengono restituite e visualizzate in una finestra di dialogo.

	![][19]

4. Inviare una nuova notifica dal back-end in uno dei modi seguenti:

	+ **App console:** avviare l'app console.

	+ **Java/PHP**: eseguire l'app o lo script.

	Le notifiche per le categorie selezionate vengono visualizzate come notifiche di tipo avviso popup.

	![][14]

##Passaggi successivi

In questa esercitazione si è appreso a trasmettere le ultime novità per categoria. Per informazioni su altri scenari avanzati di Hub di notifica, provare a completare le seguenti esercitazioni:

+ [Usare Hub di notifica per la trasmissione di notizie localizzate]

	Informazioni su come espandere l'app relativa alle ultime novità per abilitare l'invio di notifiche localizzate.

+ [Usare Hub di notifica per inviare notifiche agli utenti]

	Informazioni su come eseguire il push di notifiche a utenti autenticati specifici. Si tratta di un'ottima soluzione per inviare le notifiche solo a determinati utenti.


<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Usare Hub di notifica per la trasmissione di notizie localizzate]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Usare Hub di notifica per inviare notifiche agli utenti]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[linee guida per gli hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!---HONumber=Oct15_HO3-->