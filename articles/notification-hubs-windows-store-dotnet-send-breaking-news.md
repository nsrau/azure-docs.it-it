<properties 
	pageTitle="Usare Hub di notifica per inviare le ultime notizie (Windows Universal)" 
	description="Usare Hub di notifica di Azure con i tag nella registrazione per inviare le ultime notizie a un'app Windows universale." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>

# Uso di Hub di notifica per inviare le ultime notizie
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" class="current">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
		<a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

Questo argomento illustra come usare Hub di notifica di Azure per trasmettere le notifiche relative alle ultime notizie a un'app di Windows Store o Windows Phone 8.1 (non Silverlight). Se si sviluppano app di Windows Phone 8.1 Silverlight, fare riferimento alla versione [Windows Phone](/it-it/documentation/articles/notification-hubs-ios-send-breaking-news). Al termine dell'esercitazione, si sarà appreso a effettuare la registrazione alle categorie di ultime notizie desiderate e ricevere le notifiche push solo da tali categorie. Questo scenario è un modello comune per molte app che prevedono l'invio di notifiche a gruppi di utenti che hanno in precedenza manifestato il proprio interesse verso tali app, ad esempio lettori di feed RSS, app per fan di musica e così via. 

È possibile abilitare gli scenari di trasmissione includendo uno o più _tags_ durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno effettuato la registrazione al tag riceveranno la notifica. Poiché i tag sono costituiti da stringhe, non è necessario eseguire il provisioning anticipatamente. Per altre informazioni sui tag, vedere le [informazioni aggiuntive su Hub di notifica]. 

In questa esercitazione vengono descritte le operazioni di base per abilitare questo scenario:

1. [Aggiungere la selezione delle categorie all'app]
2. [Registrarsi per le notifiche]
3. [Inviare notifiche dal back-end]
4. [Eseguire l'app e generare notifiche]

Questo argomento si basa sull'app creata nell'esercitazione [Introduzione ad Hub di notifica][get-started]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ad Hub di notifica][get-started].

##<a name="adding-categories"></a>Aggiungere la selezione delle categorie all'app

Il primo passaggio prevede l'aggiunta degli elementi dell'interfaccia utente alla pagina principale esistente per consentire all'utente di selezionare le categorie per le quali registrarsi. Le categorie selezionate da un utente sono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione del dispositivo nell'hub di notifica con le categorie selezionate come tag. 

1. Aprire il file di progetto MainPage.xaml, quindi copiare il seguente codice nell'elemento **Grid**:
			
		<Grid Margin="120, 58, 120, 80" >
            <Grid.RowDefinitions>
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
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. Nel progetto creare una nuova classe denominata **Notifications**, aggiungere il modificatore **public** alla definizione della classe e quindi aggiungere le seguenti istruzioni **using** al nuovo file di codice:

		using Windows.Networking.PushNotifications;
		using Microsoft.WindowsAzure.Messaging;
		using Windows.Storage;

3. Copiare il seguente codice nella nuova classe **Notifications**:

		private NotificationHub hub;

        public Notifications()
        {
            hub = new NotificationHub("<hub name>", "<connection string with listen access>");
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            await SubscribeToCategories(categories);
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await hub.RegisterNativeAsync(channel.Uri, categories);
        }

    Questa classe usa l'archiviazione locale per archiviare le categorie di notizie che il dispositivo deve ricevere. Contiene inoltre i metodi per effettuare la registrazione per queste categorie.

4. Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with listen access>` con il nome dell'hub di notifica e la stringa di connessione per  *DefaultListenSharedAccessSignature* ottenuta in precedenza.

	> [AZURE.NOTE] Poiché le credenziali che sono distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso Listen con l'app client. L'accesso Listen consente all'app di registrarsi per le notifiche ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completo viene usata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.

4. Nel file di progetto App.xaml.cs aggiungere la seguente proprietà alla classe **App**:

		public Notifications notifications = new Notifications();

	Questa proprietà viene usata per creare un'istanza **Notifications** e accedervi.

5. Aggiungere quindi la seguente riga nel file MainPage.xaml.cs:

		using Windows.UI.Popups;

6. Aggiungere quindi il seguente metodo nel file di progetto MainPage.xaml.cs:

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
	
	Questo metodo crea un elenco di categorie e usa la classe **Notifications** per archiviare l'elenco nell'archiviazione locale e registrare i tag corrispondenti nell'hub di notifica. Quando le categorie vengono modificate, la registrazione viene ricreata con le nuove categorie.

L'app può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie. 

##<a name="register"></a>Registrarsi per le notifiche

Questa procedura consente di effettuare la registrazione con l'hub di notifica all'avvio usando le categorie archiviate nella risorsa di archiviazione locale. 

> [AZURE.NOTE] Poiché l'URI di canale assegnato dal Servizio di notifica Windows può cambiare in qualsiasi momento, è necessario ripetere la registrazione per le notifiche di frequente per evitare errori di notifica. In questo esempio viene effettuata la registrazione per le notifiche a ogni avvio dell'app. Per le app che vengono eseguite con una frequenza maggiore di una volta al giorno, è possibile ignorare la registrazione per conservare la larghezza di banda qualora sia trascorso meno di un giorno dalla registrazione precedente.

1. Aggiungere il seguente codice alla classe **Notifications**:

		public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

	Verranno restituite le categorie definite nella classe.

1. Aprire il file App.xaml.cs file e aggiungere il modificatore **async** al metodo **OnLaunched**.

2. Nel metodo **OnLaunched** individuare e sostituire la chiamata esistente al metodo **InitNotificationsAsync** con la seguente riga di codice:

		await notifications.SubscribeToCategories(notifications.RetrieveCategories());

	In questo modo, ogni volta che l'app viene avviata vengono recuperate le categorie dall'archiviazione locale e viene richiesta una registrazione per queste categorie. Il metodo **InitNotificationsAsync** è stato creato nell'ambito dell'esercitazione [Introduzione ad Hub di notifica], ma non è necessario in questo argomento.

3. Nel file di progetto MainPage.xaml.cs aggiungere il seguente codice nel metodo  *OnNavigatedTo*:

		var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;

	La pagina principale viene aggiornata in base allo stato delle categorie salvate in precedenza. 

Ora l'app è completa e può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie. Verrà ora definito un back-end per l'invio delle notifiche delle categorie all'app.

<h2><a name="send"></a>Inviare notifiche dal back-end</h2>

[AZURE.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Eseguire l'app e generare notifiche

1. In Visual Studio premere F5 per compilare e avviare l'app.

	![][1] 

	Si noti che l'interfaccia utente dell'app fornisce un set di interruttori che consentono di scegliere le categorie per le quali effettuare la sottoscrizione. 

2. Abilitare uno o più interruttori di categorie e quindi fare clic su **Sottoscrivi**.

	L'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica. Le categorie registrate vengono restituite e visualizzate in una finestra di dialogo.

	![][19]

4. Inviare una nuova notifica dal back-end in uno dei seguenti modi:

	+ **App console:** avviare l'app console.

	+ **Java/PHP:** eseguire l'app o lo script.

	Le notifiche per le categorie selezionate vengono visualizzate come notifiche di tipo avviso popup.

	![][14]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione si è appreso a trasmettere le ultime novità per categoria. Per informazioni su altri scenari avanzati di Hub di notifica, provare a completare le seguenti esercitazioni:

+ [Uso di Hub di notifica per la trasmissione di notizie localizzate]

	Informazioni su come espandere l'app relativa alle ultime novità per abilitare l'invio di notifiche localizzate. 

+ [Uso di Hub di notifica per inviare notifiche agli utenti]

	Informazioni su come eseguire il push di notifiche a utenti autenticati specifici. Si tratta di un'ottima soluzione per inviare le notifiche solo a determinati utenti.


<!-- Anchors. -->
[Aggiungere la selezione delle categorie all'app]: #adding-categories
[Registrarsi per le notifiche]: #register
[Inviare notifiche dal back-end]: #send
[Eseguire l'app e generare notifiche]: #test-app
[Passaggi successivi]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /it-it/manage/services/notification-hubs/getting-started-windows-dotnet/
[Uso di Hub di notifica per la trasmissione di notizie localizzate]: /it-it/manage/services/notification-hubs/breaking-news-localized-dotnet/ 
[Uso di Hub di notifica per inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users
[Servizio mobile]: /it-it/develop/mobile/tutorials/get-started/
[Informazioni aggiuntive su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedure di Hub di notifica per Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Oggetto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591






<!--HONumber=45--> 
